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
