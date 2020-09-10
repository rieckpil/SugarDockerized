# Notes on upgrading from 7.9.5.0 to 8.0.6

```
chown -R sugar:www-data sugar && chmod -R g+s sugar
chown -R sugar:www-data toothpaste && chmod -R g+s toothpaste


chown www-data:www-data -R sugar && chmod 755 -R sugar
```

1. Adjust config.php (db, siteurl, elastic host, host_name)
2. Adjust .htaccess
3. ./utilities/restore.sh 1
3. Fix access rights chown -R sugar:www-data sugar && chmod -R g+s sugar

php CliUpgrader.php -z /var/www/html/upgrade/SugarEnt-Upgrade-7.9.5.0-to-8.0.6.zip -l ./silentLog_800.log -s /var/www/html/sugar -u admin -S healthcheck

alter table `outbound_email` add column `email_address_id` char(36) DEFAULT NULL, add column `deleted` tinyint(1) DEFAULT '0';
CREATE TABLE `erased_fields` (`bean_id` char(36) NOT NULL,`table_name` varchar(128) NOT NULL,`data` text,PRIMARY KEY (`bean_id`,`table_name`)) ENGINE=InnoDB DEFAULT CHARSET=utf8;

delete from emails;
select * from emails;

SELECT CONCAT('ALTER TABLE ', TABLE_SCHEMA, '.', TABLE_NAME,' ENGINE=InnoDB;') FROM Information_schema.TABLES WHERE TABLE_SCHEMA = 'sugar' AND ENGINE = 'MyISAM';