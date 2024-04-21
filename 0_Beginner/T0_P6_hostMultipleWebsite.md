###  How to host multiple websites and services with Apache | VPS Beginner | P6

You need to have the domains/subdomains already created on your DNS provider. For this tutorial I have the following subdomains:

- cloud.mitwy.com
- git.mitwy.com
- chat.mitwy.com
- shop.mitwy.com

The LAMP stack should be installed, if not please check "LAMP and how to install it on Linux Debian".

**Log to VPS using putty and your SSH key**
Log in as admin
```
sudo su
```
**Create directories for the subdomains**
Move to /var/www

```
cd /var/www
```
Create directories
```
mkdir cloud
mkdir git
mkdir chat
mkdir shop
```
**Create index.html/index.php files in each directories of each subdomain**
For subdomain chat.mitwy.com, move to folder /var/www/chat
```
cd /var/www/chat
```
Create and open file index.php
```
vim index.php
```
click "INSERT" and add to the file
```
<?php phpinfo(); ?>
```
Click "ESC", type ":wq", type enter (:wq to save and exit)

Do the same for every subdomains. With different content in the file "index.php".
**Create virual hosts**
move to apache sites-enabled

```
cd /etc/apache2/sites-enabled
```
copy default vhost 
```
cp 000-default.conf chat.conf
```
open file chat.php
```
vim chat.php
```
click "INSERT"
change DocumentRoot path to subdomain directorie "/var/www/chat"
change ServerName to one of your subdomain, for this exmaple I will use "chat.mitwy.com"
click "ESC", type ":wq", type enter (:wq to save and exit)
enable host for apache

```
a2ensite chat.conf
```
reload apache
```
systemctl reload apache2
```
At this point you should see php informaiton if you type in your browser your subdomain.

Now create virtual hosts  and enable them for all other subdomains.