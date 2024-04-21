### How to backup your WordPress database and files | VPS Intermediate | P0

Create new git user on Gogs for backups

Create a git repo for your shop on your VPS

Save git credential to store (to not have to type it), it will be save on next push

```
# https://git-scm.com/docs/git-credential-store
git config credential.helper store
```

####  Push WordPress from your VPS to git repo your 

Make sur file .gitignore doesn't contain app/config or other name that would be needed for your website to work.

Make a bash script to backup db and push everything to git

```
# content of push.sh
mysqldump mitwy > mitwy.sql
git add --all .
git commit -am "backup"
git push origin master
```
Make crone job to run script every day, push to git
```
crontab -e

# Add at the end of the file to run every day at midnight 
0 0 * * * cd /var/www/mitwy && sh /var/www/mitwy/push.sh
```

#### Pull git from your local RasPi

Pull data from git repo and copy it in Storage disk with a new folder called by date.

Make sure python is installed with git package

```
sudo apt update

sudo apt install python-pip
sudo apt install python3-pip

pip install gitpython
pip3 install gitpython
```
Script tested in **python 2.7** to pull data from git and create backup.

```
import git
import shutil
import os
import time

print('Pull start')

max_dir = 3
storage_path = "/mnt/storage/Mitwy/shop-backup/"

# Pull from git
repo = git.Repo('./')
current = repo.head.commit
repo.remotes.origin.pull()

if current != repo.head.commit:
    print("It changed")
else:
    print("Up to date")

# Move directory to storage
dst = storage_path + time.strftime("/%Y%m%d")
shutil.copytree("./",dst)

# If more than x clean oldests
bcDirs = os.listdir(storage_path)
bcDirs = sorted(bcDirs)

i = 0
for bcDir in reversed(bcDirs):
    if i >= max_dir:
        dir_path = storage_path + bcDir
        try:
            shutil.rmtree(dir_path)
        except Exception as e:
            print("Error: %s : %s" % (dir_path, e.strerror))
    i=i+1;

print('Pull end')


```
 The script is then run in crone task daily
```
crontab -e

# Add at the end of the file to run every day at 1 after midnight 
0 1 * * * cd /var/www/mitwy && python /var/www/mitwy/pull.py
```
#### Load backup
Create directory for backup on RasPi
```
# create directori for backup
mkdir /var/www/mitwy-backup
chmod a+xwr /var/www/mitwy-backup
# create index.html file for testing
vim /var/www/mitwy-backup/index.html
Hello Backup
```
Create virtual host

```
vim /etc/apache2/sites-available/mitwy-backup.conf
```
insert into file
```
<VirtualHost *:9080>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/mitwy-backup
        
<Directory /var/www/mitwy>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Order allow,deny
        Allow from all
</Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Update port listen for Apache
```
vim /etc/apache2/ports.conf
```
Add line (under Listen 80)
```
Listen 9080
```
Enable rewrite with apache (otherwise permalinks pages may raise 404)

```
a2enmod rewrite
```

Restart apache2

```
systemctl reload apache2
```

Validate your backup website access in browser with "<ip>:9080"

Create Database

```
# if file present in backup forlder (wsa there for testing)
rm index.html

# create db, user and access right to db
MariaDB> GRANT ALL ON nextclouddb.* TO 'nextcloud_user'@'localhost' IDENTIFIED BY 'example-password';
MariaDB> FLUSH PRIVILEGES;
MariaDB> EXIT;
```

Run load backup script tested in **python3**

```
url_old = "https://www.mitwy.com"
url_new = "http://192.168.1.28:9080"
backup_dir = "/mnt/storage/Mitwy/shop-backup/20210630"
restor_dir = "/var/www/mitwy-backup"
sql_db = "mitwy"
sql_user = ""
sql_psw = ""

# Move files from backup directory to restor directory
os.system("cp -R "+backup_dir+"/* "+restor_dir+"/")
print("Moved files")


# Restor mysql
os.system("mysql "+sql_db+" < "+restor_dir+"/"+sql_db+".sql")
print("Restored DB")


# Change site URL in db
mydb = mysql.connector.connect(
  host="localhost",
  user=sql_user,
  password=sql_psw,
  database=sql_db
)

mycursor = mydb.cursor()

cmd = "UPDATE wp_options SET option_value = replace(option_value, '"+url_old+"', '"+url_new+"') WHERE option_name = 'home' OR option_name = 'siteurl'"
print(cmd)
mycursor.execute(cmd)
mycursor.execute("UPDATE wp_posts SET guid = replace(guid,'"+url_old+"','"+url_new+"')")
mycursor.execute("UPDATE wp_posts SET post_content = replace(post_content, '"+url_old+"', '"+url_new+"');")
mycursor.execute("UPDATE wp_postmeta SET meta_value = replace(meta_value,'"+url_old+"','"+url_new+"')")
mycursor.execute("UPDATE wp_links SET link_url = replace(link_url, '"+url_old+"','"+url_new+"')")
mycursor.execute("UPDATE wp_comments SET comment_content = replace(comment_content , '"+url_old+"','"+url_new+"')")
mycursor.execute("UPDATE wp_links SET link_image = replace(link_image, '"+url_old+"','"+url_new+"')")
mydb.commit()

print("URL changed")

```

If can not see WordPress try debug enable in wp-config, define( 'WP_DEBUG', true );

If still issue 404 with pages, add .htaccess to WordPress directory if not present

```
# BEGIN WordPress
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
</IfModule>
# END WordPress

```


