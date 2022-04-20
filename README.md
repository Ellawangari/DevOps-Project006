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

- Created two logical volumes one for  apps.lv(to store data for the website) and logs.lv(to store data for logs)`sudo lvcreate -n apps-lv -L 14G webdata-vg`
`sudo lvcreate -n logs-lv -L 14G webdata-vg`
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/15.PNG)

- Formatted the logical volumes using ext4 file system using the  mkfs.ext4 `sudo mkfs -t ext4 /dev/webdata-vg/apps-lv` `sudo mkfs -t ext4 /dev/webdata-vg/logs-lv` 
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/17.PNG)

- Created /var/www/html to mount apps-lv `sudo mkdir -p /var/www/html` && `sudo mount /dev/webdata-vg/apps-lv /var/www/html`
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/18.PNG)

- Used the rsync utility to backup the files in the log directory before mounting as it would have deleted all existing data.
`sudo rsync -av /var/log/. /home/recovery/logs/` `sudo mount /dev/webdata-vg/logs-lv /var/log` 
- Restored all the log files to /var/logs directory using the following command  `sudo rsync -av /home/recovery/logs/. /var/log`

- Edited /etc/fstab so mounted volumes would persist if server restarts by formatting it using my own UUID
 ![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/20.PNG)
 
- Tested the configuration and reloaded the daemon  `sudo mount -a`  `sudo systemctl daemon-reload` the used the command  `df -h` to check the specified output.
 ![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/21.PNG)
 

**STEP 2**

***PREPARATION OF THE DATABASE SERVER***
- Created 3 10GIB volumes and attached them to the databaseserver
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/db1.PNG)

- Repeated the steps similar to the web server but instead of having an apps.lv volume for website data i created a db.lv volume to store the database.
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/db8.PNG)



**STEP 3**

***INSTALLATION OF THE WORDPRESS ON THE WEB SERVER***

- Installed wget, Apache and it’s dependencies `sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`
- Started Apache using the following commands `sudo systemctl enable httpd` `sudo systemctl start httpd`
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/23.PNG)

- Installed PHP and the following dependencies and restarted Apache.
  > `sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`
 
  > `sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`
 
  > `sudo yum module list php`
 
  > `sudo yum module reset php`

  > `sudo yum module enable php:remi-7.4`
 
  > `sudo yum install php php-opcache php-gd php-curl php-mysqlnd`

  > `sudo systemctl start php-fpm`
  
  > `sudo systemctl enable php-fpm`
  
  > `setsebool -P httpd_execmem 1`
  
  > `sudo systemctl restart httpd`


- Downloaded wordpress and copied wordpress to var/www/html 
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/25.PNG)
  >  `mkdir wordpress`
   
  > `cd   wordpress`

  >  `sudo wget http://wordpress.org/latest.tar.gz`

  > `sudo tar xzvf latest.tar.gz`
 
  > `sudo rm -rf latest.tar.gz`

  > `cp wordpress/wp-config-sample.php  var/www/html/wp-config.php`

  > `cp -R wordpress /var/www/html/`

- Configured SELinux Policies
 >  `sudo chown -R apache:apache /var/www/html`

  >  ` sudo chcon -t httpd_sys_rw_content_t /var/www/html -R`

  > ` sudo setsebool -P httpd_can_network_connect=1`


**STEP 4**

***INSTALLATION OF  MySQL ON THE DATABASE SEREVER***

- Used the following commands to install `sudo yum update` `sudo yum install mysql-server`

- Checked the status of MYSQL and restarted and enabled the service.
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/27.PNG)

**STEP 5**

***CONFIGURED DB TO WORK WITH WORDPRESS***

- Ran the following commands.
 > `sudo mysql`

 > `CREATE DATABASE wordpress;`

 > `CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';`
  
 > `GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>'; `
  
 > `FLUSH PRIVILEGES;`
  
 > `SHOW DATABASES;`
  
 > `exit`
  
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/28.PNG)
  
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/29.PNG)
  

  **STEP 6**

***CONFIGURED WORDPRESS TO CONNECT TO REMOTE DATABASE***
 
- First i opened MYSQL port 3306 on my DB Server and only allowed my Web Server Private Ip Address to connect to it.
  ![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/30.PNG)
  
- Later i got an error for connection so i went ahead and reconfigured some files by editing the wp-config.php on the web server with details of my database name , database user and password and also added my Database Private Ip address.
  
- Also instead of copying the /var/html as to a wordpress directory as above i recopied it to all the files to /var/html that was why i was able to access WordPress on my browser without the /wordpress after my public ip address.
  
![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/33.PNG)
  
- After succesful load of the WordPress page I was able to create a user and log in to the site.
  
  ![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/34.PNG)
  
  ![alt text](https://github.com/Ellawangari/DevOps-Project006/blob/main/Images/35.PNG)
  
  
  ****Had a few challenges but it was an exicting experience to try and work around the issues and successfully complete the project.****
  
  ****Plus I always enjoy getting to learn something new and with [darey.io](https://www.darey.io) PBL(Project Based Learning) Structure it makes it so exicting and easier to learn.****

  
  
  
