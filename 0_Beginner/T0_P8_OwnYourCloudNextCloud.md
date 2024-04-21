### Install your personal cloud with NextCloud on Debian 10 | VPS Beginner | P8

Login as admin

```
sudo su
```

####  1. Update/Upgrade

Update list of packages

```
apt update
```

Install packages

```
apt upgrade
```

####  2. Install Apache

Install apache with php module

```
apt install apache2 libapache2-mod-php bzip2 -y
```

Start apache and enable it at boot

```
systemctl start apache2
systemctl enable apache2
```

####  3. Install MariaDb

Make sur MariaDb is installed

```
apt install mariadb-server php-mysql -y
```

If not done run security wizard

```
mysql_secure_installation
```

Answer questions with your own password

```
Enter current password for root (enter for none): Enter
Set root password? [Y/n]: Y
New password: <your password>
Re-enter new password: <your password>
Remove anonymous users? [Y/n]: Y
Disallow root login remotely? [Y/n]: Y
Remove test database and access to it? [Y/n]: Y
Reload privilege tables now? [Y/n]: Y
```

####  4. Install php

Install php with extensions

```
apt install php php-gd php-mbstring php-dom php-curl php-zip php-simplexml php-xml -y
apt install php7.3-fpm php7.3-gd php7.3-mysql php7.3-curl php7.3-xml php7.3-zip php7.3-intl php7.3-mbstring php7.3-bz2 php7.3-json php-apcu
```

####  5. NextCloud files to directory

Find out latest nextcloud version (at the moment 20.0.4) at https://nextcloud.com/install/#instructions-server and download it with

```
wget https://download.nextcloud.com/server/releases/nextcloud-<version>.tar.bz2
```

The file is compressed, decompress it to cloud directory

```
tar -xjf nextcloud-18.0.4.tar.bz2 -C /var/www/cloud
```

Make apache owner and give access rights to folder 755 (read and execute access for everyone and also write access for the owner of the file)

```
chown -R www-data:www-data /var/www/
chmod -R 755 /var/www/
```

####  6. Create a database for NextCloud

Sign in to MySql with your root password if you have set one

```
mysql -u root -p
```

Create a database for NextCloud

```
MariaDB> CREATE DATABASE cloud;
```

Create a user with access rights to the database

```
MariaDB> GRANT ALL ON cloud.* TO 'cloud_user'@'localhost' IDENTIFIED BY '<example-password>';
MariaDB> FLUSH PRIVILEGES;
MariaDB> EXIT;
```

####  7. Install NextCloud

```
<VirtualHost *:80>
 DocumentRoot "/var/www/nextcloud"
 ServerAlias cloud.rumo.xyz
 ServerName cloud.rumo.xyz

 ErrorLog ${APACHE_LOG_DIR}/error.log
 CustomLog ${APACHE_LOG_DIR}/access.log combined

<Directory /var/www/nextcloud/>
 Options +FollowSymlinks
 AllowOverride All

 SetEnv HOME /var/www/nextcloud
 SetEnv HTTP_HOME /var/www/nextcloud
 Satisfy Any

</Directory>
RewriteEngine on
RewriteCond %{SERVER_NAME} =cloud.rumo.xyz
RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
</VirtualHost>

```

Connect to the URL of your cloud and install it through your browser.