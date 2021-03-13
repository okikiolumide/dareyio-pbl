**Load Balancer Solution With Apache**

**Task**

Deploy and configure an Apache Load Balancer for Tooling Website solution building on project 7 architecture on a separate Ubuntu EC2 instance.

**Prerequisites**
>Two RHEL8 Web Servers
>One MySQL DB Server (based on Ubuntu 20.04)
>One RHEL8 NFS server


* Configuration of Apache As A Load Balancer
* Create an Ubuntu Server 20.04 EC2 instance
* Open TCP port 80 on by creating an Inbound Rule in Security Group
* Install Apache Load Balancer on The server and configure it to point traffic coming to LB to both Web Servers:
#Install apache2
>sudo apt update
>sudo apt install apache2 -y
>sudo apt-get install libxml2-dev

#Enable following modules:
>sudo a2enmod rewrite
>sudo a2enmod proxy
>sudo a2enmod proxy_balancer
>sudo a2enmod proxy_http
>sudo a2enmod headers
>sudo a2enmod lbmethod_bytraffic

#Restart apache2 service
>sudo systemctl restart apache2

Check the status of Apache2 to make sure it is up and running 
>sudo systemctl status apache2

* Configure the load balancer as follows
>sudo vi /etc/apache2/sites-available/000-default.conf


* Try other methods, like: bybusyness, byrequests, heartbeat


* Try to access the Load balacer’s public IP address or Public DNS name using a browser to verify the configuration is correct:



**Optional Step - Configuration of Local DNS Names Resolution**

* Configure a local domain name resolution by updating /etc/hosts file
* Open /etc/hosts on the load balancer server.            sudo vi /etc/hosts
* Add 2 records into this file with Local IP address and arbitrary name for both of the Web Servers <WebServer1-Private-IP-Address>Web1 <WebServer2-Private-IP-Address> Web2

5815EFA4-BC3C-4A00-BB45-02B2A7DF6E5A.png


* Update the Load Balancer configuration file with the names instead of IP addresses.



* Try to curl theWeb Servers from LOad balancer locally using curl http://Web1 or curl http://Web2 - it shall work.     Note: this is only internal configuration and it is also local to the LB server, these names will neither be ‘resolvable’ from other servers internally nor from the Internet.


**BLOCKERS**
1. The implementation of this project was quite easy. I did not encounter any technical challenge.
2. The only blocker I had was having to switch to using a mobile device to complete the project due to some faulty parts of my laptop and this was really not flexible enough to implement all the extra tasks.

**RESOURCES**
1. https://darey.io
2. https://stackoverflow.com/questions/10494431/sticky-and-non-sticky-sessions
3. https://access.redhat.com/solutions/90093
4. https://www.nginx.com/resources/glossary/layer-4-load-balancing/
5. https://www.nginx.com/resources/glossary/load-balancing/
6. https://www.f5.com/services/resources/glossary/load-balancer
7. https://www.a10networks.com/blog/how-do-layer-4-and-layer-7-load-balancing-differ/
8. https://httpd.apache.org/docs/2.4/mod/mod_proxy_balancer.html



