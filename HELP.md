# Notes on upgrading from 7.9.5.0 to 8.0.6

```
chown -R sugar:www-data sugar && chmod -R g+s sugar
chown -R sugar:www-data toothpaste && chmod -R g+s toothpaste

chown www-data:www-data -R sugar && chmod 755 -R sugar
```

1. Adjust config.php (db, siteurl, elastic host, host_name)
2. Adjust .htaccess
3. ./utilities/restore.sh 1
4. Fix access rights chown -R sugar:www-data sugar && chmod -R g+s sugar
5. Set new elastic search instance sugar-elasticsearch56 (ignore that the logs report unsupported Elastic Search Version)
6. Execute SQL scripts below:

```
alter table `outbound_email` add column `email_address_id` char(36) DEFAULT NULL, add column `deleted` tinyint(1) DEFAULT '0';
CREATE TABLE `erased_fields` (`bean_id` char(36) NOT NULL,`table_name` varchar(128) NOT NULL,`data` text,PRIMARY KEY (`bean_id`,`table_name`)) ENGINE=InnoDB DEFAULT CHARSET=utf8;

delete from emails whre id > 0;
select * from emails;

# take the output and modify all tables to InnoDB Engine
SELECT CONCAT('ALTER TABLE ', TABLE_SCHEMA, '.', TABLE_NAME,' ENGINE=InnoDB;') FROM Information_schema.TABLES WHERE TABLE_SCHEMA = 'sugar' AND ENGINE = 'MyISAM'; 
```

7. php CliUpgrader.php -z /var/www/html/upgrade/SugarEnt-Upgrade-7.9.5.0-to-8.0.6.zip -l ./silentLog_800.log -s /var/www/html/sugar -u admin -S healthcheck
8. php CliUpgrader.php -z /var/www/html/upgrade/SugarEnt-Upgrade-7.9.5.0-to-8.0.6.zip -l ./silentLog_800.log -s /var/www/html/sugar -u admin
9. Fix access rights chown -R sugar:www-data sugar && chmod -R g+s sugar



output of SilentUpgrade
```
***************         Step "healthcheck" OK - 8 seconds
***************         Step "pre" OK - 1 second
***************         Step "commit" OK - 2 seconds
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: Interlace handling should be turned on when using png_read_image
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
libpng warning: iCCP: known incorrect sRGB profile
***************         Step "post" OK - 59 seconds
***************         Step "cleanup" OK - 4 seconds
***************         SUCCESS!
```


## Upgrade to v9.0.3

```
php CliUpgrader.php -z /var/www/html/upgrade/SugarEnt-Upgrade-8.0.6-to-9.0.3.zip -l ./silentLog_900.log -s /var/www/html/sugar -u admin -S healthcheck
php CliUpgrader.php -z /var/www/html/upgrade/SugarEnt-Upgrade-8.0.6-to-9.0.3.zip -l ./silentLog_900_2.log -s /var/www/html/sugar/ -u admin  
``` 

output of SilentUpgrade
```
php CliUpgrader.php -z /var/www/html/upgrade/SugarEnt-Upgrade-8.0.6-to-9.0.3.zip -l ./silentLog_900_2.log -s /var/www/html/sugar/ -u admin                
***************         Step "unpack" OK - 2 seconds
Health check passed (green flags). Please refer to the log file /var/www/html/upgrade/upgrade9/silentLog_900_2.log

* Are you sure you want to continue? (Yes/No) Yes
***************         Step "healthcheck" OK - 38 seconds
***************         Step "pre" OK - 3 seconds
***************         Step "commit" OK - 2 seconds
libpng warning: Interlace handling should be turned on when using png_read_image
***************         Step "post" OK - 143 seconds
***************         Step "cleanup" OK - 4 seconds
***************         SUCCESS!
```

## Upgrade to v10.0.1

* requirement changes PHP 7.1 to 7.3
* change Elasticsearch hostname to `sugar-elasticsearch`

```
php CliUpgrader.php -z /var/www/html/upgrade/SugarEnt-Upgrade-9.0.3-to-10.0.1.zip -l ./silentLog_1000.log -s /var/www/html/sugar/ -u admin -S healthcheck
php CliUpgrader.php -z /var/www/html/upgrade/SugarEnt-Upgrade-9.0.3-to-10.0.1.zip -l ./silentLog_1000.log -s /var/www/html/sugar/ -u admin

```

output of SilentUpgrade
```
***************         Step "unpack" OK - 1 second
Health check passed (green flags). Please refer to the log file /var/www/html/upgrade/upgrade10/silentLog_1000.log

* Are you sure you want to continue? (Yes/No) Yes
***************         Step "healthcheck" OK - 9 seconds
***************         Step "pre" OK - 1 second
***************         Step "commit" OK - 1 second
libpng warning: Interlace handling should be turned on when using png_read_image
libpng warning: Interlace handling should be turned on when using png_read_image
***************         Step "post" OK - 72 seconds
***************         Step "cleanup" OK - 5 seconds
***************         SUCCESS!

```


## Move to cloud

* Get composer on Docker Web container: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-composer-on-ubuntu-20-04-de
* ZIP everything: https://support.sugarcrm.com/Resources/Sugar_Cloud_Policy_Guide/#Recommended_Backup_Procedure

``
ln -s /usr/local/bin/php /usr/bin/php
composer install --no-dev
./packager.php --name reoss /var/www/html/sugar/
``