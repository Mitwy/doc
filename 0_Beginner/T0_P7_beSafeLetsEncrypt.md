[![T0_P7](https://img.youtube.com/vi/RnU4nXGxDlc/0.jpg)](https://www.youtube.com/watch?v=RnU4nXGxDlc)
###  How to get a free SSL certificate with let's encrypt | VPS Beginner | P7

**Log to VPS using putty and your SSH key**

log in as admin
```
sudo su
```
**Install snap and cerbot**

You can find how to install it on https://certbot.eff.org/.

For Debian 10 and Apache.

Install snap.
```
apt install snapd
```
Make sure snap is the latest version
```
snap install core
snap refresh core
```
Install cerbot
```
snap install --classic certbot
```
Prepare the Cerbot command
```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
Set cerbot to run with apache
```
certbot --apache
```
You will be asked for your e-mail address. It will send you an e-mail if you need to renew your certificate.

You can then choose the domain for which you would like to activate HTTPS.
