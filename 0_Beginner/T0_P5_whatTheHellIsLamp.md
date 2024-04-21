[![T0_P5](https://img.youtube.com/vi/-urt0Dxq1IU/0.jpg)](https://www.youtube.com/watch?v=-urt0Dxq1IU)
###  LAMP and how to install it on linux debian 10 | VPS Beginner | P5

**Log to VPS using putty and your SSH key**

log in as admin
```
sudo su
```
**Update/Upgrade**
```
apt update
apt upgrade
```
**Install apache**
```
apt install apache2
```
**install maria db**
```
apt install mariadb-server
```
**remove unsecure access**
```
mysql_secure_installation
```
**log into Maria DB and check version**
```
mysql -u root -p
```
**install php**
```
apt install php libapache2-mod-php php-mysql
```
**check php version**
```
php -v
```
**enable php for apache2 (should be already enabled)**
```
a2enmod php7.3
```
**restart apache2**
```
systemctl restart apache2
```
