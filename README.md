This README.md provides a complete, end-to-end setup guide for your LoginWebApp, covering server creation through application deployment.
------------------------------
## LoginWebApp: End-to-End Setup Guide
A secure user authentication system built with JSP, Servlets, JDBC, and MySQL, deployed on an Ubuntu-based App Server and a MySQL-based DB Server.
------------------------------
## 🏗️ Phase 1: Infrastructure Creation

   1. Create Two VMs (Ubuntu 22.04 LTS):
   * DB Server: To host the MySQL database.
      * App Server: To host Tomcat and the Java application.
   2. Configure Security Groups (Cloud Firewall):
   * DB Server: Allow inbound TCP Port 3306 from the App Server's Private IP.
      * App Server: Allow inbound TCP Port 8080 from your Public IP (for browser access) and TCP Port 22 (for SSH).

<img width="1919" height="773" alt="connecto bd ssh" src="https://github.com/user-attachments/assets/ade3ee2d-c13c-4ade-b6da-f6ef17ee14b5" />
<img width="1919" height="783" alt="connect to app ssh" src="https://github.com/user-attachments/assets/f0acbe2d-61be-421b-ba38-52e05217faa2" />
security groups
<img width="1918" height="607" alt="security groups" src="https://github.com/user-attachments/assets/1b2468e1-6740-476d-a9a8-a4ca9904cf4f" />

------------------------------
## 🗄️ Phase 2: Database Server Configuration
Log in to the DB Server and perform the following:
## 1. Install & Secure MySQL

sudo apt update && sudo apt install mysql-server -y
sudo systemctl start mysql
sudo systemctl enable mysql
sudo mysql_secure_installation  # Follow prompts to set a root password



## 2. Configure Remote Access

sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf# Change bind-address from 127.0.0.1 to 0.0.0.0
sudo systemctl restart mysql

## 3. Create 'jet' Database and 'USER' Table [1] 
Log in: sudo mysql -u root -p

CREATE DATABASE jet;
USE jet;
CREATE TABLE USER (
  id int(10) unsigned NOT NULL auto_increment,
  first_name varchar(45) NOT NULL,
  last_name varchar(45) NOT NULL,
  email varchar(45) NOT NULL,
  username varchar(45) NOT NULL,
  password varchar(45) NOT NULL,
  regdate date DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (id)
) ENGINE=InnoDB;
-- Create Application UserCREATE USER 'sai'@'%' IDENTIFIED BY 'sai123saiprasad';GRANT ALL PRIVILEGES ON jet.* TO 'sai'@'%';
FLUSH PRIVILEGES;
EXIT;
<img width="1792" height="939" alt="TABLE CREATED FOR USER REGESTRATION" src="https://github.com/user-attachments/assets/10f8acb1-1158-497c-9480-e232ca3d6135" />

------------------------------
## 💻 Phase 3: App Server Setup & Development
Log in to the App Server and perform the following:
## 1. Install Dependencies

sudo apt update
sudo apt install openjdk-11-jdk maven git -y

## 2. Install Apache Tomcat 9

wget https://apache.org
tar -xvzf apache-tomcat-9.0.110.tar.gz

## 3. Clone and Edit Source Code

git clone <your-git-repo-url> aws-rds-java
cd aws-rds-java

Edit Files: Use nano to update the DB IP address in your .jsp files (e.g., login.jsp, userregistration.jsp):

* Locate the DriverManager.getConnection line.
* Change the IP to your DB Server's Private IP (e.g., 172.31.67.117).
<img width="1694" height="487" alt="login page edit" src="https://github.com/user-attachments/assets/efcef0ad-57a4-4f5d-aebf-d0e09b0a1a9a" />
<img width="1916" height="606" alt="userregistration jsp edit" src="https://github.com/user-attachments/assets/4dae7c1d-8fff-4e7c-8f00-cd54939b9197" />

## 4. Build and Deploy

# Build the application
mvn clean package
# Copy WAR to Tomcat
cp target/LoginWebApp.war ~/apache-tomcat-9.0.110/webapps/
<img width="1461" height="50" alt="copy war file" src="https://github.com/user-attachments/assets/ab7fc4ce-0fdb-4af7-bd38-0e355753af04" />

# Start Tomcat
~/apache-tomcat-9.0.110/bin/startup.sh
<img width="1919" height="662" alt="BUILD SUCESS" src="https://github.com/user-attachments/assets/72f586ef-59b0-4185-bcd1-0352a105bdcf" />
<img width="1745" height="369" alt="war file generated" src="https://github.com/user-attachments/assets/f185d07d-8126-4bf6-aa91-60ce35f661f5" />

------------------------------
## 🌐 Phase 4: Accessing the Application
Use the Public IP of your App Server to access the following pages:

* Login Page: http://<APP_SERVER_PUBLIC_IP>:8080/LoginWebApp/login.jsp
* http://98.80.104.93:8080/LoginWebApp/
* Registration Page: http://<APP_SERVER_PUBLIC_IP>:8080/LoginWebApp/userregistration.jsp [2] 
  http://98.80.104.93:8080/LoginWebApp/register.jsp
  <img width="1919" height="931" alt="login page website" src="https://github.com/user-attachments/assets/30b95fa7-0c8d-4a74-98cc-3ae5f9a380b2" />
<img width="1919" height="677" alt="user registration" src="https://github.com/user-attachments/assets/c7ee18d9-cb6e-4986-b590-64d64a64c1ad" />
<img width="1329" height="487" alt="registration successfull" src="https://github.com/user-attachments/assets/aff8bcf6-e936-4ad2-b7d9-3e7e461445b2" />
database user data storage after registartion in USER table
<img width="1912" height="966" alt="DATABSE_REFELCT" src="https://github.com/user-attachments/assets/0f688b3a-ed9c-491d-8304-a0cd8d610df2" />

------------------------------
## 📝 Troubleshooting

* Connection Refused: Ensure bind-address is 0.0.0.0 on the DB server and Port 3306 is open in the firewall.
* 404 Not Found: Check Tomcat logs at ~/apache-tomcat-9.0.110/logs/catalina.out to see if the WAR deployed correctly.


