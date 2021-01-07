---
title: 'WordPress on Amazon EC2???'
layout:   post
category: tutorial-tips
tags:     [wordpress, ssl, ec2]
feature:  /assets/img/multiple-db-pic1.png
---
You will director install your Wordpress to ec2 with Ubuntu 18.04 instance with Apache2 web server, PHP 7.2 and MySQL server.
WordPress is open-source software and most popular CMS (content management system) platform used by many developers and entrepreneurs around the world to run their blogs and fully functioning websites.
<!--more-->

# What will you do?
1. Installing Apache, PHP, Mysql Server
2. Changing Ownership and Permission
3. Installing WordPress on Server
3. Configure MySQL database
4. Configure WordPress Security Key
5. Creating Apache Virtual Host
6. User Interface WordPress Configuration

# Requirements
- AWS Account. Create your own AWS Account
- Amazon EC2 Ubuntu 18.04. Learn how to deploy Ubuntu 18.04 on AWS
- A user with sudo privilege command.
- To get started, this guide will show you through step process on how to install latest WordPress on Ubuntu 18.04 server along with Apache 2.4, PHP 7.2, and latest Mysql Server.

# Start Installing
## Step 1. SSH Remote
Firstly, we assume that your EC2 Ubuntu server has been launched on the AWS console. In this case, Open your favorite terminal console and connect to the remote server using SSH, type command:
```ruby
ssh -i yourfile.pem ubuntu@EC2_IP
``` 

## Step 2. Installing Apache Web Server
To begin the installation of Apache, use the following commands:
```ruby 
sudo add-apt-repository ppa:ondrej/apache2
sudo apt update
sudo apt-get install apache2 -y
```

## Step 3. Installing PHP 7.2 for WordPress
WordPress has made of using PHP language, which makes PHP very important language in WordPress websites to enable static and dynamic content. 

To install latest PHP version, add the PHP (PPA package) below using:

```ruby
sudo add-apt-repository ppa:ondrej/php
While adding the PPA packages, Press [ENTER] on the prompt to continue.
...
# LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php
 More info: https://launchpad.net/~ondrej/+archive/ubuntu/php
Press [ENTER] to continue or Ctrl-c to cancel adding it.
```

after add respo, you need to update and install PHP 7.2, type command below:
```ruby
sudo apt update
sudo apt install -y php7.2
```

Apart from this, you need to install the following PHP modules that required for your WordPress application.
```ruby 
sudo apt install -y libapache2-mod-php7.2 php7.2-common php7.2-mbstring php7.2-xmlrpc php7.2-gd php7.2-xml php7.2-mysql php7.2-cli php7.2-zip php7.2-curl php-imagick
```

After installation completed, you will make some changes from php.ini configuration file, run command:
```ruby 
sudo nano /etc/php/7.2/apache2/php.ini
```

Set value like below, you can use ctrl + w to find faster
```ruby
file_uploads = On
allow_url_fopen = On
memory_limit = 256M
upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 360
```

## Step 4. Installing MySQL Server for WordPress
To install MySQL Server, type the following command below:

```ruby
sudo apt update
sudo apt-get install mysql-server -y
```


After the installation completed, it’s time to create database, you can flexible to use command below:

```ruby
mysql -u root 
CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON * . * TO 'newuser'@'localhost';
FLUSH PRIVILEGES;

DROP USER 'username'@'localhost'; #use it when you want to drop

# create database
create schema 'database_name'
```

## Step 5. Installing latest WordPress
To install latest WordPress, using the command below:
```ruby
cd /tmp && wget https://wordpress.org/latest.tar.gz
tar -zxvf latest.tar.gz
sudo mv wordpress /var/www/wordpress
cd /var/www/wordpress/
```

Create a .htaccess file, type command:

```ruby
sudo nano .htaccess
```

Add following lines into the .htacess file.
```ruby
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
</IfModule>
```

Next, Rename the wp-config-sample.php using the following command:
```ruby
sudo mv wp-config-sample.php wp-config.php
```

## Step 6. Changing Ownership and Permission for WordPress files
To change WordPress ownership, www-data is username and ubuntu is groupname, type command:
```ruby
sudo chown -R www-data:ubuntu /var/www/wordpress
```

Next, Change the right permission for the particular files and directories, type command:
```ruby
sudo find /var/www/wordpress/ -type d -exec chmod 755 {} \;
sudo find /var/www/wordpress/ -type f -exec chmod 644 {} \;
```

Apart from this, set the following important files to chmod 600 so that only the owner can fully read and write access to these files.
```ruby
sudo chmod 600 /var/www/wordpress/wp-config.php
sudo chmod 600 /var/www/wordpress/.htaccess
```

