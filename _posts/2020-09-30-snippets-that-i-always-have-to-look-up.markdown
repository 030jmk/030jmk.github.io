---
layout: post
title:  "Snippets that I always have to look up"
date:   2020-09-30 12:00:00 +0200
categories: [bots and scripts]
---

### download a playlist as mp3 with youtube-dl 
```
youtube-dl --extract-audio --audio-format mp3 -o "%(title)s.%(ext)s" <url to playlist>
```
source: [Oli](https://askubuntu.com/questions/564567/how-to-download-playlist-to-mp3-format-with-youtube-dl)

### ffmpeg conversion to H.265
```
ffmpeg -i 'INPUT FILE NAME AND LOCATION ' -vcodec libx265 -crf
28 'OUTPUT FILE NAME AND LOCATION '
```
