## IMPLEMENTING NGINX AS A LOAD BALANCER
---

Architecture diagram is shown below

![Alt text](<Nginx LB/Architecture.png>)

After launching the Ubuntu Virtual machine on AWS Account, Update it using;

**`sudo apt update -y`**

After the update is completed, following the instruction from the task assigned

Install Apache using Ubuntu’s package manager ‘apt’:

**`sudo apt install apache2`**

To verify that apache2 is running as a Service in our ubuntu instance, use following command:

**`sudo systemctl status apache2`**

Below is the outcome of the apache status, which shows it is running and active

![Alt text](<Nginx LB/Status of Apache.png>)

After creating Apache, you can permit port 8000 in the inbound rules for both WebServers.

![Alt text](<Nginx LB/Inbound rule.png>)

Then configure apache to serve content on port 8000

sudo vi /etc/apache2/ports.conf 

Then add a new listening directive on port 8000

![Alt text](<Nginx LB/Listen 8000.png>)

sudo vi /etc/apache2/sites-available/000-default.conf

Replace listening 80 with port 8000

![Alt text](<Nginx LB/Listen2.png>)

Then restart apache using this command

**`sudo systemctl restart apache2`**

sudo vi index.html

![Alt text](<Nginx LB/Index.png>)

Change file ownership of index.html file

**`sudo chown www-data:www-data ./index.html`**

Replace old index.html with new index.html

**`sudo chown www-data:www-data ./index.html`**

**`sudo cp -f ./index.html /var/www/html/index.html`**

![Alt text](<Nginx LB/Chown.png>)

Then restart the apache service

**`sudo systemctl restart apache2`**

You should find it like this after browsing the public Ip on port 8000

![Alt text](<Nginx LB/Webpage.png>)

1. Create an EC2 instance based on Ubuntu Server and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections)

![Alt text](<Nginx LB/Nginx Lb.png>)

![Alt text](<Nginx LB/Inbound rule nginx.png>)

Install Nginx on the instance

**`sudo apt update -y && sudo apt install nginx -y`**

**`sudo systemctl status nginx`**

![Alt text](<Nginx LB/Status of nginx.png>)

**`sudo vi /etc/nginx/conf.d/loadbalancer.conf`**

Include the public Ip of the WebserverA and WebserverB

```
        
upstream backend_servers {

    # your are to replace the public IP and Port to that of your webservers
        server 54.242.194.53:8000; # public IP and port for webserserA
        server 54.152.40.216:8000; # public IP and port for webserverB
    }

server {
        listen 80;
        server_name <your load balancer's public IP addres>; # provide your load balancers public IP address

        location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
     }    

```

Check the status of the configuration to make sure it's okay.

**`sudo nginx -t`**

![Alt text](<Nginx LB/nginx config test.png>)


Restart Nginx and make sure the service is up and running

**`sudo systemctl restart nginx`**

Then, browser the public IP of the nginx server 

![Alt text](<Nginx LB/New webpage.png>)

![Alt text](<Nginx LB/New webpage1.png>)

Both WebserverA and WebserverB are both accessed from the Nginx public Ip on port 80, This shows that requests can be sent to any of the two servers to load balance traffic through the niginx server.

Nginx in this illustration represents both a reverse proxy and a load balancer.

### Thank You!!