---
title: "Fixing System Errors Before Daemon Services Startup" 
layout: post
post-image: "/assets/images/daemon_error.jpg"
description: When the daemon service itself runs into problems, that is where you need these tips here.
tags:
- Linux
- Grub
- Daemon
---

### The Problem I Encountered

So I have updated the Linux Kernel of a server of our lab yesterday, and hence many packages need to be upgraded to the compatible version as well. There are a few dependency problems during the package updating process, but with the help of package managers, most of them can be easily fixed. You can also try installing aptitude for your package management as aptitude usually can help in building a more tidy package environment. But in this case, I just stick to the original dpkg and apt tools, for which some of my most commonly used command lines (we use Ubuntu on our service so the package manager is apt and dpkg) are:

- apt -f install
- dpkg --configure xxxx (or simply -a)
- apt update && apt upgrade
- apt autoremove

When some complicated dependency conflicts appear, I will first try to remove it and then run 'apt -f install' to fix the broken installations. But do not forget to run 'dpkg --configure -a' to properly configure these installed .debs or otherwise they might not work after the installation. Sometimes there will also be shared library problems during the update, and maybe you will need to dip into the report directory to change the soft link to the file. My problem was encountered this time by changing the shared library of 'libseccomp' with the incompatible Linux kernel. This library is required by the systemd service and the Ubuntu system won't start properly without this system daemon. 

### How to Solve the Daemon Service Issues

As a successful system startup (not only normal, but also upstart, and even recovery mode) usually depends on the proper startup of related daemon services, it can be very tricky to fix the issues of these daemon services themselves as the system won't even start properly to allow you to enter the root shell. In other cases, we can try entering a read-only root shell through the grub panel by selecting booting in recovery mode in the advanced options (and nowadays network services are even made available in this mode). But since the recovery mode also relies on certain daemon services, it may also fail to start in our case (reporting kernel panic). 

Entering a whatever shell is now the only redemption for our previous mistake, and luckily we can realize it by editing the grub script by appending 'init=/bin/sh' to the back of the line starting with 'linux \t /....'. Just enter the grub panel and scroll down to the option that says 'starting Ubuntu with the NEWEST kernel version (recovery)', and then press 'e' to edit the script as aforementioned. By doing so, we can skip the daemon service startup and directly jump into the root shell. You can also switch to /bin/bash for a more friendly interface, but anyway we are only here to fix the errors and the basic shell can also do the job.

Then, to make the system writable (the shell is read-only whether you start it by grub recovery mode or by editing the script), we can run 'mount -o remount,rw /' to remount the root directory into rw mode. You can find your daemon service file now and make changes to them accordingly. For my issue, it results from the fact that the libseccomp2 library I installed during the update is too new for my kernel version, so I just removed the library files in /lib/Linux_x86_64..../libseccomp* and turned to start the systemd service with the old library files in the /usr/lib/ directory. Note that the reboot command won't work in this shell as the system daemon services are not initialized yet, so you will have to manually shut down the server and restart it.  

