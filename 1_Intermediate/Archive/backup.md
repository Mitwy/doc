

```
# move files from backup directory 
cp -R /mnt/storage/Mitwy/shop-backup/20210619/* ./mitwy-backup/

# restore mysql
cd /var/www/mitwy-backup
mysql mitwy < mitwy.sql

# change siteurl https://wpbeaches.com/updating-wordpress-mysql-database-after-moving-to-a-new-url/ (chaning url in wp_config is not enough)

mysql -u root -p mitwy

UPDATE wp_options SET option_value = replace(option_value, 'https://www.mitwy.com', 'http://192.168.1.28:9080') WHERE option_name = 'home' OR option_name = 'siteurl';

UPDATE wp_posts SET guid = replace(guid,'https://www.mitwy.com','http://192.168.1.28:9080');

UPDATE wp_posts SET post_content = replace(post_content, 'https://www.mitwy.com', 'http://192.168.1.28:9080');

UPDATE wp_postmeta SET meta_value = replace(meta_value,'https://www.mitwy.com','http://192.168.1.28:9080');

UPDATE wp_links SET link_url = replace(link_url, 'https://www.mitwy.com','http://192.168.1.28:9080');

UPDATE wp_comments SET comment_content = replace(comment_content , 'https://www.mitwy.com','http://192.168.1.28:9080');

UPDATE wp_links SET link_image = replace(link_image, 'https://www.mitwy.com','http://192.168.1.28:9080');

```

#### Issue not crone tab running

You can enable logging for cron jobs in order to track problems. You need to edit the `/etc/rsyslog.conf` or `/etc/rsyslog.d/50-default.conf` (on Ubuntu) file and make sure you have the following line uncommented or add it if it is missing:

```
cron.*                         /var/log/cron.log
```

Then restart `rsyslog` and `cron`:

```
sudo service rsyslog restart
sudo service cron restart
```

Cron jobs will log to `/var/log/cron.log`.

#### Old pull using bash

```
git pull
mkdir /mnt/storage/Mitwy/shop-backup/$(date +%Y%m%d)
cp -R /var/www/mitwy/* /mnt/storage/Mitwy/shop-backup/$(date +%Y%m%d)/
```

```
crontab -e

# Add at the end of the file to run every day at 1 after midnight 
0 1 * * * cd /var/www/mitwy && sh /var/www/mitwy/pull.sh
```

