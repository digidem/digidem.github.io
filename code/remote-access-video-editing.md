---
layout: code
type: module
title:  "Remote Access: Video Editing"
date:   2016-03-17 20:43:00 -0700
categories: ['']
featured: false
description: "A discussion of video editing workflow."
---

### Current video editing workflow

Right now the monitor coordinator (MC) uses iMovie for video editing. iMovie has proved very easy to learn and use, and we have not found such a simple alternative that runs on Windows. This has made a Mac a requirement for the monitoring work, which adds an expense, and few people in Peru know how to use a Mac, although it is much simpler for the Achuar to learn.

Monitors are gathering a lot of video, sometimes more video than photos. In the current workflow the MC downloads the video from the SD card, and then will trim and combine a few clips. These might include a pan of the spill, a close up, and a piece to camera by the monitor. This could easily be improved with training.

The video editing requirements are actually pretty simple. We just need to edit the video down to 3-5 minute pieces that can go on Youtube. We could help with story-boards (see the Small World News / Guardian Project app that guides people through this).

### HTML5 Video Editor

HTML5 technology has improved enough that a video editor _should_ be possible just running in the browser, particularly for our simple editing needs: select some clips, select the best bits and trim, and edit them together in one sequence. This would remove most hardware requirements and not require any software installation - it would just run in the browser on a tablet or a desktop. It would not work well as a web service given the slow internet, but could work as a local service.

I discovered this tool, [HTML5-videoEditor](https://github.com/casatt/html5-videoEditor) which is a prototype/proof of concept based on a node.js backend and a frontend built on Backbone and JQueryUI. Project creation, asset upload, and initial editing works, but streaming of video was not working on my machine. I haven't yet found other projects that do something similar.

[Node installs fine](http://www.raspberrypi.org/phpBB3/viewtopic.php?f=34&t=18775) on a Raspberry Pi. FFmpeg also installs and can transcode at 3-5 fps. The Pi has a fast GPU that supports hardware encoding, but [ffmpeg does not currently support that](http://www.raspberrypi.org/phpBB3/viewtopic.php?f=31&t=17500).

### Video Workflow Proposal

1. Install customized HTML5-videoEditor on top of nodejs and ffmpeg on the Raspberry Pi.
2. User connects SD card to Pi, automatically downloads video into asset folder for HTML5-videoEditor, transcoding if needed.
3. User connects to HTML5-videoEditor over the local network from a laptop or tablet. Can browse videos, add attribute information, and edit short piece.
4. Pi encodes edited video, places into "pending upload" folder.
5. Rsync uploads video whenever a connection is detected. Support for partial uploads.




======================

See it on [GitHub](https://github.com/digidem/RemoteAccess/wiki/Video-Editing)