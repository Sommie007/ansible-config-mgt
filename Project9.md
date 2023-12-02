## IMPLEMENTING WORDPRESS WEBSITE WITH LVM STORAGE MANAGEMENT

Create two instances called (Make sure to use Redhat as the distro)

**`Webserver`**

**`MYSQL DB`**

[Setup an Ec2 instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html)

**Step 1**
## Prepare a Web Server

Create a Volume on AWS after powering on the server

![Alt text](<Lvm implementation/Create VL.png>)

Make sure the volume created is in the same Avaliability Zone as the Webserver and MYSQL DB in this case us-east-1b

![Alt text](<Lvm implementation/Attach.png>)

Then attach the Volume to the Webserver

![Alt text](<Lvm implementation/Attach2.png>)


After launching the Ubuntu Virtual machine on AWS Account, Update it using;

**`sudo yum update -y`**

Then use the **`lsblk`** command to inspect what block drivers are attcahed to the Webserver

![Alt text](<Lvm implementation/lsblk.png>)

You can also use the **`df -h`** command to see all mounts and free space on your Webserver.

Also use the **`sudo gdisk /dev/xvdf`** command to create a single partition on each of the 3 disks

then use "n"

![Alt text](<Lvm implementation/gdisk.png>)

Then use the **`lsblk`** command to view the newly configured partition on each of the 3 disks.

![Alt text](<Lvm implementation/gdisk1.png>)

Then Install **lvm2** package using this command **`sudo yum install lvm2 -y`** 

![Alt text](<Lvm implementation/installLvm2.png>)

and then use the command **`sudo lvmdiskscan`** to check for available partitions.

![Alt text](<Lvm implementation/lvmdiskscan.png>)

Use the **pvcreate** utility to mark each 3 disks as physical volumes(PVs) to be used by the LVM

**`sudo pvcreate /dev/xvdf1`**

**`sudo pvcreate /dev/xvdg1`**

**`sudo pvcreate /dev/xvdh1`**

veiw the physical volume created with this command **`sudo pvs`**

![Alt text](<Lvm implementation/pvs.png>)

We can now use the **vgcreate** utility to add all 3 PVs to a volume group(VG).

**`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`**

![Alt text](<Lvm implementation/vgcreate.png>)

using the lvcreate utility to create 2 logical volumes apps-lv (using half of the PV size) and logs-lv (using the remaining space of the PV size).

The apps-lv will be used to store data for the website while logs-lv will be used to store data for logs.

**`sudo lvcreate -n apps-lv -L 14G webdata-vg`**

**`sudo lvcreate -n logs-lv -L 14G webdata-vg`**

![Alt text](<Lvm implementation/lvcreate.png>)

**`sudo lvs`** to verify that your logical volume has been created

![Alt text](<Lvm implementation/lvs.png>)


**`sudo vgdisplay -v #view complete setup - VG, PV, and LV`**

**`sudo lsblk`**

![Alt text](<Lvm implementation/sudo lsblk.png>)

use mkfs.ext4 to format the logical volumes with ext4 filesystem

**`sudo mkfs -t ext4 /dev/webdata-vg/apps-lv`**

**`sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`**

![Alt text](<Lvm implementation/mkfs.png>)

create **/var/www/html** directory to store website files

**`sudo mkdir -p /var/www/html`**

create **/home/recovery/logs** to store backup of log data

**`sudo mkdir -p /home/recovery/logs`**

Mount **/var/www/html** on apps-lv logical volume

**`sudo mount /dev/webdata-vg/apps-lv /var/www/html`**

Use **rsync** utility to backup all the files in the log directory. Note that this is required before mounting the file system.

**`sudo rsync -av /var/log/. /home/recovery/logs/`**

Then Mount **/var/log** on logs-lv logical volume. Note that all the existing data on **/var/log** will be deleted.

**`sudo mount /dev/webdata-vg/logs-lv /var/log`**

