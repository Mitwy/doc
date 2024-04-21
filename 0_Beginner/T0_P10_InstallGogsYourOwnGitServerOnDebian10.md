[![T0_P10](https://img.youtube.com/vi/5ysGZoBJGVU/0.jpg)](https://www.youtube.com/watch?v=5ysGZoBJGVU)
### Install Gogs your own git server on Debian 10 | VPS Beginner | P10
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
####  4. Install git
```
apt-get install git
```
####  5. Download latest release of Gogs
Check version at https://gogs.io/docs/installation/install_from_binary
```
# move to git folder
cd /var/www/git
# download file
wget https://dl.gogs.io/0.12.3/gogs_0.12.3_linux_amd64.tar.gz
```
The file is compressed, decompress it with
```
tar xzvf latest.tar.gz
```
#### 6. Update vhost
Add reversee proxy to /etc/apache/sites-enable/git.conf (or your vhost for your gogs)
```
ProxyPreserveHost On
ProxyRequests off
ProxyPass / http://127.0.0.1:3000/
ProxyPassReverse / http://127.0.0.1:3000/
```
And remove DocumentRoot
####  7. Enable proxy modes for Apache
Enable proxy modules for apache
```
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod proxy_balancer
```
Restart apache
```
systemctl restart apache2
```
####  8. Change Gogs configuration

Create directory and file

```
mkidr /var/www/gogs/custom
mkidr /var/www/gogs/custom/conf
vim custom/conf/app.ini
```

Change configuration in custom/conf/app.ini 

```
[server]
HTTP_ADDR        = 127.0.0.1
EXTERNAL_URL     = <Put the url of your gogs service hear>
```

####  

####  9. Create a database for Gogs

Sign in to MySql with your root password if you have set one
```
mysql -u root -p
```
Create a database for Gogs
```
MariaDB> CREATE DATABASE gogs;
```
Create a user with access rights to the database
```
MariaDB> GRANT ALL ON gogs.* TO 'gogs_user'@'localhost' IDENTIFIED BY 'example-password';
MariaDB> FLUSH PRIVILEGES;
MariaDB> EXIT;
```
####  10. Install gogs
execute gogs with

```
 var/www/git/gogs web.
```

The go in your browser to install gogs. Set database access and user that started "var/www/git/gogs web."

Make apache owner and give access rights to folder 755 (read and execute access for everyone and also write access for the owner of the file)

```
chown -R www-data:www-data /var/www/gogs
chmod -R 755 /var/www/gogs
```
####  11. Set https
Change configuration in custom/conf/app.ini for example
```
certbot --apache
```
####  12. Create systemctl service to run on start
in
```
vim /etc/systemd/system/gogs.service
```
Past

```
[Unit]
Description=Gogs
After=syslog.target
After=network.target
After=mariadb.service mysqld.service postgresql.service memcached.service redis.service

[Service]
# Modify these two values and uncomment them if you have
# repos with lots of files and get an HTTP error 500 because
# of that
###
#LimitMEMLOCK=infinity
#LimitNOFILE=65535
Type=simple
User=root
Group=sudo
WorkingDirectory=/var/www/gogs
ExecStart=/var/www/gogs/gogs web
Restart=always
Environment=USER=root HOME=/home

# Some distributions may not support these hardening directives. If you cannot start the service due
# to an unknown option, comment out the ones not supported by your version of systemd.
ProtectSystem=full
PrivateDevices=yes
PrivateTmp=yes
NoNewPrivileges=true

[Install]
WantedBy=multi-user.target

```
Enable service with 
```
systemctl enable gogs.service
```
Start service with 
```
systemctl start gogs.service
```
