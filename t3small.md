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
## Growing Your Disk Space
Fortunately, you can increase the size of your EC2 disk dynamically without creating a new instance.  
You can increase your disk from the default of 10GBytes to 30GBytes while still staying in the free tier on AWS.  
But you should still make sure everything is backed up to github in case something goes wrong.  
To do this, follow these steps:

1. Take a look at the current size of your partitions with "lsblk". You should see that xvda1 has 9.9G of space.
```
> sudo lsblk
NAME     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0      7:0    0 55.6M  1 loop /snap/core18/2538
loop1      7:1    0 25.1M  1 loop /snap/amazon-ssm-agent/5656
loop2      7:2    0   47M  1 loop /snap/snapd/16292
loop3      7:3    0   48M  1 loop /snap/snapd/16778
loop4      7:4    0 55.6M  1 loop /snap/core18/2566
xvda     202:0    0   10G  0 disk
├─xvda1  202:1    0  9.9G  0 part /
├─xvda14 202:14   0    4M  0 part
└─xvda15 202:15   0  106M  0 part /boot/efi
```
2. Go to your AWS console, select the EC2 dashboard.  Select Volumes on the left hand panel under "Elastic Block Store".

![](/images/volumes.png)

3. You should only have one EBS volume if you are running one instance.  Select this volume.

![](/images/yourvolume.png)

4. Select the modify button

![](/images/modify.png)

5. Change the size to 30 and click the modify button.  You should see that your volume size is now 30GB.  You can refresh your browser window to see when the change is complete.

![](/images/changeto30.png)

6. Now you need to update your Linux Ubuntu view of the partition size with "growpart".  
```
> sudo growpart /dev/xvda 1
CHANGED: partition=1 start=227328 old: size=20744159 end=20971487 new: size=62687199,end=62914527
```
You should be able to see that the disk partition is now 30GB
```
> sudo lsblk
NAME     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0      7:0    0 55.6M  1 loop /snap/core18/2538
loop1      7:1    0 25.1M  1 loop /snap/amazon-ssm-agen
loop2      7:2    0   47M  1 loop /snap/snapd/16292
loop3      7:3    0   48M  1 loop /snap/snapd/16778
loop4      7:4    0 55.6M  1 loop /snap/core18/2566
xvda     202:0    0   30G  0 disk
├─xvda1  202:1    0 29.9G  0 part /
├─xvda14 202:14   0    4M  0 part
└─xvda15 202:15   0  106M  0 part /boot/efi
```

7. Use resize2fs to increase the size that Ubuntu sees
```
> sudo resize2fs /dev/xvda1
resize2fs 1.44.1 (24-Mar-2018)
Filesystem at /dev/xvda1 is mounted on /; on-line resizing required
old_desc_blocks = 2, new_desc_blocks = 4
The filesystem on /dev/xvda1 is now 7835899 (4k) blocks long.
```

8. Congratulations! Now you should be able to see the space with df
![](/images/bigger.png)
