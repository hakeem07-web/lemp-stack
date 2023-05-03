# Deploying a LEMP Stack on AWS
Introduction
What is a Web stack?

A web stack is a set of frameworks and tools used to develop a software product.
This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software.
A web stack is usually comprised of:
• Operating system • Database application • Programming language • Web server
Now that we have defined what a webstack is. Now let’s dive into the type of stack we’ll be using in this project(LEMP Stack).

LEMP is an open-source web application stack used to develop web applications.


**LEMP stands for**
```
• L- Linux Operating System
• E- Nginx Server
• M- MySQL Database
• P- PHP
```
Linux: The operating system. It is a free and open source operating system (OS) that has been around since the mid-1990s.
Linux is popular in part because it offers more flexibility and configuration options than some other operating systems.

Nginx Server: The web server. The Nginx web server processes requests and serves up web assets via HTTP
so that the application is accessible to anyone in the public domain over a simple web URL.
It is developed and maintained by an open community,
Nginx is a mature, feature-rich server that runs a large share of the websites currently on the internet.

MySQL: The database. MySQL is an open source relational database management system for storing application data.
With My SQL, you can store all your information in a format that is easily queried with the SQL language. 
SQL is a great choice if you are dealing with a business domain that is well structured,
and you want to translate that structure into the backend.

PHP: The programming language. The PHP open source scripting language works with Apache to help you create dynamic web pages.
You cannot use HTML to perform dynamic processes such as pulling data out of a database. 
If you prefer, you can swap out PHP in favor of Perl or the increasingly popular Python language.

**How each technology interacts**

At a high-level overview the order of execution shows how the elements interoperate.
The process begins when the Nginx web server receives requests for web pages from a user’s browser. 
If the request is for a PHP file, Nginx passes the request to PHP, which loads the file and executes the code contained in the file
. PHP also communicates with MySQL to fetch any data referenced in the code.

PHP then uses the code in the file and the data from the database to create the HTML that browsers require to display web pages.
The LEMP stack is efficient at handling not only static web pages, but also dynamic pages
where the content may change each time it is loaded depending on the date, time, user identity and other factors.

After running the file code, PHP then passes the resulting data back to the Nginx web server to send to the browser.
It can also store this new data in MySQL. And of course, all of these operations are enabled
by the Linux operating system running at the base of the stack.

#<ins>**Pre-requisites**</ins>
```
1.Basic Linux command line knowledge
2.Access to a terminal
3.Basic knowledge of Vim editor
4.Create an AWS Free Tier account 
5.Basic SSH knowledge
6.Set up AWS CLI
7.Knowledge of how to launch an EC2 instance on AWS and connect to this instance 
```
**Step one: Launch a Virtual Server with Ubuntu Server OS**
The operating system of our LAMP stack is a Linux server and we need a Linux machine with the right specifications
to successfully deploy our LEMP stack. 
Sign in to the AWS Management Console as an IAM user

Navigate to the EC2 dashboard by typing **“EC2”** on the search bar and clicking on EC2 to go to the EC2 dashboard.

Click on the **“Launch Instance”** button

