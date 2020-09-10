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