However, If you want to verify the ownership and permission for your WordPress files and directories, type command:
```ruby
ls -lah
-rw-------  1 www-data ubuntu 420 Nov 30  2017 .htaccess
-rw-r-----  1 www-data ubuntu 420 Nov 30  2017 index.php
-rw-r-----  1 www-data ubuntu 19935 Jan  1  2019 license.txt
-rw-r-----  1 www-data ubuntu 7447 Apr  8 22:59 readme.html
-rw-r-----  1 www-data ubuntu 6919 Jan 12  2019 wp-activate.php
drwxr-x---  9 www-data ubuntu 4096 Sep  5 01:08 wp-admin/
-rw-r-----  1 www-data ubuntu 369 Nov 30  2017 wp-blog-header.php
-rw-r-----  1 www-data ubuntu 2283 Jan 21  2019 wp-comments-post.php
-rw-r-----  1 www-data ubuntu 2898 Jan  8  2019 wp-config-sample.php
-rw-------  1 www-data ubuntu 2898 Sep 15 04:36 wp-config.php
drwxr-x---  4 www-data ubuntu 4096 Sep  5 01:08 wp-content/
-rw-r-----  1 www-data ubuntu 3847 Jan  9  2019 wp-cron.php
drwxr-x--- 20 www-data ubuntu 12288 Sep  5 01:08 wp-includes/
-rw-r-----  1 www-data ubuntu 2502 Jan 16  2019 wp-links-opml.php
-rw-r-----  1 www-data ubuntu 3306 Nov 30  2017 wp-load.php
-rw-r-----  1 www-data ubuntu 39551 Jun 10 13:34 wp-login.php
-rw-r-----  1 www-data ubuntu 8403 Nov 30  2017 wp-mail.php
-rw-r-----  1 www-data ubuntu 18962 Mar 28 19:04 wp-settings.php
-rw-r-----  1 www-data ubuntu 31085 Jan 16  2019 wp-signup.php
-rw-r-----  1 www-data ubuntu 4764 Nov 30  2017 wp-trackback.php
-rw-r-----  1 www-data ubuntu 3068 Aug 17  2018 xmlrpc.php
```

## Step 7. Setup MySQL Database in WordPress Configuration File
In this case, type command:
```ruby
sudo nano /var/www/wordpress/wp-config.php
```

Therefore, look for the MySQL database section and define the following values, similar to this:
```ruby
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'database_name_here' );

/** MySQL database username */
define( 'DB_USER', 'username_here' );

/** MySQL database password */
define( 'DB_PASSWORD', 'password_here' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );

/** Database Charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );
```

## Step 8. Setup WordPress Security Key
To grab your own WordPress Security Key, type following command on your terminal console:
```ruby
curl -s https://api.wordpress.org/secret-key/1.1/salt/
```

The output will response unique values, similar to this:
```ruby
define('AUTH_KEY',         'e-9mU8FzzyD7;89|;d5!}5vR-<KGlV:[C:El4XeJG7!D9@ V5p-:F`oPeQ$`;WVN');
define('SECURE_AUTH_KEY',  '$3|%C9X:!67+N=w|brb*[`C:QQODY-qg=NLTl@+/A)Iuxg< |=HOsaL4-4DFu:k<');
define('LOGGED_IN_KEY',    'C<g>(!)=rlBuQ%a]}~HX1/Z=,$p^WVaj )nU0!KVyE?PKom}T0j`D&S9<WQx([5i');
define('NONCE_KEY',        '!1,gPH:m;o~mNB(Uu+BOAxT9}fZKcOVWs^y1Yb:+X;0[VV4d42xh/|Ocawt@@k9+');
define('AUTH_SALT',        'LzXAtk+AL~G*Fj.DEANk%THZf@eul+jpv1A_u59`e:AH;aRL=gI9J@BpY{M[ON|h');
define('SECURE_AUTH_SALT', 'yiHB&2#e%7b,QTH33[O1u}R?FC97LLu2+ma!oH`40T}W-l}+N-=<VmAJ04a|mx14');
define('LOGGED_IN_SALT',   '=AINX^{7lb/$j|HjCOR)^)XBl[s:I(@B!}Wez7s-On5kY)x=an-J~SeLB7:M/K.]');
define('NONCE_SALT',       'Rlgt+LcGgcMFhFo&FuEB8KfZgS7_Ml]=5&sJN!k#&J3o6GFeFx%7T|aLH@L_NiEw');
```

open the WordPress configuration file and replace all line, type command:
```ruby
sudo nano /var/www/wordpress/wp-config.php #then replace all line above
```

## Step 9. Creating Apache Virtual Host for WordPress
To add an Apache virtual host for WordPress, type command: 
```ruby
cd /etc/apache2/sites-available
```

Next, Disable the default configuration file, type command:
```ruby
sudo a2dissite 000-default.conf
```

Then reload the Apache2 configuration file.
```ruby
sudo systemctl reload apache2
# sudo service apache2 restart
```

Now, Create a new WordPress virtual host configuration file, type command:
```ruby
sudo nano wordpress.conf
```

the following virtual host configuration file below and change the domain name and also the path location for root document directory.
```ruby
<VirtualHost *:80>
     ServerAdmin admin@example.com
     DocumentRoot /var/www/wordpress
     ServerName example.com
     ServerAlias www.example.com

     <Directory /var/www/wordpress/>
        Options +FollowSymlinks
        AllowOverride All
        Require all granted
     </Directory>

     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Next, Enable the virtual host configuration file to available on public access, type command:
```ruby
sudo a2ensite wordpress.conf
```

Also, Enable the Apache rewrite module to enable .htaccess file on rewrite modules , type command:
```ruby
sudo a2enmod rewrite
```

Next, Verify your WordPress virtual host configuration using:
```ruby
sudo apache2ctl configtest
```

The output similar to this:
```ruby
Syntax OK 
```

Therefore, If everything is fine. Reload your Apache2 service to implement the changes, type command:
```ruby
sudo service apache2 restart
```

Above all, this section completes the backend setup. We are almost done, now continue on step 10.
# Node:
- remember to create database, username and password
- reading error of apache2 in /var/log/apache2/error.log
- you can config route 53 to ec2
- dont forgot open inbound of ec2 to open your wordpress
