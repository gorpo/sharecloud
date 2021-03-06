# Installation Guide

You need to setup an Webserver environment first. 

## Installation

### Note
This installation guide was created on an ArchLinux machine. This guide is only valid for ArchLinux installations.

We assume that the packages `httpd`, `php`, `mysql` or `mariadb` have already been installed and configured.

As we are logged in as root, we will perform all actions as `http` user, so we won't have any problems
with access control when using Apache. If you want to perform all action in the context of the current
user, simply remove `sudo -u http -H` from all shell commands.

### Get the source code

	$ cd /srv/http
	$ sudo -u http -H git clone https://github.com/sharecloud/sharecloud.git sharecloud/
	
You are then at the master-branch, which is supposed to have the latest stable code. If you want to use
the current developer branch:

	$ sudo -u http -H git checkout dev
	
Let's switch to the sharecloud directory:

	$ cd sharecloud/
	
### Database
Now, we create a new database for your local sharecloud instance.

Make sure, you have secured your MySQL installation:

	$ sudo mysql_secure_installation

#### Database with own user

	$ mysql -u root -p
	
Type the database root password
	
	mysql> CREATE USER sharecloud@localhost IDENTIFIED BY '{$password}';

Where `{$password}` should be substituted with a proper password.

	mysql> CREATE DATABASE IF NOT EXISTS `sharecloud` DEFAULT CHARACTER SET `utf8` COLLATE `utf8_unicode_ci`;
	mysql> GRANT SELECT, LOCK TABLES, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER ON `sharecloud`.* TO sharecloud@localhost;
	mysql> \q

	
Let's test the connection:

	$ mysql -u sharecloud -p -D sharecloud
	
Type the password you have set earlier

You should see a 'mysql>' prompt now.

	mysql> \q

#### Database without own user
Run:

	$ mysql -u root -p
	mysql> CREATE DATABASE IF NOT EXISTS `sharecloud` DEFAULT CHARACTER SET `utf8` COLLATE `utf8_unicode_ci`;

### Setup Smarty

	$ sudo -u http -H chmod 700 classes/smarty/templates_c
	
### Setup config.php

	$ sudo -u http -H cp system/config.php.example system/config.php
	$ sudo -u http -H nano system/config.php

Modify `config.php` to fit your needs!

### Run installation
Now open a browser and navigate to `http://YOUR_HOST/sharecloud/install/` and follow the steps there.

### Post-installation clean-up
For security reasons, you should remove the `install` and `upgrade` folders:

	$ sudo -u http -H rm -rf install/
	$ sudo -u http -H rm -rf upgrade/

### Install optional dependencies

For best experience you should install `imagick`, the PHP-RAR-Extension, and the PHP-exif-Extension.

	$ pacman -S imagemagick php-pear librsvg
	$ pecl install rar imagick

Now ensure following extensions are enabled (=uncommented (=without ';')) in your `php.ini`

	extension=exif.so
	extension=zip.so
	extension=pdo_mysql.so

if not already done.
Additional add following lines to you `php.ini`:

	extension=rar.so
	extension=imagick.so
	
Now you have to restart httpd with following lines:
	
	systemctl restart httpd.service
