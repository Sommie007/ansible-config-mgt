## 3-TIER WEB APPLICATION ARCHITECTURE WITH A SINGLE DATABASE AND AN NFS SERVER AS SHARED FILES STORAGE

![Alt text](<Web Application Architecture/Architecture.png>)

## IMPLEMENTING A BUSINESS WEBSITE USING NFS FOR THE BACKEND FILE STORAGE
**STEP 1**
## PREPARE NFS SERVER

Spin up a new EC2 instance with RHEL Linux 8 Operating System

using the lvcreate utility to create 2 logical volumes apps-lv (using half of the PV size) and logs-lv (using the remaining space of the PV size).

The apps-lv will be used to store data for the website while logs-lv will be used to store data for logs.

**`sudo lvcreate -n opt-lv -L 9G webdata-vg`**

**`sudo lvcreate -n apps-lv -L 9G webdata-vg`**

**`sudo lvcreate -n logs-lv -L 9G webdata-vg`**

![Alt text](<Web Application Architecture/create lv.png>)

**`sudo lvs`** to verify that your logical volume has been created

![Alt text](<Web Application Architecture/lvs.png>)

**`sudo lsblk`**

![Alt text](<Web Application Architecture/lsblk.png>)

use mkfs.xfs to format the logical volumes with xfs filesystem

**`sudo mkfs -t xfs /dev/webdata-vg/opt-lv`**

**`sudo mkfs -t xfs /dev/webdata-vg/apps-lv`**

**`sudo mkfs -t xfs /dev/webdata-vg/logs-lv`**

create **/mnt/opt** to be used as Jenkins server

**`sudo mkdir -p /mnt/opt`** 

create **/mnt/apps** directory to store website files

**`sudo mkdir -p /mnt/apps`** 

create **/mnt/logs** to store webserver logs

**`sudo mkdir -p /mnt/logs`** 

Mount **/mnt/opt** on opt-lv logical volume

**`sudo mount /dev/webdata-vg/opt-lv /mnt/opt`**

Mount **/mnt/apps** on opt-lv logical volume

**`sudo mount /dev/webdata-vg/apps-lv /mnt/apps`**

Use **rsync** utility to backup all the files in the log directory. Note that this is required before mounting the file system.

**`sudo rsync -av /var/log/. /mnt/logs`**

Then Mount **/var/log** on logs-lv logical volume. Note that all the existing data on **/var/log** will be deleted.

**`sudo mount /dev/webdata-vg/logs-lv /mnt/logs`**

**`sudo blkid`**

![Alt text](<Web Application Architecture/sudo blkid.png>)

copy the UUID for the vg apps and vg logs then include it in the etc/fstab

**`sudo vi /etc/fstab`**

![Alt text](<Web Application Architecture/mounts.png>)

Test the configuration and reload the daemon

**`sudo mount -a`**

**`sudo systemctl daemon-reload`**

verfy your setup use the command **`df -h`**

![Alt text](<Web Application Architecture/df.png>)

Install NFS server and configure it to start reboot and make sure it is up and running.

```
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```
![Alt text](<Web Application Architecture/nfs status.png>)

Set up a permission that will allow our Web servers to read, write and execute files on NFS

```
sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt

sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt

sudo systemctl restart nfs-server.service
```
![Alt text](<Web Application Architecture/chmod.png>)

## configure access to NFS for clients within the same subnets 

**`sudo vi /etc/exports`**

```
/mnt/apps 172.31.32.0/20(rw,sync,no_all_squash,no_root_squash)
/mnt/logs 172.31.32.0/20(rw,sync,no_all_squash,no_root_squash)
/mnt/opt 172.31.32.0/20(rw,sync,no_all_squash,no_root_squash) 
```
![Alt text](<Web Application Architecture/vi.png>)

**`sudo exportfs -arv`**

![Alt text](<Web Application Architecture/export.png>)

Check which ports are used by NFS using this command **`rpcinfo -p | grep nfs`**, then open it using this security group.

![Alt text](<Web Application Architecture/rpcinfo.png>)

Also open these additional ports TCP111, UDP111, UDP2049,

![Alt text](<Web Application Architecture/inbound.png>)

## CONFIGURE THE DATABASE SERVER
**STEP 2**

**`sudo apt update -y`**

**`sudo apt install mysql-server -y`**

**`sudo systemctl enable mysql`**

**`sudo systemctl start mysql`**

