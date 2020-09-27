

$yum install maven

$git clone https://github.com/krishnamaram2/webapps.git

$cd webapps/src/middleware/Student

$mvn package

a.Web Server(Apache HTTP) Set up
=====================================

Step 1: Launch EC2 instance

$sudo yum update -y && sudo yum install wget -y && sudo yum install git -y

Step 2: install apache http server

$sudo yum install httpd -y

$sudo systemctl start httpd
$sudo systemctl enable httpd

Step 3: Build and deploy angular code into web server

$cd webapps/binaries

$less dist/main.js |  grep this.baseUrl = 'http://<app-server-ip>:8080/Student/api/'; note:have to edit two times(nile 314, 701)

$cp -rf dist /var/www/html


b.App Server(Apache Tomcat) Set up
========================================

Step 1: Launch EC2 instance

$sudo yum update -y && sudo yum install wget -y && sudo yum install git -y

Step 2: install openjdk

sudo yum install java-1.8.0-openjdk-devel -y

Step 3: install set up tomcat app server

$sudo groupadd tomcat

$sudo useradd -M -s /bin/nologin -g tomcat -d /opt/tomcat tomcat

$wget https://downloads.apache.org/tomcat/tomcat-8/v8.5.57/bin/apache-tomcat-8.5.57.tar.gz 

$sudo mkdir /opt/tomcat

$sudo tar xvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1

$cd /opt/tomcat

$sudo chgrp -R tomcat /opt/tomcat

$sudo chmod -R g+r conf

$sudo chmod g+x conf

$sudo chown -R tomcat webapps/ work/ temp/ logs/

$sudo vi /etc/systemd/system/tomcat.service

[Unit]

Description=Apache Tomcat Web Application Container

After=syslog.target network.target

[Service]

Type=forking

Environment=JAVA_HOME=/usr/lib/jvm/jre

Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid

Environment=CATALINA_HOME=/opt/tomcat

Environment=CATALINA_BASE=/opt/tomcat

Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'

Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

ExecStart=/opt/tomcat/bin/startup.sh

ExecStop=/bin/kill -15 $MAINPID

User=tomcat

Group=tomcat

UMask=0007

RestartSec=10

Restart=always

[Install]

WantedBy=multi-user.target

$sudo systemctl daemon-reload

$sudo systemctl start tomcat

$sudo systemctl enable tomcat

Step 4: build source code and deploy war file

$cd webapps/src/middleware/Student/target

$cp -rf Student.war /opt/tomcat/webapps

$less /opt/tomcat/webapps/Student/WEB-INF/classes/application.properties | grep db.url= jdbc:mysql://<db-server-ip>:3306/indigo
 
 
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

$git clone https://github.com/krishnamaram2/WebApp.git

$cd WebApp/binary

$mysql -u <user_name> -p indigo < indigo.sql

