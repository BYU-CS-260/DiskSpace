# DiskSpace for t3.small

When you created your Cloud9 EC2 instance you were given 10GB of disk storage for your files.  On my instance the operating system took about 6GB of space leaving me with around 3.6GB of space for development.  This repo will show you how to reclaim some of your disk space or to increase your disk space so that you will be able to complete all of your projects.

## Seeing where your disk space is used

You can see how much disk space is being used with the command
``` 
df -h
```
You will be able to see the size of each of your disk partitions and what percentage is avaialble.  
```
> df -h
Filesystem       Size  Used Avail Use% Mounted on
udev             959M     0  959M   0% /dev
tmpfs            195M  824K  194M   1% /run
/dev/nvme0n1p1   9.6G  5.6G  4.0G  59% /
tmpfs            973M     0  973M   0% /dev/shm
tmpfs            5.0M     0  5.0M   0% /run/lock
tmpfs            973M     0  973M   0% /sys/fs/cgroup
/dev/loop1        25M   25M     0 100% /snap/amazon-ssm-agent/6312
/dev/loop0        56M   56M     0 100% /snap/core18/2566
/dev/loop2        48M   48M     0 100% /snap/snapd/17336
/dev/nvme0n1p15  105M  4.4M  100M   5% /boot/efi
tmpfs            195M     0  195M   0% /run/user/1000
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
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0          7:0    0 55.6M  1 loop /snap/core18/2566
loop1          7:1    0 24.4M  1 loop /snap/amazon-ssm-agent/6312
loop2          7:2    0   48M  1 loop /snap/snapd/17336
nvme0n1      259:0    0   10G  0 disk 
├─nvme0n1p1  259:1    0  9.9G  0 part /
├─nvme0n1p14 259:2    0    4M  0 part 
└─nvme0n1p15 259:3    0  106M  0 part /boot/efi
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
> sudo growpart /dev/nvme0n1 1
CHANGED: partition=1 start=227328 old: size=20744159 end=20971487 new: size=62687199,end=62914527
```
You should be able to see that the disk partition is now 30GB
```
> sudo lsblk
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0          7:0    0 55.6M  1 loop /snap/core18/2566
loop1          7:1    0 24.4M  1 loop /snap/amazon-ssm-agent/6312
loop2          7:2    0   48M  1 loop /snap/snapd/17336
nvme0n1      259:0    0   30G  0 disk 
├─nvme0n1p1  259:1    0 29.9G  0 part /
├─nvme0n1p14 259:2    0    4M  0 part 
└─nvme0n1p15 259:3    0  106M  0 part /boot/efi
```

7. Use resize2fs to increase the size that Ubuntu sees
```
> sudo resize2fs /dev/nvme0n1p1
resize2fs 1.44.1 (24-Mar-2018)
Filesystem at /dev/nvme0n1p1 is mounted on /; on-line resizing required
old_desc_blocks = 2, new_desc_blocks = 4
The filesystem on /dev/nvme0n1p1 is now 7835899 (4k) blocks long.
```

8. Congratulations! Now you should be able to see the space with df
![](/images/bigger.png)
