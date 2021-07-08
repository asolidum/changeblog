---
title: "Securing Changeblog Images"
subtitle: "using git hooks"
image: "../securing_changeblog.gif"
alt: "Exiftool in action"
description: "As I add more content to this site, I want to ensure the images are free of EXIF geolocation data. Why not automate this process with git hooks?"
date: 2021-05-25T14:22:18-07:00
draft: false
---
## The issue

As I add more content to this site, I want to ensure the images are free of [EXIF][exif.link] geolocation data. I prefer maintaining some level of privacy since a decent portion of these images are taken at my work/home.

I could turn off geotagging of my images during capture, but I ultimately like retaining the GPS data of where those moments occurred.

Luckily an open-source application, [exiftool][exiftool.link],  provides an easy way to remove geolocation data by issuing the following command.
```bash
exiftool -geotag= <filename>
```
This manual step could become tedious/easily overlooked, especially if it must be applied to every image we want to be geotag free. Why not automate this process with git hooks?

## A possible solution
Unfortunately, my research yielded no concrete examples/code of developers using git hooks intent on removing geotag data from images.  Inspired by this [example](example.link) meant for removing trailing whitespace, I came up with the following `pre-commit` git hook.

```bash
#!/bin/bash

echo "Running pre-commit"
# Get added/modified file list
for FILE in `exec git diff --cached --name-only --diff-filter=AM` ; do
    EXT=$(echo "${FILE##*.}" | tr "[:lower:]" "[:upper:]")
    # Process JPG & PNG files. Can add additional filetypes below
    if [ "$EXT" = "JPG" ] || [ "$EXT" = "PNG" ]; then
        echo "Attempting to remove GPS data from $FILE"
        exiftool -geotag= -overwrite_original $FILE
        git add $FILE
    fi
done
```
The above script removes geotag info on any added/modified pre-committed files.  Note, if you're interested in trying this out yourself, make sure you have [exiftool][exiftool.link] installed locally on your machine.

Occasionally we'll want to retain the image geolocation data and can temporarily achieve this by using the `--no-verify` option when performing a `git commit`.

## Alternative solutions/enhancements

* Add a `pre-receive` git hook on the remote repository so geotag removal would be enforced for all developers.  I would have to figure out how to install [exiftool][exiftool.link] on the remote server and ideally would want an easy mechanism to turn it off when we want to preserve the image geolocation data. This seems like a lot of effort with little ROI considering I'll likely be the only developer working on this blog.

* Create temporary debug `<figcaption>` elements that display any existing geolocation data while running a local/development blog instance. Thus I would receive visual feedback of any undesirable geotags still present while developing the site.

* Update the `pre-commit` git hook to reduce the image GPS resolution down to the city/neighborhood level instead of complete removal.  This allows us to continue including geolocation data while providing an acceptable level of privacy.

[exif.link]: https://en.wikipedia.org/wiki/Exif
[exiftool.link]: https://exiftool.org/
[example.link]: https://snipplr.com/view/28523/git-precommit-hook-to-fix-trailing-whitespace