Restore log files back into /var/log

**`sudo rsync -av /home/recovery/logs/. /var/log`**

update **/etc/fstab** file so that the mount configuration will persist after restart of the server.

**`sudo blkid`**

![Alt text](<Lvm implementation/blkid.png>)

copy the UUID for the vg apps and vg logs then include it in the etc/fstab

**`sudo vi /etc/fstab`**

![Alt text](<Lvm implementation/fstab.png>)

Test the configuration and reload the daemon

**`sudo mount -a`**

**`sudo systemctl daemon-reload`**

verfy your setup use the command **`df -h`**

![Alt text](<Lvm implementation/df.png>)

## INSTALLING WORDPRESS AND CONFIGURATION TO USE MYSQL DATABASE

**Step 2** 
## Prepare a Database Server

Launch the RedHat EC2 instance already create for MYSQL DB server earlier and repeat the same steps as for the Web Server, but instead of apps-lv create db-lv e.g (**`sudo lvcreate -n db-lv -L 14G webdata-vg`**) and mount it to /db directory instead of /var/www/html.



then run the **`df -h`** command to verify your setup

![Alt text](<Lvm implementation/dfDB.png>)

**Step 3** 
## Install Wordpress on your Web Server EC2

Install wget, Apache and it's dependencies since you have already updated the webserver earlier.

**`sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`**

start Apache

**`sudo systemctl enable httpd`**

**`sudo systemctl start httpd`**

![Alt text](<Lvm implementation/start httpd.png>)

To install PHP and it's dependencies use this commands below

```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm -y
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm -y
sudo yum module list php -y
sudo yum module reset php -y
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd -y
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```
**`sudo systemctl status php-fpm`**

![Alt text](<Lvm implementation/status php.png>)

restart Apache 

**`sudo systemctl restart httpd`**

Download wordpress and copy wordpress to var/www/html

```mkdir wordpress
cd   wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
sudo cp -R wordpress /var/www/html/
```

![Alt text](<Lvm implementation/cp wordpress.png>)

Then give ownership to apache

``` sudo chown -R apache:apache /var/www/html/wordpress
 sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
 sudo setsebool -P httpd_can_network_connect=1
```
![Alt text](<Lvm implementation/chown wordpress.png>)

**Step 4** 
## Install MYSQL on your DB Server Ec2

**`sudo yum update -y`**

**`sudo yum install mysql-server`**

Verify that the service is up and running using this command 

**`sudo systemctl status mysqld`**

![Alt text](<Lvm implementation/status mysql.png>)

Enable and restart the service using the two command below

**`sudo systemctl restart mysqld`**

**`sudo systemctl enable mysqld`**

**Step 5** 
## configure the DB to work with wordpress

```sudo mysql
CREATE DATABASE wordpress;
CREATE USER `myuser`@`172.31.25.48` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'172.31.25.48';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
```

![Alt text](<Lvm implementation/create DB.png>)

**Step 6** 
## configure wordpress to connect to remote database

Remember to open MySQL port 3306 on DB server Ec2. Allow only access to the DB server only form the Webserver's IP address.

![Alt text](<Lvm implementation/inbound rule.png>)

Install MySQL client and test you can connect from the web server to your DB server successfully using the mysql-client

**`sudo yum install mysql -y`**

**`sudo mysql -u myuser -p -h 172.31.30.47`**

![Alt text](<Lvm implementation/connection to mysql server.png>)

Then change permission and configuration so apache can use Worpress.

Enable port 80 in Inbound rules configuration for your Web Server EC2(enable from anywhere 0.0.0.0/0)

Try to access from your browser using this link to your wordpress

http://54.83.107.247/wordpress/

![Alt text](<Lvm implementation/wordpress page.png>)

![Alt text](<Lvm implementation/worpress1.png>)

![Alt text](<Lvm implementation/worpress2.png>)

And you have successfully logged into Wordpress.

### Thank You!!