Verify that the service is up and running using this command 

**`sudo systemctl status mysql`**

![Alt text](<Web Application Architecture/status mysql.png>)

Enable and restart the service using the two command below

```
sudo mysql
CREATE DATABASE tooling;
CREATE USER `webaccess`@`172.31.32.0/20` IDENTIFIED BY 'myp@ss1';
GRANT ALL ON tooling.* TO 'webaccess'@'172.31.32.0/20';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
```
![Alt text](<Web Application Architecture/mysql config.png>)

## CONFIGURE THE WEBSERVER SERVERS
**STEP 3**

configure NFS client on the three servers
Deploy a Tooling application to the Webservers into a shared NFS folder
Then configure the Webservers to work with a single MySQL database

To configure NFS client on the three servers run this command

**`sudo yum update -y && sudo yum install nfs-utils nfs4-acl-tools -y`**

The mount **/var/www** and target the NFS server's export for apps

**`sudo mkdir /var/www`**
**`sudo mount -t nfs -o rw,nosuid 172.31.38.213:/mnt/apps /var/www`**

Verify that NFS was mounted successfully by running **df -h** and also make sure the changes persist on the server a reboot

![Alt text](<Web Application Architecture/dfcheck.png>)

**`sudo vi /etc/fstab`**

then add the following line

172.31.38.213:/mnt/apps /var/www nfs defaults 0 0

![Alt text](<Web Application Architecture/fstab.png>)

then install apache and PHP

```
sudo yum install httpd -y

sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm -y

sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm -y

sudo dnf module reset php -y

sudo dnf module enable php:remi-7.4 -y

sudo dnf install php php-opcache php-gd php-curl php-mysqlnd -y

sudo systemctl start php-fpm

sudo systemctl enable php-fpm

sudo setsebool -P httpd_execmem 1

```
You can verify that the apache files and directories are available on the web servers in **/var/www** and also on the NFS server /mnt/apps to make sure the configuration worked fine.

You can create a test file to confirm everything works fine across the NFS and the servers.

![Alt text](<Web Application Architecture/touchtest.png>)

![Alt text](<Web Application Architecture/test.png>)

Futher located the log folder for Apache on the webserver and mount it to the NFS server's export for logs.

**`sudo mount -t nfs -o rw,nosuid 172.31.38.213:/mnt/logs /var/log/httpd`**

[Then Fork this repository ](https://github.com/darey-io/tooling)

Then depoly the tooling website's code to the webserver. ensure that the html folder from the respository is deployed to **var/www/html**

Make sure the TCP port 80 is open on the Webservers.

![Alt text](<Web Application Architecture/inbound80.png>)

Install git on the on the 3 webservers using **sudo yum install git -y**

then clone the repo using this command **git clone https://github.com/Sommie007/tooling.git** on each of the webservers

Then cd into tooling and run the command **`sudo cp -R html/. /var/www/html/`**
to copy everything in the folder of the tooling to the html directory.

![Alt text](<Web Application Architecture/gitconfig.png>)

remember to check your permission on /var/www/html then disable SELinux using this command **sudo setenforce 0** to make changes permanent open the following config file using this command **sudo vi /etc/sysconfig/selinux** and set SELinux=disabled, then restart httpd using this command 
**sudo systemctl restart httpd**

![Alt text](<Web Application Architecture/disable selinux.png>)

remember to go to your MYSQL server and run this command **sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf** and edit the loop back address or localhost address to 0.0.0.0 for binding. so that your tool-db.sql command can work fine, then restart the mysql service **sudo systemctl restart mysql**

![Alt text](<Web Application Architecture/mysqlconfig.png>)
You can now update the website's config to connect to the database in **sudo vi var/www/html/functions.php file**, 

![Alt text](<Web Application Architecture/phpconfig.png>)

Intstall mysql on each of the webservers using **sudo yum install mysql -y**

**cd tooling**
Apply the tooling-db.sql script to your database using this command **mysql -h 172.31.44.49 -u webaccess -p tooling < tooling-db.sql**

![Alt text](<Web Application Architecture/tooling schema script.png>)

You can further verify that the user has been created in the tooling database.

![Alt text](<Web Application Architecture/tooling db.png>)

make sure the port 80 is included on the inbound rule on the webservers then
browse the public Ip. 

![Alt text](<Web Application Architecture/webpagedisplay.png>)

## THANK YOU!!