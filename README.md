

$yum install maven

$git clone https://github.com/krishnamaram2/webapps.git

$cd webapps/src/middleware/Student

$mvn package

b.App Server(Apache Tomcat) Set up
========================================

Step 1: Launch EC2 instance

$sudo yum update -y && sudo yum install wget -y && sudo yum install git -y

Step 2: install openjdk

sudo yum install java-1.8.0-openjdk-devel -y

Step 3: install set up tomcat app server

$wget https://downloads.apache.org/tomcat/tomcat-8/v8.5.57/bin/apache-tomcat-8.5.57.tar.gz 

$sudo tar xvf apache-tomcat-8*tar.gz -C /home/centos/tomcat --strip-components=1

$sudo chown -R centos:centos /home/centos/tomcat*

$sudo sh /home/centos/tomcat/bin/startup.sh

Step 4: build source code and deploy war file

$sudo rm -rf /home/centos/tomcat/webapps/ROOT/*
 
$git clone https://github.com/krishnamaram2/binary-code.git && cd binary-code && cp -rf dist /home/centos/tomcat/webapps/ROOT && cp -rf Student.war /home/centos/tomcat/webapps/

$sed -i 's/localhost/192.168.5.4/g' /home/centos/tomcat/webapps/Student/WEB-INF/classes/application.properties 
 
c.Database Server(MySQL) Set up
====================================

Step 1: Launch EC2 instance

$sudo yum update -y && sudo yum install wget -y && sudo yum install git -y

Step 2:install MySQL server

$sudo wget https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm

$sudo rpm -Uvh mysql80-community-release-el7-1.noarch.rpm

$sudo yum install mysql-server -y

$sudo systemctl start mysqld

$sudo grep 'temporary password' /var/log/mysqld.log

$sudo mysql_secure_installation

Step 3: create database and user for MySQL 

$mysql -u <<user>> -p
  
mysql>create database indigo;

mysql>CREATE USER '<user_name>'@'%' IDENTIFIED BY '<passwd>';
 
mysql>GRANT ALL ON *.* TO '<user>'@'%';
  
mysql>FLUSH PRIVILEGES;

$git clone https://github.com/krishnamaram2/binary-code.git && cd binary-code

$mysql -u <user_name> -p indigo < indigo.sql

