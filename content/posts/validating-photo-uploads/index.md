---
title: "Validating Photos Uploads"
subtitle: "with the Google Java API client" 
image: "../google_photos.jpg"
alt: "Google Photos albums page view"
description: "Verito GP (Google Photos) is a validation tool to ensure that all the files in a specific folder successfully upload to your GP account"
date: 2021-09-15T09:31:18-07:00
category: [ "projects" ]
tag: ["google photos", "java" ]
draft: false
---
I'm a big fan of Google Photos (GP) from the ease of sharing photos/videos, ability to run Google-like searches on your collection, automated image placement by subject, non-destructive editing functionality (similar to Adobe Lightroom), etc... The list goes on.

## The problem
My photos/video come from multiple sources like phones, tablets, laptops, screenshots, family/friends, etc.  With [Google's 15GB daily upload limit](https://support.google.com/photos/answer/6220791), I must occasionally split directories (sometimes containing hundreds of files) into multiple partial directory uploads. So I needed an easy way to validate the proper transfer of all those media files to the GP servers...  Thus Verito Google Photos (GP) was born.

## The concept
Verito GP (Google Photos) is a validation tool to ensure that all the files in a specific folder successfully upload to your GP account.  It accomplishes this by getting a local directory listing and comparing it to the list of files already backed up on the GP servers.

## The design
### What language to use?
Originally I wanted to create this program in Golang and execute GP API requests using goroutines (see my example in the Github repo [erg (Evaluator of Reverse Geocoders)](https://github.com/asolidum/erg/blob/master/main.go). Luckily Google provides Java and PHP libraries to communicate with their GP API servers.

The Java API client also supports date filtering.  With this functionality, only photo filenames between the earliest and latest file creation times in our specified folder will be returned.  Since I've been using GP for almost a decade, we want to prevent the GP API from returning every media file name present and free us from waiting hours for Verito GP to complete verification.

Guess the language we're going to use will be Java instead.  Go [here](https://developers.google.com/photos/library/guides/get-started-java) for more details on how to install and configure the Java client library.

### Two classes? Or more?
My initial assessment is Verito GP will be composed of two classes, VeritoFilelist and VeritoAPI.

The VeritoFilelist class will handle file operations such as filtering invalid files and determining the earliest and latest file creation times in the specified directory.  The VeritoAPI class will be responsible for setting up access scopes, obtaining tokens and communicating with the GP server.

Date functionality such as converting between Google and Local date types started cluttering up the VeritoFilelist class causing me to move those methods into the VeritoDate class.  Consequently, this also simplified testing.


## Final Thoughts
One further optimization to consider is creating a local database for storing Google Photos API results.  While this would require us to set up a local datastore, it could drastically reduce API requests and save time.  If Verito GP performance becomes an issue, we should reconsider this solution.

Check out the [git repo](https://gitlab.com/a1s0/verito_gp)
