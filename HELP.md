# Notes on upgrading from 7.9.5.0 to 8.0.6

```
chown -R sugar:www-data sugar && chmod -R g+s sugar


chown www-data:www-data -R sugar && chmod 755 -R sugar
```

1. Adjust config.php (db, siteurl, elastic host, host_name)
2. Adjust .htaccess
3. ./utilities/restore.sh 1
3. Fix access rights chown -R sugar:www-data sugar && chmod -R g+s sugar

php CliUpgrader.php -z /var/www/html/upgrade/SugarEnt-Upgrade-7.9.5.0-to-8.0.6.zip -l ./slientLog_800.log -s /var/www/html/sugar -u admin -S healthcheck
