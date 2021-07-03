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

 
