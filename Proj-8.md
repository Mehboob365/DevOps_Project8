**PROJECT 8: Load Balancer Solution With Apache**

1. Will create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb, so our EC2 list will look like this:

![image](https://user-images.githubusercontent.com/67065306/135689094-bb3ed4fe-8d60-46b5-a484-2a602d7e74b9.png)

2. Will open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in Security Group

  ![image](https://user-images.githubusercontent.com/67065306/135689301-0d1c6871-1ebc-4974-a1b0-0ea4d5f05fd2.png)
  
3. Will Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers:

#Install apache2

    sudo apt update

    sudo apt install apache2 -y

    sudo apt-get install libxml2-dev


#Enable following modules:

    sudo a2enmod rewrite

    sudo a2enmod proxy

    sudo a2enmod proxy_balancer
  
    sudo a2enmod proxy_http
  
    sudo a2enmod headers
  
    sudo a2enmod lbmethod_bytraffic

#Restart apache2 service

    sudo systemctl restart apache2
    
#Make sure apache2 is up and running

     sudo systemctl status apache2

![image](https://user-images.githubusercontent.com/67065306/135691704-f4a8fb3a-90ca-489c-9bc8-7371c2fcce9a.png)

#Configure load balancing

    sudo vi /etc/apache2/sites-available/000-default.conf

#Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

<Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/
  
![image](https://user-images.githubusercontent.com/67065306/135692456-9c8f121f-c9e4-4414-9da2-c1a160cf0e3a.png)

#Restart apache server

   sudo systemctl restart apache2
  
4. We can verify that our configuration works – we try to access our LB’s public IP address or Public DNS name from our browser:
  
     http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php
  
     http://3.250.111.211/index.php

![image](https://user-images.githubusercontent.com/67065306/135692816-dec80f9c-01c6-46ee-baa3-9df85fed5f8b.png)


Note: If in the Project-7 we mounted /var/log/httpd/ from our Web Servers to the NFS server – we will unmount them & make sure that each Web Server has its own log directory.

We will open two ssh/Putty consoles for both Web Servers and run following command:

    sudo tail -f /var/log/httpd/access_log
  
We will refresh our browser page http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php several times and make sure that both servers receive HTTP GET requests from our LB – new records must appear in each server’s log file. The number of requests to each server will be approximately the same since we set loadfactor to the same value for both servers – it means that traffic will be disctributed evenly between them.

If you have configured everything correctly – our users will not even notice that their requests are served by more than one server.
  
![image](https://user-images.githubusercontent.com/67065306/135694502-366b487a-6ee8-49e9-9f07-ae9e7b1cdc4c.png)

**#Configure Local DNS Names Resolution**

Sometimes it is tedious to remember and switch between IP addresses, especially if we have a lot of servers under our management.
What we can do, is to configure local domain name resolution. The easiest way is to use /etc/hosts file, although this approach is not very scalable, but it is very easy to configure and shows the concept well. So let us configure IP address to domain name mapping for our LB.

# We open this file on our LB server

sudo vi /etc/hosts

#We add 2 records into this file with Local IP address and arbitrary name for both of our Web Servers

<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2

Now we can update our LB config file with those names instead of IP addresses.

   BalancerMember http://Web1:80 loadfactor=5 timeout=1
  
   BalancerMember http://Web2:80 loadfactor=5 timeout=1

![image](https://user-images.githubusercontent.com/67065306/135694940-338b8429-46d5-4b2d-ad81-67f0d05393f0.png)


 We  can try to curl our Web Servers from LB locally curl http://Web1 or curl http://Web2 – it shall work.

![image](https://user-images.githubusercontent.com/67065306/135695236-2568fbda-502c-4fff-af0f-eaf325befaa2.png)








