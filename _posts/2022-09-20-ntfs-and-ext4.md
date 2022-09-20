---
title: "Large File Transfer between Windows (NTFS) and Linux (Ext4, etc)" 
layout: post
post-image: "/assets/images/ntfs_ext4.png"
description: When the file size becomes extremely large, your experience becomes less reliable.
tags:
- WSL
- NTFS
- ext4
- Filesystem
---

Currently I am working on reseaches related to pathology dataset, and as you may know these images are usually extremely large. For example, a .tif whole slide image from a Phillips machine can cost me about 2.3G disk space and remember, remember that it is just ONE SINGLE image. For my pathology dataset, there are about 2200 such images that sum up to 5.3T. 

The doctors like to use Windows as their operating system. It is totally fine, and in fact I personally use Windows a lot as well. But unfortuately our deep learning server runs Linux, so we cannot just simply connect the dataset drive to the server and start the research immediately. In the past, when the dataset size is reasonable (~100G), I can usually count on scp to upload the files. But this time, with the so-big whole slide images, scp just refuses to work. 

I have to find another way.

### scp and how it fails

The doctors give me two NTFS drives that contains their 4-year's hard-work of data collecting and I have to upload these data to a ext4 Linux drive. The very first idea that comes to me is by using scp. scp works in such way:

`scp -r <source_folder_path> <user>@<ip_address>:<destination_folder_path>`

Usually it works perfectly but this time I find it often disconnects halfway due to the heavy tranferring burden. Also, the transferring speed is not so good since it can only maintains about 12MB/s speed. I have also tried to use FileZilla but it also fails frenquently. Specifically, for a scheduled task with 30 whole slide images, 17 of them failed.

### Can we do a real physical copy?

I am tired of uploading the dataset by network connection. I want to do this in a more primitive but effective way. I want to directly copy the files through my personally computer.

But is that even possible? I mean, when we connect a Linux drive to a Windows PC, the PC is just 'impossible' to recognize the drive right? 

*No, it is possible now, with the support of WSL2.** (If you are using Windows with version higher that Build.22000)

To use WSL2, uninstall WSL first and then open cmd to run

`wsl --install -D Ubuntu`

Then windows will download the necessary components and install Ubuntu as a Subsystem. Remeber to enable HyperV or the installation may be failed. After that, you can connect your Linux drive to your PC and mount it to a path under this Linux Subsystem. To do so, simply run the command as Administrator:

`wsl --mount <your_physical_drive> --partition 1`

If your disk has more than one partition then you have to change the parameter accordingly. The disk will be mounted to /mnt/wsl/<your_drive>p1 by default. You can also find it in File Explorer, meaning that you can now directly copy files in a NTFS disk to this Linux drive. 

### However, it is still not that easy

Though this is the most stable way of copying files and it also has a great stability, there are still some restrictions. It seems that there is a buffer area for this cross-filesystem copying operation and you can only copy 255G files at a time. So, I have to split my 5.3T pathology dataset and perform the copying operation several times. Also, there is also sudden crash to the copying process, but only with an extremely low chance. When this happens, there will be no alert for you so you won't know the files are actually broken. You'll have to check the file size by yourself and reconduct the process if needed.

Generally speaking it is really amazing to see Microsoft giving full support for Linux Filesystem on Windows. I believe it is the right decision that they embrace the open-source community and make contributions that can help everyone. 