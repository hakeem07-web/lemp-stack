# Deploying a LEMP Stack on AWS
Introduction
What is a Web stack?

A web stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software.
A web stack is usually comprised of: • Operating system • Database application • Programming language • Web server
Now that we have defined what a webstack is. Now let’s dive into the type of stack we’ll be using in this project(LEMP Stack).

LEMP is an open-source web application stack used to develop web applications.

**LEMP stands for**

• L- Linux Operating System
• E- Nginx Server
• M- MySQL Database
• P- PHP

Linux: The operating system. It is a free and open source operating system (OS) that has been around since the mid-1990s. Linux is popular in part because it offers more flexibility and configuration options than some other operating systems.

Nginx Server: The web server. The Nginx web server processes requests and serves up web assets via HTTP so that the application is accessible to anyone in the public domain over a simple web URL. It is developed and maintained by an open community, Nginx is a mature, feature-rich server that runs a large share of the websites currently on the internet.

MySQL: The database. MySQL is an open source relational database management system for storing application data. With My SQL, you can store all your information in a format that is easily queried with the SQL language. SQL is a great choice if you are dealing with a business domain that is well structured, and you want to translate that structure into the backend.

PHP: The programming language. The PHP open source scripting language works with Apache to help you create dynamic web pages. You cannot use HTML to perform dynamic processes such as pulling data out of a database. If you prefer, you can swap out PHP in favor of Perl or the increasingly popular Python language.

**How each technology interacts**

At a high-level overview the order of execution shows how the elements interoperate.
The process begins when the Nginx web server receives requests for web pages from a user’s browser. If the request is for a PHP file, Nginx passes the request to PHP, which loads the file and executes the code contained in the file. PHP also communicates with MySQL to fetch any data referenced in the code.

PHP then uses the code in the file and the data from the database to create the HTML that browsers require to display web pages. The LEMP stack is efficient at handling not only static web pages, but also dynamic pages where the content may change each time it is loaded depending on the date, time, user identity and other factors.

After running the file code, PHP then passes the resulting data back to the Nginx web server to send to the browser. It can also store this new data in MySQL. And of course, all of these operations are enabled by the Linux operating system running at the base of the stack.

#<ins>**Pre-requisites**</ins>

1.Basic Linux command line knowledge
2.Access to a terminal
3.Basic knowledge of Vim editor
4.Create an AWS Free Tier account 
5.Basic SSH knowledge
6.Set up AWS CLI
7.Knowledge of how to launch an EC2 instance on AWS and connect to this instance 

**Step one: Launch a Virtual Server with Ubuntu Server OS**
The operating system of our LAMP stack is a Linux server and we need a Linux machine with the right specifications to successfully deploy our LEMP stack. 
Sign in to the AWS Management Console as an IAM user
Navigate to the EC2 dashboard by typing **“EC2”** on the search bar and clicking on EC2 to go to the EC2 dashboard.
Click on the **“Launch Instance”** button
Give your instance name (E.g., MyLinuxinstance)
Choose an Amazon Machine Image (AMI) for your instance. The AMI is a pre-configured virtual machine image that contains the operating system and other software required for your application. For this project, we will be launching an **Ubuntu instance.** Type **“Ubuntu”** on the search bar to show all available Ubuntu AMIs. Select “Ubuntu Server 20.04 LTS (HVM), SSD Volume Type, 64-bit (x86)” which is free tier eligible.
On the Instance Type, select “t2.micro”, this is free tier eligible.
Select a key pair from your existing keypairs for the region you are in. If you don’t have an existing keypair, click on “Create new key Pair” to create a new key pair.
Enter a name for your key pair
if you are using a MacOS or Windows 10 machine, select “RSA” as the key pair type and “.pem” as the Private key file format. Otherwise, select “.ppk” which works with PuTTY.
Click on “Create key pair” to create a new key pair. Note that key Pairs are bound to a region.
8. Under “Network settings”, under “Firewall (Security Groups)”, click on “Create security group”

9. Ensure **“Allow SSH traffic from”** is checked. This would enable us to connect to our ec2 instance from our local machine via the ssh client.

10. Keep all other default configurations and click on “launch instance” and wait a little while for the launch to complete. The instance will show under “Instances” in a running state.
We have successfully launched an Ubuntu ec2 instance to the server as our operating system, next we will connect to this instance to set up all other components of our LAMP stack.

**Connecting to our EC2 instance**

Connecting to our EC2 instance

**IMPORTANT-** Anywhere you see these anchor tags < > , going forward, it means you will need to replace the content in there with values specific to your situation. For example, if you see <private-key-name>.pem. This will change to private-key.pem where private-key is the name of your own private key.

Now we move to the terminal in your linux OS.

-The terminal is already installed by default. You just need to open it up.
-We need the .pem key file which we saved from our EC2 downloaded from AWS.
-Change directory into the loacation where your PEM file is. Most likely will be in the Downloads folder

cd ~/Downloads



cd ~/Downloads
