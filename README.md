# LAMP-stack-basic-implementation
## Pre-requisites

### Setting Up AWS EC2 Instance with Ubuntu Server

To complete this project, you will need an AWS account and a virtual server with Ubuntu Server OS.

### AWS Account Setup

1. Register a new AWS account.
2. Launch a new EC2 instance in your preferred region using the t2.micro family and Ubuntu Server 20.04 LTS (HVM).

**IMPORTANT:** Save your private key (.pem file) securely and **do not share it**.

### Connecting to the EC2 Instance

#### MAC/Linux Users

1. Open Terminal.
2. Use the same key downloaded from AWS; no need to convert it.
3. Change directory to the location of your PEM file (likely in the Downloads folder):
```cd ~/Downloads```
4. Change permissions for the private key file (.pem):
```sudo chmod 0400 <private-key-name>.pem```
5. Connect to the instance:
```ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>```

#### AWS Free Tier Limits

- You can use 750 hours (31.25 days) of t2.micro server per month for the first 12 months **FOR FREE**.
- Stop your EC2 instance when not in use.
- By default, there is a soft limit of maximum 5 running instances at the same time.
- Note: Every time you stop and start your EC2 instance, you will have a new IP address. Update your SSH credentials accordingly.

#### STEP 1 - INSTALLING APACHE AND UPDATING THE FIREWALL

Apache is a widely used web server software known for its speed, reliability, and security. It can be customized using extensions and modules.

To install Apache using Ubuntu's package manager 'apt':

```bash
sudo apt update
sudo apt install apache2
```

Verify that Apache is running:

```bash
sudo systemctl status apache2
```

Open TCP port 80 to allow web traffic:

- Open inbound port 80 in EC2 configuration.

To access the server locally, run:

```bash
curl http://localhost:80
```

To access the server from the internet, use your public IP address:

```bash
curl http://<Public-IP-Address>:80
```

To check the public IP address from the EC2 instance:

```bash
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```

If the server is correctly installed, you will see the Apache Ubuntu Default Page in the browser.

#### STEP 2 - INSTALLING MYSQL

Install the MySQL Database Management System (DBMS) to store and manage data for your site.

To install MySQL using 'apt':

```bash
sudo apt install mysql-server
```

Log in to the MySQL console:

```bash
sudo mysql
```

Set a password for the root user:

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```

Exit the MySQL shell:

```mysql
exit
```

Run the interactive script for security configuration:

```bash
sudo mysql_secure_installation
```

Configure password validation and set a strong password for the root user.

To test the login, use:

```bash
sudo mysql -p
```

Exit the MySQL console:

```mysql
exit
```
Note: MySQL PHP library mysqlnd may not support caching_sha2_authentication, so configure database users to use mysql_native_password.

#### STEP 3 - INSTALLING PHP

Install PHP and its modules for dynamic content processing and communication with MySQL-based databases.

To install PHP, php-mysql, and libapache2-mod-php, run:

```bash
sudo apt install php libapache2-mod-php php-mysql
```

Confirm your PHP version:

```bash
php -v
```

#### STEP 4 - CREATING A VIRTUAL HOST WITH APACHE

Create a virtual host for your website using Apache. Replace 'projectlamp' with your desired domain.

Create the projectlamp directory:

```bash
sudo mkdir /var/www/projectlamp
```

Assign ownership of the directory:

```bash
sudo chown -R $USER:$USER /var/www/projectlamp
```

Create and open a new configuration file:

```bash
sudo vi /etc/apache2/sites-available/projectlamp.conf
```

Paste the following configuration:

```apache
<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Save and close the file. Enable the virtual host:

```bash
sudo a2ensite projectlamp
```

(Optional) Disable the default website:

```bash
sudo a2dissite 000-default
```

Check the configuration file for syntax errors:

```bash
sudo apache2ctl configtest
```

Reload Apache:

```bash
sudo systemctl reload apache2
```

Test your virtual host by accessing the website URL:

```
http://<Public-IP-Address>:80
```

If successful, you will see the text you added in the `index.html` file. You can also access the website using the public DNS name.

```bash
http://<Public-DNS-Name>:80
```

Note: Remember to remove or rename the `index.html` file once you set up an `index.php` file for your application.

#### STEP 5 - ENABLE PHP ON THE WEBSITE

To prioritize index.php over index.html as the landing page, modify the `dir.conf` file:

```bash
sudo vim /etc/apache2/mods-enabled/dir.conf
```

Change the order of `index.php` within the `DirectoryIndex` directive:

```
DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
```

Save and close the file. Reload Apache:

```bash
sudo systemctl reload apache2
```

Create a PHP test script:

```bash
vim /var/www/projectlamp/index.php
```

Add the following PHP code to the file:

```php
<?php
phpinfo();
```

Save and close the file. Refresh the page to see the PHP info.
```







