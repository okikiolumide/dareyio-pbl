# Client-Server Architecture with MySQL

## To Implement Client-Server Architecture using MySQL Database Management System (DBMS)

Create two linux-based virtual servers
Server A name - `mysql server`
Server B name - `mysql client`

Install mysql client Linux Server
  	Steps
Update Ubuntu:
$sudo apt update
On mysql client Linux Server install MySQL Client software
 $sudo apt-get install mysql-client


  3.  Install mysql server Linux server
Update Ubuntu:
$sudo apt update
On mysql client Linux Server install MySQL Client software
$sudo apt-get install mysql-server

Run a security script that comes pre-installed with MySQL to remove some insecure default settings and lock down access to the database system. 
$sudo apt mysql_secure_installation


MySQL server uses TCP port 3306 by default, open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups. 
Note: For extra security, do not allow all IP addresses to reach your ‘mysql server’ - allow access only to the specific local IP address of your ‘mysql client
Configure MySQL server to allow connections from remote hosts.
	$sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf

	Replace the bind address from ‘127.0.0.1’ to ‘0.0.0.0’

     NOTE: The bind-address configuration within MySQL tells MySQL on which networks it can listen for connections. Binding MySQL to 0.0.0.0 makes it listen on all networks, private and public.


	
4. To Connect remotely to mysql server Database Engine from MySQL client server without using SSH but utilising the mysql utility

connect to the MySQL server console using the root account:
$sudo mysql
Create a new user and grant full privileges on the new database

Grant him full privileges on the database you have just created

log in to the MySQL Client console again using the custom user credentials and hostname
 $ mysql -u username -p -h server_ip_address

Check that you have successfully connected to a remote MySQL server and can perform SQL queries


BLOCKER
1. When connecting to the MySQL server from the MySQL client, I got the error below 
ERROR 1045 (28000): Access denied for user 'okiki'@'localhost' (using password: YES)
SOLUTION: I noticed I did not indicate the hostname for my user when creating the user in the mysql server. 
I updated the user credentials using the correct hostname for my mysql server and removed unused custom user



I entered the correct MySQL command on the client by indicating the server hostname when connecting to the custom user on the server.
 mysql -u okiki -p -h 40.76.228.41

RESOURCES
https://docs.rackspace.com/support/how-to/install-mysql-server-on-the-ubuntu-operating-system/
https://askubuntu.com/
https://stackoverflow.com/
https://serversforhackers.com/c/mysql-network-security#:~:text=MySQL%20Bind%20Address,it's%20using%20the%20unix%20socket.
https://www.percona.com/blog/2019/07/05/fixing-a-mysql-1045-error/

