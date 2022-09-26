# DiskSpace

When you created your Cloud9 EC2 instance you were given 10GB of disk storage for your files.  On my instance the operating system took about 6GB of space leaving me with around 3.6GB of space for development.  This repo will show you how to reclaim some of your disk space or to increase your disk space so that you will be able to complete all of your projects.

## Seeing where your disk space is used

You can see how much disk space is being used with the command
``` 
df -h
```
You will be able to see the size of each of your disk partitions and what percentage is avaialble.  
```
> df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            473M     0  473M   0% /dev
tmpfs            98M  840K   97M   1% /run
/dev/xvda1      9.6G  6.0G  3.6G  63% /
tmpfs           488M     0  488M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           488M     0  488M   0% /sys/fs/cgroup
/dev/loop0       56M   56M     0 100% /snap/core18/2538
/dev/loop1       26M   26M     0 100% /snap/amazon-ssm-agent/5656
/dev/loop2       47M   47M     0 100% /snap/snapd/16292
/dev/xvda15     105M  4.4M  100M   5% /boot/efi
tmpfs            98M     0   98M   0% /run/user/1000
/dev/loop3       48M   48M     0 100% /snap/snapd/16778
/dev/loop4       56M   56M     0 100% /snap/core18/2566
```

You can also see how much space is used by each partition by using the "du" Disk Usage command. The following command will show you how much space is being used by web pages.  Remember that your public_html is a symbolic link to /usr/share/caddy where your web pages have disk space allocated.  So you can see how much space each project is using with the following command.
```
du -sh /usr/share/caddy/*
```
You should see the amount of disk space each file and directory uses.  You may want to create a git repository for all of your files during the semester and then delete them after they have been checked in and graded.
```
> du -sh /usr/share/caddy/*                                                                                    
4.0K    /usr/share/caddy/index.html
4.0K    /usr/share/caddy/javascript
```
