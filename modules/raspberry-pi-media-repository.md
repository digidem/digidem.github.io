---
layout: module
title:  "Remote Access: Raspberry Pi Media Repository"
date:   2016-03-17 20:43:00 -0700
categories: ['']
featured: false
description: "This is a proposal for building a media repository around a Raspberry Pi."
---

One of the challenges of the monitoring is that it produces large amounts of media: photos, video, and sometimes audio. When SD cards arrive in the office from the monitors they need to be downloaded and backed up. It is best if this media is all stored on an external drive, that makes it easy to transfer the media between computers. This media needs to be uploaded online, but that is a challenge with the slow internet connection.

This is a proposal for building a media repository around a [Raspberry Pi](http://www.raspberrypi.org/faqs).

### What is a Raspberry Pi?

![Raspberry Pi](http://www.raspberrypi.org/wp-content/uploads/2011/07/7513051848_9a6ef2feb8_o.jpeg)

It is a $25-$25 linux-based computer that can run on 4xAA rechargeable batteries. It boots from an SD card and has 2 USB ports, HDMI out and an ethernet port. It is made by a non-profit in the UK. It does not come with a case, and has grown a large hacker following.

## Proposal Overview

Build a Raspberry Pi into a custom case with a USB hub, SD card reader and Li-ion backup battery. Connect a USB harddrive, and when you insert an SD card it downloads the photos & video to the external drive. An LED shows download progress.

The Li-ion acts as a simple UPS backup if power should fail, and would allow the unit to be taken to the field to collect photos and imagery directly from monitors.

For backup you would connect a second drive and backup would happen automatically. An LED shows backup progress.

Whenever it gets an internet connection it will automatically synchronise the photos online, prioritizing the best photos.

### SD card downloads

An LED in the case shows status:

* Green: Downloading
* Flashing Green: Download complete
* Flashing Red: Error - will show if no external drive is attached or if drive is full.
* No light: SD card has not been recognized, remove and try again.

The software would simply download the SD card contents to the hard drive. It should have some basic sync logic built in so that it does not re-download photos that have already been removed from the card.

Should it delete the contents of the card? Or leave that to the camera user?

We could rename the images with an MD5 hash of the file when the download happens. This would ensure that each filename is unique and allows consistency referencing the images as they move around. Is there a way to uniquely fingerprint images similar to MD5 that would allow a unique filename even if the image is re-sized?

If we can fingerprint each SD card, we can configure the software to recognize which SD cards belong to each monitor, allowing us to store images in a folder structure similar to what has already been used: MONITOR_NAME/DOWNLOAD_DATE/ Would sub-folders per download date make sense if we want to check for previously downloaded images?

Should we automatically downsample the images? Create a hi-res and lo-res version? This would speed up upload and reduce drive space (if we deleted the hi-res original) but occasionally for good photos the hi-res version can be useful.

### External drive

If this is a 2.5" portable drive it will not need an external power supply (as long as we have a powered hub connected to the Raspberry Pi in the case - the Pi USB ports only provide 100mA power).

We should have an indicator LED in the case for HD status:

* Green: Connected, all good.
* Red: Drive getting full, needs replaced ASAP.
* Flashing Red: Drive full, cannot download photos.

### Backup

This should be automatic. Whenever a second drive is connected it should Rsync backup between the two drives to make an identical image. Ideally this should be bi-directional sync incase the two drives get muddled up - this way it would not matter which one is the backup and which one is the original. Both drives should be the same size. One would be permanently connected and the other should be taken home.

An LED indicator should show Backup status:

* Green: Backing up.
* Flashing Green: Backup complete, you can remove the drive.
* Red: Backup failed.

### Internet Sync

We can use Rsync to synchronize files to an online service. Rsync is open source, well tested, and works efficiently over a slow connection. It supports partial uploads and resumes. Not many image providers support it so we would probably have to provide our own service. This could be an Amazon EC2 instance running RSync that saves the files to Amazon S3. There is a [commercial service](http://www.s3rsync.com/) that does this but it should not be difficult to implement this ourselves. We would have to pay bandwidth, but it should not be too expensive.

We only need one-directional sync - I don't think there would be any need to download images from online.

There should be some way of prioritizing image uploads. If the user can somehow select the best images, and those are uploaded first. We do not want a couple of large videos blocking the upload pipe.

### Alternative image storage and sync

We could store the image files themselves in a CouchDB database. CouchDB would take care of the syncing, and since we are using CouchDB for the other data this might make sense. We could store attribute data together with the image in the same database. The (major) disadvantage is that CouchDB stores databases as single files, so that backup is more complicated and you cannot simply attach the drive to another computer and see the images.

### Image attributes & image service

The user can connect over the local network to an image service on the Raspberry Pi to browse the images and add attribute data. Key attributes to add would be:

* Event (each monitoring "event" is normally given an event code)
* Location (GPS points entered from forms / GPS download)
* Priority / rating (To select the best photos that will be prioritized for upload)

This data could either be embedded in the photos in a header, or in an XMP sidecar, or it could be stored in a database.

Embedding this data in the photos and reading and searching this information could be slow, unless cached. If there is a pre-existing library to index XMP or header data this could work, but would it also work when the photos are online?

We could use a CouchDB to store image attributes, and potentially a low-res image thumbnail. If it links to a unique image name then the same database could be used online and offline (referring to photos locally or photos in an online image service / Amazon S3).

A simple image server can serve the images locally that can be browsed from a tablet or laptop via an HTML5 app. The image browser should be able to add the attributes described above. The CouchDB database (with image thumbnails/previews) could sync to the tablet so that images can be shown from the device when it is not connected to the Pi.

We can write this image service so that we use the same service online and locally: i.e. running off the Raspberry Pi on the local filesystem, or running online, perhaps off an S3 backend.

### Raspberry Pi case

We would ideally build a sealed case containing:

* Raspberry Pi
* Powered USB hub
* Backup battery
* SD Card reader
* LED indicators

The SD card reader built into the Pi is needed for the operating system; it cannot boot from a USB drive.

The backup battery would act as a simple UPS should the power fail, and would allow you to carry the unit into the field if the monitor coordinator was going to visit the monitors and wanted to download their photos. Something [like this](http://www.bootstrapsolar.com/) would be great, and we could even build in the solar charging circuitry so you could charge from a solar panel.

The powered hub is necessary to connect two hard drives (original and backup) and the SD card reader (the Pi only has two USB ports and they do not provide enough power for an external drive).

Ideally one power supply could power both the Pi, the USB hub and backup battery and everything would be built into one simple case. Ideally the user would not be able to remove the SD card with the operating system - it could get confused with SD cards from the cameras. Could be build a SD card reader into the case?

The case should incorporate several LED indicators as described above: SD card download status; HD 1 status; HD 2 status; Backup process status. We should try and have a consistent indication scheme, something like:

* Green: Something is happening, all is good, just leave it.
* Flashing Green: It's ready, you can remove the HDD / SD card
* Flashing Red: Something bad, you need to do something.
* Red: Warning, but no action.

That way Flashing = Action needed. More thinking welcome.

The case should have clear words describing each LED. If affordable we could have a line of LED lights to show HDD storage / upload and backup progress.

**Cost:** Ideally we could make the entire unit including the Pi (excluding external HDD) for < $100. This would make a super simple unit that we could easily purchase for monitoring programs.

### Video editing

Could we make a simple service that would allow people to make basic video edits? Maybe something already exists? It would be great to remove dependency on iMovie, which is something that restricts us to Macs at this time. We have not yet found a simple video editing service that runs on Windows. It would be great if we could create a web app for video editing, that connected over the local network to the Pi. Or we could use an existing editing app for Android (is there a good one?). The user could select the video he wants to work with and download it from the Pi to his tablet over the local network.

[More details about editing proposal](https://github.com/digidem/RemoteAccess/wiki/Video-Editing)

### Networking

The Pi can just connect via ethernet to the router / internet connection. Normally internet installations in Peru include Wi-Fi so the tablet could just connect to the Pi over that Wi-fi. We could include a Wi-Fi USB with the Pi that would create an ad-hoc network for the tablet to connect to (and share the internet connection). That would make the whole system independent but would (a) increase cost and (b) potentially create confusion if there are two wi-fi networks - one for the office and one created by the Pi. Which one should we use?


======================

See it on [GitHub](https://github.com/digidem/RemoteAccess/wiki/Raspberry-Pi-Media-Repository).

Return to [Modules](/modules).