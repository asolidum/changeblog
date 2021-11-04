---
title: "Daily Fla.sh (inspired by 1 Second Everyday)"
subtitle: "scripted with Bash and FFMpeg"
image: "../iterm_bash_ffmpeg_dailyflash_view.jpg"
alt: "Iterm bash shell with daily flash command line"
description: "Daily Fla.sh is a video montage command-line tool heavily inspired by the 1 Second Everyday app. It's primarily powered with FFMpeg and scripted in bash shell"
date: 2021-10-29T20:28:57-07:00
category: [ "projects" ]
tag: ["bash", "ffmpeg", "photography", "journal" ]
draft: false
---
# What is Daily Fla.sh?
[Daily Fla.sh][flash.link] is a video montage command-line tool heavily inspired by the [1 Second Everyday][one_second.link] app. It's primarily powered with [FFMpeg][ffmpeg.link] and scripted in bash shell.

## So what is 1 Second Everyday?
I first learned about [1 Second Everyday][one_second.link] after watching [Cesar Kuriyama's Ted talk][ted.link]. If you've never heard of One Second Everyday, it's basically the concept of creating daily one-second video compilations. Kuriyama hoped that this process would help him remember his daily experiences. He would further develop this idea into a [Kickstarter campaign][kick.link] to help fund app development.

[flash.link]: https://gitlab.com/a1s0/dailyfla.sh
[one_second.link]: https://en.wikipedia.org/wiki/1_Second_Everyday
[ted.link]: https://www.ted.com/talks/cesar_kuriyama_one_second_every_day
[kick.link]: https://www.kickstarter.com/projects/cesarkuriyama/1-second-everyday-app

## "There's an app for that..."
When the app ([iOS][ios_app.link]/[Android][and_app.link]) was released, I enjoyed using it, but I needed a little more flexibility and wanted to see if I could simplify the process with a command-line tool removing the need for a GUI.

[ios_app.link]: https://itunes.apple.com/us/app/1-second-everyday/id587823548
[and_app.link]: https://play.google.com/store/apps/details?id=co.onese.android

# What we need

Some video feature requirements for our tool are:
1) Extracting `x` seconds from a specific starting point
2) Transcoding to a common output format
3) Adding titles/text
4) Concatenating videos into one final video

I've used a command-line tool called [`FFMpeg`][ffmpeg.link], a comprehensive video utility, which should serve our needs. I'm pretty sure it can accomplish requirements 1 and 2. After further research, I learned that it fulfills all our remaining requirements as well. It looks like we have everything we need! :relieved:

So my solution should be a relatively simple bash script. :sweat_smile:

# Scripting the flow

The high-level program flow is:
1) Creating a title screen (currently set to display `Month Year`)
2) Iterating over a list of video files and extracting `x` seconds from specified starting points
3) Transcoding those extracted videos into a standard format and adding text indicating whichever daily video we are viewing (currently set to display `Day DD`)
4) Concatenating the title screen video and extracted videos into one final video

I also wanted to allow for easy customization. We included the following [adjustable parameters][adjustparam.link].

```bash
## Duration of each daily video (in seconds)
duration="00:00:01"
## Duration of the title screen (in seconds)
title_duration="3"
## Resolution of the final videos (in pixels)
resolution="3840x2160"
transcode_settings="-vcodec libx264 -c:a aac -b:a 320k -filter:v fps=25 -s ${resolution}"
font_size=128
font_settings="/System/Library/Fonts/Keyboard.ttf:fontcolor=white"
title_text_settings="drawtext=fontfile=${font_settings}:fontsize=$((font_size*2)):x=(w-text_w)/2:y=(h-text_h)/2"
daily_text_settings="drawtext=fontfile=${font_settings}:fontsize=${font_size}:box=1:boxcolor=black@0.5:boxborderw=5:x=(w*3/4):y=(h*3/4)"
```
We've set our daily text font size to 128 and hardcoded the title text twice as much.  The title text will also appear centered on the screen, while the daily text will be present on the bottom right.

[adjustparam.link]: https://gitlab.com/a1s0/dailyfla.sh/-/blob/master/dailyfla.sh#L3

## AI Pair Programming Anyone?
The biggest function in the script is [`get_num_days_in_month()`][func.link].  This function is responsible for determining how many days are in the specified month and year. I recently was approved for [GitHub Copilot][copilot.link], and this was my first program to benefit from having an "AI pair programmer". That function (with some additions from me) is a result of using that technology.  Stay tuned for a future post where I'll go into more details about my experience.

[func.link]: https://gitlab.com/a1s0/dailyfla.sh/-/blob/master/dailyfla.sh#L83
[copilot.link]: https://copilot.github.com

## Unexpected "features"
### Inconsistent Text
My early test runs generated output video as expected except for occasional inconsistent daily text output.  You can see that Day 10's text is slightly larger than the other days in the images below.
{{< single_imgrow images="day_09.png,day_10.png,day_11.png" >}}
I wasn't sure of the root cause until I realized the input videos (sometimes originating from different video sources) had incongruous video resolutions.  Our original script included the following line to cut `${duration}`-sized video from the file, transcode it into our specified output format, and finally add the "`Day DD`" daily text.

```bash
 ffmpeg -i "${filename[$i]}" -ss ${timestamp[$i]} -t ${duration} -vf "${daily_text_settings}:text='Day ${day_str}'" ${transcode_settings} ./tmp/${outfile} &> /dev/null
```
My solution was to split the command into cutting and transcoding the video to our desired output resolution and then adding the daily titles to those video files that now possess the same resolution.
```bash
ffmpeg -i "${filename[$i]}" -ss ${timestamp[$i]} -t ${duration} ${transcode_settings} ./tmp/tmp_${outfile}
ffmpeg -i ./tmp/tmp_${outfile} -vf "${daily_text_settings}:text='Day ${day_str}'" ./tmp/${outfile}
```
This idea appears to have fixed the bug but at the expense of degraded performance since we have to invoke [`FFMpeg`][ffmpeg.link] twice as often now.

### Incorrect Final Video
Early results of the final output video yielded some missing days and out-of-sync audio.  While we made sure our video resolutions were consistent, we should have also included the frame rate.  Another thing we should have ensured was audio consistency.  Our included laptop screen recordings were missing audio tracks, so we added the following function to fix this issue.
```bash
function add_missing_audio_stream() {
    local outfile=$1
    local daystr=$2
    num_streams=$(ffprobe -loglevel error -show_entries stream=codec_type -of csv=p=0 ${outfile} | wc -l)
    if [ ${num_streams} == 1 ]; then
        echo "Adding missing audio stream to day ${day_str}"
        ffmpeg -f lavfi -i anullsrc=channel_layout=stereo:sample_rate=48000 -i ${outfile} -c:v copy -c:a aac -shortest -y ./tmp/tmp_audio.mp4 &> /dev/null
        mv ./tmp/tmp_audio.mp4 ${outfile}
    fi
} 
```

# Fla.sh Forward
Some enhancements would be inputting the filenames and timestamps through file I/O redirection or as a separate input file keeping the data and code separate.  Another optimization related to the diverse video resolution performance issue is customizing the daily text font size based on the particular input video resolution to free us from calling [`FFMpeg`][ffmpeg.link] twice.

We could also look into parallelizing the creation of each of the daily second videos.  Those are independent operations until we need to concatenate them into the final video.

Check out the git repo [here](https://gitlab.com/a1s0/dailyfla.sh)

[ffmpeg.link]: https://www.ffmpeg.org