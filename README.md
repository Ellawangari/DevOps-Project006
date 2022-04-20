# DevOps-Project006

****Project Implementation****

The project tasks were to prepare two storage infrastructure on Linux servers and implement a basic web solution using WordPress. 
  
****Key Takeaways****
-  Gained practical experience on working with disks, partitions and volumes in Linux.
-  Learned  WordPress installation and connectivity to a remote MYSQL Database.
-  Was able to do disk management on RHEL( Red Hat Enterprise Linux OS) - a Linux Distro 

****STEPS****
**Step 1: Configuring two RHEL EC2 AWS instances one named web-server and the other database-server**
- ![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/1.PNG)

****PREPARATION OF THE WEB SERVER****
- Created 3 10GIB volumes and attached them to the server
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/2.PNG)

- Connected my web-server on Mobaxterm and ran the command `lsblk` to confirm the disks were added and attached succesfully.
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/4.PNG) 

- `df -h` to check all the mounts and free space.
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/5.PNG)

- Created partitions on each of the 3 disks using the gdisk utility `sudo gdisk /dev/xvdf`
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/8.PNG)

-Installed lvm package using `sudo yum install lvm2` and ran the command `sudo lvmdiskscan` to check for available partitions
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/9.PNG)
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/11.PNG)

- Created physical volumes to be used by LVM for each of the 3 disks and ran `sudo pvs` to check that the volumes were running succesfully.
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/12.PNG)
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/13.PNG)

- Created a volume group called webdata-vg and added the 3 physical volumes to it and ran the command `sudo vgs` to that the volume group has been created succesfully.
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/14.PNG)



