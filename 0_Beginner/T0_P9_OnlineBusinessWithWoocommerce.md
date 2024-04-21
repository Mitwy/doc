### Install your free online store with Woocommerce on Wordpress | VPS Beginner | P9

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
apt install apache2 libapache2-mod-php -y
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
apt install php7.3-curl php7.3-gd php7.3-mbstring php7.3-xml php7.3-xmlrpc php7.3-soap php7.3-intl php7.3-zip
```

####  5. Download WordPress

```

wget https://wordpress.org/latest.tar.gz
```

The file is compressed, decompress it to cloud directory

```
tar xzvf latest.tar.gz
```

Make apache owner and give access rights to folder 755 (read and execute access for everyone and also write access for the owner of the file)

```
chown -R www-data:www-data /var/www/shop
chmod -R 755 /var/www/shop
```

####  6. Create a database for WordPress

Sign in to MySql with your root password if you have set one

```
mysql -u root -p
```

Create a database for WordPress

```
MariaDB> CREATE DATABASE wp_db;
```

Create a user with access rights to the database

```
MariaDB> GRANT ALL ON wp_db.* TO 'wp_db_user'@'localhost' IDENTIFIED BY 'example-password';
MariaDB> FLUSH PRIVILEGES;
MariaDB> EXIT;
```

####  7. Generate salts

Generate unique salts (used to encrypte data)

```
curl -s https://api.wordpress.org/secret-key/1.1/salt/
# past salt in/var/www/wordpress/wp-config.php
vim /var/www/wordpress/wp-config.php
# also add DB_NAME, DB_USER and DB_PASSWORD
```

####  8. Install WordPress

Connect to the URL of your shop and install it through your browser.

####  9. Install Woocommerce

WordPress Dashboard and go to: Plugins > Add New.