![1](https://user-images.githubusercontent.com/130447223/235752069-40ed0a52-4150-4858-a94d-5a7e558d73c0.png)

Give your instance name (E.g., MyLinuxinstance)

Choose an Amazon Machine Image (AMI) for your instance. The AMI is a pre-configured virtual machine image that contains
the operating system and other software required for your application. For this project, we will be launching an
**Ubuntu instance.** Type **“Ubuntu”** on the search bar to show all available Ubuntu AMIs.
Select “Ubuntu Server 20.04 LTS (HVM), SSD Volume Type, 64-bit (x86)” which is free tier eligible.

![2](https://user-images.githubusercontent.com/130447223/235720730-c078dfdd-8534-4d76-b8ab-4be2441cadff.png)

On the Instance Type, select “t2.micro”, this is free tier eligible.
Select a key pair from your existing keypairs for the region you are in. If you don’t have an existing keypair,
click on “Create new key Pair” to create a new key pair.
Enter a name for your key pair
if you are using a MacOS or Windows 10 machine, select “RSA” as the key pair type and “.pem” as the Private key file format.
Otherwise, select “.ppk” which works with PuTTY.
Click on “Create key pair” to create a new key pair. Note that key Pairs are bound to a region.

![3](https://user-images.githubusercontent.com/130447223/235753139-ecd850ef-e820-4915-b0ba-91dcb962a39f.png)

8. Under “Network settings”, under “Firewall (Security Groups)”, click on “Create security group”

9. Ensure **“Allow SSH traffic from”** is checked. This would enable us to connect to our ec2 instance from
   our local machine via the ssh client.

10. Keep all other default configurations and click on “launch instance” and wait a little while for the launch to complete.
11.  The instance will show under “Instances” in a running state.

![4](https://user-images.githubusercontent.com/130447223/235752918-ae6fa45a-a65d-47ac-8a9b-70a98c038918.png)

We have successfully launched an Ubuntu ec2 instance to the server as our operating system,
next we will connect to this instance to set up all other components of our LEMP stack.

![5](https://user-images.githubusercontent.com/130447223/235969843-be15a60e-5e9a-45d6-8afd-4e86117c2f02.png)

**Connecting to our EC2 instance**

Connecting to our EC2 instance

**IMPORTANT-** Anywhere you see these anchor tags < > , going forward, it means you will need to replace
the content in there with values specific to your situation.
For example, if you see <private-key-name>.pem. This will change to private-key.pem where private-key is the name of your own private key.

Now we move to the terminal in your linux OS.

-The terminal is already installed by default. You just need to open it up.
-We need the .pem key file which we saved from our EC2 downloaded from AWS.
-Change directory into the loacation where your PEM file is. Most likely will be in the Downloads folder
```
cd ~/Downloads
 ``` 
Change permissions for the private key file (.pem) to be readable by the owner of the file
  only to ensure that only you can access your private key. This is done using the chmod command:
```
sudo chmod 0400 <MykeyPair.pem>
 ``` 
Connect to the ubuntu instance using the below command:
```
ssh -i <MyKeyPair>.pem ubuntu@<Public_IP-address>
```
   
![7](https://user-images.githubusercontent.com/130447223/235969415-f6ec568e-5f99-40fe-96fa-3e54d08029dd.png)

**NOTE:** You can get the public ip of your EC2 instance from the management console by clicking
on your instance and copying the “Public IPv4 address”.
   
![6](https://user-images.githubusercontent.com/130447223/235969585-c0900cc7-0a0b-42e2-b884-7cbb343c2eb6.png)

**Step 2: INSTALLING NGINX AND UPDATING THE FIREWALL**
```  
# Update all packages in package manager
sudo apt update

# Install nginx package 
sudo apt install nginx

# Verify that nginx is up and running in our OS after successfully installation:
sudo systemctl status nginx
``` 
![8](https://user-images.githubusercontent.com/130447223/235970371-a9be7d47-48a7-4c08-9105-44e2353c939c.png)
 
If the status shows a green “active (running)” You have successfully launched a web server on the cloud.
   
![9](https://user-images.githubusercontent.com/130447223/235970565-198df8f5-f25f-4ee6-9789-f4e14865c3ea.png)

Next we will edit our security group rule to open TCP port 80 to enable http connection from anywhere on the internet.
Recall that we only opened port 22 when we created our instance. 
Begin by navigating to your EC2 instance on the AWS Console. Click on the security group tab
  
![10](https://user-images.githubusercontent.com/130447223/235970950-47b2339a-c689-42c5-b221-662773182e84.png)

and edit the inbound rules of the running EC2 instance.
Then click on edit **inbound rules** as shown below.
   
![11](https://user-images.githubusercontent.com/130447223/235971102-dcc33caa-7435-4fa6-8b78-c2a523a0dab4.png)

Then click on **Add rule** and configure the inbound rules using **HTTP** as the protocol and **0.0.0.0/0** as the source,
so that traffic from any IP address can enter as shown below.

![12](https://user-images.githubusercontent.com/130447223/235971304-9c9795a5-0d48-4666-bb28-fba9c291706f.png)

Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).
   
![13](https://user-images.githubusercontent.com/130447223/235971814-a4e4b8d1-511c-4059-99dc-2ec89d57750a.png)

First, let us try to check how we can access it locally in our Ubuntu shell, On the Terminal, use the following command
to send a request our Nginx on port 80.
```
$ curl http://localhost:80
or
$ curl http://127.0.0.1:80
```
You will see something like picture below in your terminal

![14](https://user-images.githubusercontent.com/130447223/235972049-10dbe062-ec2d-44ba-bcc6-80a8c6d593eb.png)

Next, we verify that we can access web server through the web browser using the public IP address of the EC2 instance.
So, open a web browser of your choice and then enter the following url
(remember to replace contents within the Anchor Tabs < > as was instructed in the beginning.
```
http://<Public-IP-Address>:80
```  
This will display the default nginx server webpage
   
![15](https://user-images.githubusercontent.com/130447223/235972396-d99c3fe7-82cc-4804-881c-9aa54ec7c091.png)

**STEP 3: INSTALL MYSQL**
At this point we have successfully set up our web server,
we now need to set up a database management system to be able to store and manage data for our website in a relational database.
We will be using MySQL for this project. This is the most popular RDBMS used within PHP environments.

**Install mysql-server:**
```
  sudo apt install mysql-server -y
```  
![16](https://user-images.githubusercontent.com/130447223/235973031-792e1bcc-b89d-4851-ba74-d65ff86f5841.png)

**When the installation is finished, log in to the MySQL console as an administrative database user root by typing:**
``` 
# Login to MySQL
sudo mysql

# Set a password for the root user using mysql_native_password as default authentication method:
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Password@1';

#Exit MySQL shell
      exit
```
![17](https://user-images.githubusercontent.com/130447223/235974086-af25f1f1-d694-499c-881e-9a40b1edd570.png)

It is recommended that we run a security script that comes pre-installed with MySQL
to remove some insecure default settings and lock down access to your database system. 
This would be done by running an interactive script on the terminal.
```
# Start the interactive script by running:
sudo mysql_secure_installation
# You will be asked if you want to validate the password plugin, enter "yes":
y
# Enter 0 or 1 as the level of password validation. Note that for a production grade system the level should be very high.
1
# You will be shown the password strength, and you will be asked if you want to change the password for the root user.Select "Yes" to enter the root user password
Y
# You would be asked if you wish to continue with the password provided, select "Yes"
Y
# For the rest of the options select "Yes"
```
Test if you can log in to the MySQL console by typing:
```
# Test if the password was set successfully
```
![18](https://user-images.githubusercontent.com/130447223/235974325-a9f7c577-7c40-4eac-8e40-451e553cf1de.png)
![19](https://user-images.githubusercontent.com/130447223/235974397-c1d581c3-29f4-41da-98c6-042da6d3cb83.png)

sudo mysql -p

![20](https://user-images.githubusercontent.com/130447223/236061421-7783c812-f5c8-4c2a-a834-ea24a6e5c62d.png)

# Exit MySQL shell
exit


The -p flag would prompt you to enter your password. Enter your set password to proceed.
If you are able to access it, MySQL is now set up and secure. You can proceed to step 4 to install PHP.
  
STEP 4: Install PHP

We now have an Nginx server to serve content and MySQL to store and manage data. PHP is the component of our setup
that will process code to display dynamic content to the end user.
Unlike Apache2, nginx does not embed PHP interpreter in each request, it requires the installation of php-fpm,
which is an external program to handle PHP processing and to act as a bridge between the PHP interpreter and the web server.
Additionally, we need php-mysql to allow communication with the MySQL-based db. All core PHP packages would be installed by default.

The following line of code would Install php-fpm and php-mysql and confirm that PHP has been successfully installed by checking the version of PHP:
```
# Install php-fpm and php-mysql
sudo apt install php-fpm php-mysql -y
# Check php version
php -v
```
![21](https://user-images.githubusercontent.com/130447223/236061585-290c399e-d37f-4aca-a9e1-4f5a42075fe1.png)
![22](https://user-images.githubusercontent.com/130447223/236061710-8d170f61-08e6-437c-82dc-4531d6f0906b.png)

STEP 5: CONFIGURING NGINX TO USE PHP PROCESSOR

Similar to creating a virtual host in Apache, when using the nginx web server, we can create server blocks
to encapsulate configuration details and host more than one domain on a single server.
By default, on Ubuntu Nginx has a default server block set up and is configured to serve contents out of the directory /var/www/html.
We will be creating a new domain at this location to serve our web content. The domain name for the project would be lempstackproject.

The following block of code would create a new directory in the /var/www/ directory to serve as the root web directory for my domain,
assign ownership of the directory to the current system user, and create a new configuration file in the /etc/nginx/sites-available/ directory.

```
# Create root web directory
sudo mkdir /var/www/lempstackproject

# Assign ownership of the directory to the current system user
sudo chown -R $USER:$USER /var/www/lempstackproject

# Create configuration file in nginx sites-available directory using nano
sudo nano /etc/nginx/sites-available/lempstackproject

# Enter the following configurations into this file and save

#/etc/nginx/sites-available/lempstackproject

server {
    listen 80;
    server_name lempstackproject www.lempstackproject;
    root /var/www/lempstackproject;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```
![23](https://user-images.githubusercontent.com/130447223/236061843-0ab99332-373c-4f8b-b9a3-36b9d88d1e89.png)

After editing this file using nano, save and close the file by typing CTRL+X

In the above configuration:

Listen: Defines the port nginx is listening on
Server_name: Defines the domain name and/or ip addresses this server block should respond to.
Root: Defines the document root where the files served by this website are stored.
Index: Defines the order Nginx will prioritize index files for this website.
location: The first location block includes a try_files directive,
which checks for the existence of files or directories matching a URL request.
If Nginx cannot find the appropriate resource, it will return a 404 error.
location ~ \.php$ — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file
and the php7.4-fpm.sock file, which declares what socket is associated with php-fpm.
location ~ /\.ht — The last location block deals with .htaccess files, which Nginx does not process.
By adding the deny all directive, if any .htaccess files happen to find their way into the document root,
they will not be served to visitors.
Next, we will need to activate this configuration by linking it to the configuration file in nginx’s sites-enabled directory.
This tells Nginx to use this configuration the next time it is reloaded.
We will test this afterward to check the configuration for any syntax errors.
```
# Link configuration to nginx sites-available directory to activate configuration
sudo ln -s /etc/nginx/sites-available/lempstackproject /etc/nginx/sites-enabled/

# Test for syntax error:
sudo nginx -t
```
![24](https://user-images.githubusercontent.com/130447223/236061953-2409925f-fe39-408a-8032-4350b3ca553e.png)

Next, we need to disable the default Nginx host that is currently configured to listen on port 80 
and reload Nginx to apply the changes by running the below line of code:
```
# Disable default Nginx host
sudo unlink /etc/nginx/sites-enabled/default

# Reload nginx to apply changes
sudo systemctl reload nginx
```
The new website is now active, but the webroot at /var/www/lempstackproject is still empty.
Create an index.html file in that location so that we can test that the virtual host works as expected by running the following command:
```
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/lempstackproject/index.html
```
We can now access the website by entering the public IP address on our browser (i.e.,http://13.40.228.161/)
   
![25](https://user-images.githubusercontent.com/130447223/236062128-05d6fef5-1d4b-42d9-b9b3-e8ec6385810b.png)

EMP stack is now fully configured; next, we will create a PHP script to test that Nginx is able to handle .php files
within our newly configured website.

STEP 6: TESTING PHP WITH NGINX

LEMP stack is now fully installed and operational. we will now be testing it to validate
that Nginx can correctly handle .php files generated by the PHP processor.
First, we need to create a test PHP file in the document root directory and try to access the PHP page from our browser:
```
# Create test php file
sudo nano /var/www/lempstackproject/info.php

![26](https://user-images.githubusercontent.com/130447223/236062286-9e9a705e-e4d1-4808-a7b0-0f60cf4b769c.png)

# Insert the below text into this file, save, and close.
<?php
phpinfo();
```
Access this page in your browser by entering the public IP address or domain name you set on your configuration file, followed by /info.php into your browser (i.e.,http://13.40.228.161/info.php)

![27](https://user-images.githubusercontent.com/130447223/236062422-2d1e034a-ecb5-4f9a-8b2e-2f837b9628a5.png)

Remove this page after checking the relevant information:
```
sudo rm  /var/www/projectLEMP/info.php
```

STEP 7: RETRIEVING DATA FROM MYSQL DATABASE WITH PHP

We will create a test database with a simple To-Do list and configure access to it,
 so Nginx website would be able to query data from the database and display it.

We will be creating a new user with the mysql_native_password authentication method to enable us to connect to MySQL.
Ideally, we should use the native MySQL PHP library “mysqlnd” but this does not support “caching_sha2_authentication”,
which is the default authentication method for mySQL 8.
```
# connect to the MySQL console using the root account
sudo mysql -p

![28](https://user-images.githubusercontent.com/130447223/236062462-abb8abec-9d6b-4380-b3f7-f96d3e606c4a.png)

# create a new database called "example_database
mysql> CREATE DATABASE example_database;

# Create a new user and grant this user full privileges on the database 
mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'ExampleUser@1';

# Give this user permission over the example_database database
mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';

![29](https://user-images.githubusercontent.com/130447223/236062526-1b49b01e-f109-4d97-bbff-4a1e8bb7f3cb.png)

# Exit MySQL Shell
mysql> exit
```
Test if this new user has permission to access MySQL:
```
mysql -u example_user -p
```
![30](https://user-images.githubusercontent.com/130447223/236062717-dcd59e35-32a3-44fe-bee4-98b305b2a020.png)

Confirm that this user has access to example_database:
```
mysql> SHOW DATABASES;
```
![31](https://user-images.githubusercontent.com/130447223/236062793-95696995-fe2d-46c8-928e-022a47e3955c.png)

Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement:
```
#Set database
USE example_database;

# Create table "todo_list"
mysql> CREATE TABLE todo_list (
    -> item_id INT AUTO_INCREMENT,
    -> content VARCHAR(255),
    -> PRIMARY KEY(item_id)
    -> );

![32](https://user-images.githubusercontent.com/130447223/236062866-532250d8-49fa-4ec3-a6b9-c46b6adf3071.png)

# Insert a few records in the test table. Enter the below command multiple times incrementing the value
mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
mysql> INSERT INTO example_database.todo_list (content) VALUES ("My second important item");

# View this table
mysql>  SELECT * FROM example_database.todo_list;

![33](https://user-images.githubusercontent.com/130447223/236063015-f2541078-2481-47f9-977c-5020f104d9e3.png)

# exist MySQL Shell
mysql> exit
```
Now we will create a new PHP script in the custom web root directory that will connect to MySQL and query the content of this table.
```
# Create php script in the custom web root directory
nano /var/www/lempstackproject/todo_list.php

![34](https://user-images.githubusercontent.com/130447223/236063193-7b3c157d-537f-46e1-83ff-0434c85972eb.png)

# Enter the below text in the script and save:

<?php
$user = "example_user";
$password = "ExampleUser@1";
$database = "example_database";
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
Access this page in your web browser by visiting the domain name or public IP address configured for your website,
followed by /todo_list.php (i.e., http://13.40.228.161/todo_list.php)

![35](https://user-images.githubusercontent.com/130447223/236063263-cbae9607-3f25-4825-a59d-1d6e03dbc98b.png)

This implies your PHP environment is ready to connect to and interact with your MySQL server.

**Conclusion**

We have successfully deployed a LEMP stack on an AWS EC2 instance for serving PHP websites to visitors
using nginx as a web server and MySQL as a DBMS.


