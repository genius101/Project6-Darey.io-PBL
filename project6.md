<h1> In this project, we would be implementing a Web Solution With WordPress</h1>

<p>In this project we are tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress</p>

<h2>Project 6 consists of two parts:</h2>

<p>Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give you practical experience of working with disks, partitions and volumes in Linux.</p>

<p>Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify your skills of deploying Web and DB tiers of Web solution.</p>

<h2>Step 1 — Prepare a Web Server</h2>

<p>Create and configure two Linux-based virtual servers (EC2 instances in RedHat)</p>

<p>Launch an EC2 instance that will serve as “Web Server”. Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB</p>

![1b](https://user-images.githubusercontent.com/10243139/124367167-cb37f780-dc4c-11eb-8673-9cc801a7f8ce.jpg)

<p>Attach all three volumes one by one to your Web Server EC2 instance</p>

<p>Use gdisk utility to create a single partition on each of the 3 disks:</p>

- [x] sudo gdisk /dev/xvdf
- [x] sudo gdisk /dev/xvdg
- [x] sudo gdisk /dev/xvdh

![1d](https://user-images.githubusercontent.com/10243139/124367221-28cc4400-dc4d-11eb-8e41-53038af8fccb.jpg)

<p>Use lsblk utility to view the newly configured partition on each of the 3 disks:</p>

- [x] lsblk

![1e](https://user-images.githubusercontent.com/10243139/124367246-64670e00-dc4d-11eb-9007-fdd66a81afb7.jpg)

- [x] Install lvm2 package using "sudo yum install lvm2". 
	
- [x] Run "sudo lvmdiskscan" command to check for available partitions.

![1f](https://user-images.githubusercontent.com/10243139/124367282-c162c400-dc4d-11eb-908a-e969fd1b17a4.jpg)

<p>Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM:</p>
	
- [x] sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1

![1g](https://user-images.githubusercontent.com/10243139/124367301-e7886400-dc4d-11eb-9d31-4c2b70109e82.jpg)

<p>Verify that your Physical volume has been created successfully by running:</p>
	
- [x] sudo pvs

![1h](https://user-images.githubusercontent.com/10243139/124367322-17d00280-dc4e-11eb-852b-a7d1499e9853.jpg)

<p>Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg:</p>

- [x] sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1

<p>Verify that your VG has been created successfully by running:</p>

- [x] sudo vgs

<p>Use lvcreate utility to create 2 logical volumes:</p>

- [x] sudo lvcreate -n apps-lv -L 14G webdata-vg
	
- [x] sudo lvcreate -n logs-lv -L 14G webdata-vg

<p>Verify that your Logical Volume has been created successfully by running:
	
- [x] sudo lvs
  
![1l](https://user-images.githubusercontent.com/10243139/124367437-0e936580-dc4f-11eb-8f90-7b5c9d99c98d.jpg)
  
<p>Verify the entire setup:</p>

- [x] sudo vgdisplay -v 

- [x]	sudo lsblk 

![1m](https://user-images.githubusercontent.com/10243139/124367500-82357280-dc4f-11eb-9d26-76aa1bc0b865.jpg)

<p>Use mkfs.ext4 to format the logical volumes with ext4 filesystem:</p>

- [x] sudo mkfs -t ext4 /dev/webdata-vg/apps-lv

- [x] sudo mkfs -t ext4 /dev/webdata-vg/logs-lv

![1n](https://user-images.githubusercontent.com/10243139/124367569-27e8e180-dc50-11eb-9de7-544fbf9a2d15.jpg)

<p>Create /var/www/html directory to store website files:</p>

- [x] sudo mkdir -p /var/www/html

<p>Create /home/recovery/logs to store backup of log data:</p>

- [x] sudo mkdir -p /home/recovery/logs

<p>Mount /var/www/html on apps-lv logical volume:</p>

- [x] sudo mount /dev/webdata-vg/apps-lv /var/www/html/

<p>Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs:</p>

- [x] sudo rsync -av /var/log/. /home/recovery/logs/

<p>Mount /var/log on logs-lv logical volume:</p>

- [x] sudo mount /dev/webdata-vg/logs-lv /var/log 

<p>Restore log files back into /var/log directory:</p>

- [x] sudo rsync -av /home/recovery/logs/log/. /var/log

<p>Update /etc/fstab file so that the mount configuration will persist after restart of the server, you can get the UUID of the device by running sudo blkid. Update the file with:</p>

	UUID=874b555a-0af3-4344-a233-e429b13c666c /var/www/html ext4 defaults 0 0
	UUID=5d84b27a-9e74-4927-92cd-b8664962b697 /var/log      ext4 defaults 0 0

<p>Test the configuration and reload the daemon:</p>

- [x] sudo mount -a
- [x] sudo systemctl daemon-reload

<p>Verify your setup by running df -h, output must look like this:</p>

![1x](https://user-images.githubusercontent.com/10243139/124367761-fffa7d80-dc51-11eb-9078-a749d796d23a.jpg)

<h2>Step 2 — Prepare the Database Server</h2>

<p>Launch a second RedHat EC2 instance that will have a role - ‘DB Server’ Repeat the same steps as for the Web Server</p>

<p>Create and Attach three volumes one by one to your Database Server EC2 instance</p>

<p>Use gdisk utility to create a single partition on each of the 3 disks:</p>

- [x] sudo gdisk /dev/xvdf

- [x] sudo gdisk /dev/xvdg
	
- [x] sudo gdisk /dev/xvdh

<p>Install lvm2 package by running the command:

- [x] sudo yum install lvm2 -y
	
<p>Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM:</p>

- [x] sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1

<p>Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg:</p>

- [x] sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1

<p>Use lvcreate utility to create 2 logical volumes:</p>

- [x] sudo lvcreate -n db-lv -L 14G database-vg
	
- [x] sudo lvcreate -n logs-lv -L 14G database-vg

<p>Verify that your Logical Volume has been created successfully by running:</p>

- [x] sudo lvs

<p>Use mkfs.ext4 to format the logical volumes with ext4 filesystem:</p>

- [x] sudo mkfs -t ext4 /dev/database-vg/db-lv

<p>Create /db directory to store database files:</p>

- [x] sudo mkdir /db

<p>Mount /db on db-lv logical volume:</p>

- [x] sudo mount /dev/database-vg/db-lv /db

<p>Confirm that it has been mounted by running this command:</p>

- [x] df -h

![2l](https://user-images.githubusercontent.com/10243139/124367905-777cdc80-dc53-11eb-837b-26d9a1ed747c.jpg)

<p>Update /etc/fstab file so that the mount configuration will persist after restart of the server, you can get the UUID of the device by running sudo blkid. Update the file with:</p>

	UUID=ae2f91ec-5e4a-4b49-9d82-3d61e0d7bb22 /db ext4 defaults 0 0
	
<p>Test the configuration and reload the daemon:</p>

- [x] sudo mount -a

- [x] sudo systemctl daemon-reload

<h2>Step 3 — Install Wordpress on your Web Server EC2</h2>

<p>Update the repository

- [x] sudo yum update -y
	
<p>Install wget, Apache and it’s dependencies
	
- [x] sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
	
<p>Start Apache:
	
- [x] sudo systemctl enable httpd

- [x] sudo systemctl start httpd
	
<p>To install PHP and it’s depemdencies:</p>

- [x] sudo yum install https://dl.fedoraproject.org/pub/epel/epel-	release-latest-8.noarch.rpm
	
- [x] sudo yum install yum-utils 	http://rpms.remirepo.net/enterprise/remi-release-8.rpm
	
- [x] sudo yum module list php
	
- [x] sudo yum module reset php
	
- [x] sudo yum module enable php:remi-7.4
	
- [x] sudo yum install php php-opcache php-gd php-curl php-mysqlnd
	
- [x] sudo systemctl start php-fpm
	
- [x] sudo systemctl enable php-fpm
	
- [x] sudo setsebool -P httpd_execmem 1

<p>Restart Apache</p>

- [x] sudo systemctl restart httpd

<p>Download wordpress and copy wordpress to var/www/html<p>
	
- [x] mkdir wordpress
	
- [x] cd   wordpress
	
- [x] sudo wget http://wordpress.org/latest.tar.gz
	
- [x] sudo tar xzvf latest.tar.gz
	
- [x] sudo rm -rf latest.tar.gz
	
- [x] sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
	
- [x] sudo cp -R wordpress/. /var/www/html/
	
<p>Configure SELinux Policies<p>
	
- [x] sudo chown -R apache:apache /var/www/html/wordpress

- [x] sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R

- [x] sudo setsebool -P httpd_can_network_connect=1
	
![3g](https://user-images.githubusercontent.com/10243139/124368193-8dd86780-dc56-11eb-8581-0d32ba3afee1.jpg)

<h2>Step 4 — Install MySQL on your DB Server EC2</h2>

<p>Update and install mysql server on the DB Server EC2:</p>

- [x] sudo yum update

- [x] sudo yum install mysql-server

<p>Verify that the service is up and running by using:</p>

- [x] sudo systemctl restart mysqld
	
- [x] sudo systemctl enable mysqld


<h2>Step 5 — Configure DB to work with WordPress</h2>

- [x] sudo mysql

		CREATE DATABASE wordpress;
		CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
		GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
		FLUSH PRIVILEGES;
		SHOW DATABASES;
		exit
		
![5](https://user-images.githubusercontent.com/10243139/124373204-23d8b600-dc88-11eb-8a22-d26a51eb21ae.jpg)


<h2>Step 6 — Configure WordPress to connect to remote database</h2>

<p>Open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY from your Web Server’s IP address</p>

![6a](https://user-images.githubusercontent.com/10243139/124373238-77e39a80-dc88-11eb-9ef1-79576b056264.png)

<p>Install MySQL client and test that you can connect from your Web Server to your DB server:</p>

- [x] sudo yum install mysql -y
- [x] sudo mysql -u myuser -p -h <DB-Server-Private-IP-address>
	
<p>Verify if you can successfully execute SHOW DATABASES command and see a list of existing databases</p>

![6c](https://user-images.githubusercontent.com/10243139/124373271-c85af800-dc88-11eb-91fc-97d25f538b63.jpg)

<p>Update /etc/my.cnf, add the following to the file:</p>
		
	[mysqld]
	bind-address=0.0.0.0
	
![6d](https://user-images.githubusercontent.com/10243139/124373304-2c7dbc00-dc89-11eb-8f6c-3de98dba467a.jpg)
	
<p>Restart mysql:</p>
	
- [x] sudo systemctl restart mysqld
	
<p>Change permissions and configuration on the webserver so Apache could use WordPress, update the Database name, username and password:</p>
	
- [x] sudo nano /var/www/html/wp-config.php

![6f](https://user-images.githubusercontent.com/10243139/124373344-83839100-dc89-11eb-8fbf-545efdc010cf.jpg)

<p>Try to access WordPress by going through the Public IP Address of your Webserver:</p>
	
![6g](https://user-images.githubusercontent.com/10243139/124373364-b594f300-dc89-11eb-9c44-1b138b3677df.jpg)
	
<p>The Wordpress Page after setup</p>
	
![6g 1](https://user-images.githubusercontent.com/10243139/124373381-d0fffe00-dc89-11eb-9d5d-1e7a0c30ccec.jpg)
	
<p>The Wordpress Admin Page</p>
	
![6g 2](https://user-images.githubusercontent.com/10243139/124373397-eb39dc00-dc89-11eb-94b4-407803729c18.jpg)

<h3>We have learned how to configure Linux storage susbystem and have also deployed a full-scale Web Solution using WordPress CMS and MySQL RDBMS!</h3>


