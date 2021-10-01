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





















