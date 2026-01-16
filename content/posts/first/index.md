---
title: "Arch Linux: system maintenance"
date: 2023-01-30T11:36:25+01:00
draft: false
---

## 
Having a Linux machine is like having a baby. I personally am not a father but the point is the same: they need love, kind of. I have been using Linux since 2014, my first distro was Ubuntu and instantaneosly fell in love with the terminal. Years passed by and after trying several distros i landed
on Arch Linux.as many out there.
The following is my daily maintenance of my Arch Linux system.

## Updating the system
To update your Arch Linux system open your terminal and digit 
```bash
sudo pacman -Syu
```
## Logfiles
Log files store all the process that take place in your machine. They can be found in /var/log.
To stream errors in log files we will use the journalctl command that manipulates the data collected by journald daemon.
```bash
sudo journalctl -p 3 -xb
```
- **-p** : priorities, 3 stands for 'error'.
- **-x**: add explanatory messages if avaiable
- **-b**: boot. No entries means last booted system

Size of journal
```bash
du -sh /var/log/journal
```
To clean journal entries by time
```bash
sudo journalctl --vacuum-time=3weeks
```

If you want to automatize the process i share a simple script i wrote time ago
```bash
#! /bin/bash

journalpath='/var/log/journal/'
action=$(du -sh $journalpath | awk '{print $1}' | cut -d M -f1)

if [ $action -gt 500 ]; then
  journal --vacumm-time=2weeks
else
  echo "Space under 500M"
fi
```

You can set the size limit to the system journal by editing the file /etc/systemd/journald.conf.
Search for the voice #SystemMaxUse=, uncomment it and set a size limit
```bash
SystemMaxUse=200M 
```

## Pacman cache
Pacman is the packet manager of Arch Linux and derived distros. Pacman packages can be found in /var/cache/pacman/pkg. Since pacman does not remove old packages we need a script called **paccache**.
To perform a dryrun
```bash
sudo paccache -dr
```
to remove packages
```bash
sudo paccache -r
```
The above command keeps all the three recent versions of your installed packages and delete the older ones. If you want to keep the last two versions you can type
```bash
sudo paccache -rk2
```

## Cache directory
You can print the size of your .cache directory using the following command
```bash
du -sh ~/.cache
```

To find elements by time and delete them you can run
```bash
find ~/.cache -type f -atime +<number of days> -delete
```

Usually it is safe to free .cache space. Just be careful about the softwares installed.

## Bleachbit
Bleachbit is a free and open source privacy oriented tool used for cleaning disks. 
Install bleachbit
```bash
sudo pacman -S bleachbit
```

## In conclusion
These are simple recommendations to maintain your Arch distro. I hope people can find this guide useful and if you would like to share your thoughts feel free to contact me.
You can find more about Arch maintenance here: https://wiki.archlinux.org/title/system_maintenance.
See you soon! 
