# LEMP STACK IMPLEMENTATION ON AWS
 ## **This project shows how to implement LEMP**(Linux-Nginx-Mysql-Php) on AWS

 ## .......................................INSTALLING NGINX WEBSERVER......................................
--- 
 Update a list of packages in package manager

 **`sudo apt update -y`**

Run Nginx package installation

 **`sudo apt install nginx`**

 Verify Nginx is Up and running.

 **`sudo systemctl status nginx`**

 ![Alt text](<LEMP/Nginx Status.png>)

 Verify the web server is reachable from the localhost

```
 curl http://localhost:80

or

 curl http://127.0.0.1:80
 
```

![Alt text](<LEMP/curl localhost.png>)

Get IPV4 ip address by using the command  

**`curl http:// 169.254.169.254/latest/meta-data/public-ipv4`**

Open a browser and input the public ip address displayed by the previous command you should get a welcome page from Nginx.

**`http://<Public-IP-Address>:80`**

![Alt text](<LEMP/Nginx webpage.png>)


## .......................INSTALLING MYSQL..............................
---

Install MYSQL package using apt command

**`sudo apt install mysql-server -y`**

Log into Mysql console.

**`sudo mysql`**

![Alt text](<LEMP/mysql login.png>)

Run a security script that comes with Mysql to change some insecure default settings, but before then we are setting root password as shown below.

**`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'P@ssW0rd@1';`**

Exit Mysql.

**`exit`**

Run the security with the command.

**`sudo mysql_secure_installation`**

This will ask if you want to configure the VALIDATE PASSWORD PLUGIN, After the validation is completed, login to MYSQL and input the password by adding the -p flag.

**`sudo mysql -p`**

After succcessful confimation of login, you can exit MYSQL

**`exit`**


## ......................................INSTALLING PHP........................................
---

install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, we need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

**`sudo apt install php-fpm php-mysql -y`**


## ...................................Configuring Nginx to Use PHP Processor......................................
---
Create a folder called projectLEMP for our webserver.
Create the root web directory for your_domain in /var/www/ folder as follows:

**`sudo mkdir /var/www/projectLEMP`**

Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:

**`sudo chown -R $USER:$USER /var/www/projectLEMP`**

Open a new configuration file in Nginx’s sites-available directory

**`sudo nano /etc/nginx/sites-available/projectLEMP`**

![Alt text](<LEMP/Nginx script.png>)


After editing, save and close the file. If you’re using nano, you can do so by typing `CTRL+X` and then `y` and `ENTER` to confirm.

Lets activate the configuration by linking the config file from Nginx’s sites-enabled directory, This will tell Nginx to use the configuration next time it is reloaded:

**`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`**

Let's test the configuration is working.

**`sudo nginx -t`**

![Alt text](<LEMP/Nginx configstatus.png>)

We need to disable default Nginx host that is currently configured to listen on port 80, for this run:

**`sudo unlink /etc/nginx/sites-enabled/default`**

Let's reload NGINX to apply these changes. 

**`sudo systemctl reload nginx`**

Create an index.html file in the location /var/www/projectLEMP so that we can test that your new server block works as expected:

```
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```
open your website URL from the browser using IP address or DNS name:

**`http://<Public-IP-Address>:80`**  or  **`http://<Public-DNS-Name>:80`**

The LEMP stack is now fully configured. In the next step, we’ll create a PHP script to test that Nginx is in fact able to handle .php files within our newly configured website.

## ..............................TESTING PHP WITH NGINX............................
---

Do this by creating a test PHP file in our document root. let's open a new file called info.php:

**`sudo nano /var/www/projectLEMP/info.php`**

![Alt text](<LEMP/php nano.png>)


Access this page in the web browser by visiting the domain name or public IP address set up in the Nginx configuration file, followed by /info.php:

`http://`server_domain_or_IP`/info.php`

![Alt text](<LEMP/php webpage.png>)

## ................RETRIEVING DATA FROM MYSQL DATABASE WITH PHP...............
---

We will create a database named lemp_database and a user named lemp_user.

First, connect to the MySQL console using the root account and password:

**`sudo mysql -p`**

Create a new database and user.

**`mysql> CREATE DATABASE `\`test_database \`;**


Create a new user and grant the user full privileges on the database you have just created.

**`mysql>  CREATE USER 'testuser'@'%' IDENTIFIED WITH mysql_native_password BY 'P@ssWord124';`**

Give this user permission over the lemp_database database:

`mysql> GRANT ALL ON test_database.* TO 'testuser'@'%';`

This will give the lemp_user user full privileges over the   **test_database** database, while preventing this user from creating or modifying other databases on your server.

exit the MySQL shell with:  **`exit`**

login to our `test_database` with the new `test_user` account created.

**`mysql -u testuser -p test_database`**

Let's confirm we have access to `test_database`.

**`mysql> SHOW DATABASES;`**

![Alt text](<LEMP/mysql database status.png>)

Create a test table named todo_list with the following statement:

```
 CREATE TABLE test_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));
```
Let's insert a few rows of content in the test table. Ywe will repeat the next command a few times, using different VALUES:

```
INSERT INTO test_database.todo_list (content) VALUES ("My first database item");
INSERT INTO test_database.todo_list (content) VALUES ("My second important item");
```
To see the contents of our table, run the command:

**`mysql> select * from test_database.todo_list;`**

![Alt text](<LEMP/Create table in database.png>)

exit MYSQL.
**`exit`**

We will create a PHP script that will connect to MySQL and query the content. let's create a new PHP file in the custom web root directory.

**`vi /var/www/projectLEMP/todo_list.php`**

```
<?php
$user = "testuser";
$password = "P@ssWord124";
$database = "test_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```
Save and close vi.
`:wq` then press `Enter`.

Access the todo_list.php page from our web browser

**`http://<Public_domain_or_IP>/todo_list.php`**

![Alt text](<LEMP/todo list webpage.png>)

If you get an output like the picture above it shows the php environment can successfuy interact with the mysql database.

**NOTE!**
 Make sure the `php-fpm.sock` version referenced in the `etc/nginx/sites-available/projectLEMP` and `etc/nginx/sites-enabled/projectLEMP` files is the same version as the one installed in the `/var/run/php/` folder.

 

### ***Thank you !!!***