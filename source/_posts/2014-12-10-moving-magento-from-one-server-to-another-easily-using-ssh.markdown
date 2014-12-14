---
layout: post
title: "Moving magento Store from one server to another easily using ssh"
date: 2014-12-10 08:40:11 -0500
comments: true
categories: php web
---

When I first started working with Magento I thought moving a magento installation with lots of data from one server to another wont be easy considering the db size is very big and the codebase is not small either.

I was a noob at linux server's stuff then, I am not good at all now, but I have become better than before. And after moving several Magento installations around, creating development sandboxes from live data with large databases like 4GB+ I do think with some terminal kungfu you can pretty easily move magento from one  server to another. The method I will show will required ```ssh``` access to both servers.

<!-- more -->

I will walk you through step by step,

## Exporting the current Magento Database

To export the current database magento is using, first ```ssh``` into the server and then find the database config from magento config file located at ```app/etc/local.xml``` . In that file you will see a connection node like following

``` xml app/etc/local.xml```
    <connection>
          <host><![CDATA[localhost]]></host>
          <username><![CDATA[username]]></username>
          <password><![CDATA[password]]></password>
          <dbname><![CDATA[dbname]]></dbname>
          <initStatements><![CDATA[SET NAMES utf8]]></initStatements>
          <model><![CDATA[mysql4]]></model>
          <type><![CDATA[pdo_mysql]]></type>
          <pdoType><![CDATA[]]></pdoType>
          <active>1</active>
    </connection>
```

So you have DB access to export the database magento is using. You can use ```mysqldump``` to dump the database using the following command:

``` bash mysqldump

mysqldump -h DB_HOST -u DB_USER -p DB_NAME > EXPORT_FILENAME.sql

```

It will prompt you for password and after you provide that it will start dumping the database into the file you specified. It can take time depending on the amount of data you have in your magento store. Like in my case it took quite some time to export a database of size around 4GB. Once done, you will get a uncompressed sql file whose size can be reduced easily. As it's a text file it's compression ratio will be good and you should get a file much smaller in size. You can compress it using ```tar```

```bash tar
tar -czf  EXPORT_FILENAME.sql.tar.gz EXPORT_FILENAME.sql

```

## Moving the database into the new server

If it's a very big dump then you should be using ```scp``` (Secure Copy over ssh) to save yourself time. That would reduce the step of downloading it in your local machine first and then re upload into the new server. If you have ```ssh``` access of the new server then you should definitely try to use ```scp``` . For that login into the old server where you have the exported database dump then run a command like

```bash scp

 scp -P SSH_PORT /PATH/TO/EXPORT_FILENAME.sql.gz username@NEW_SERVER_IP:/NEW/SERVER/PATH/

```

It will copy the database over to the new server in no time, in server's internet speed which is high in most cases.

You can copy the magento codebase the same way over to the new server.


## Importing the database into the new server

First ```untar``` the copied database into it's original ```.sql``` format. Use the following command,

```bash untar
tar -xzf EXPORT_FILENAME.sql.tar.gz

```

After this step you will have a file named ```EXPORT_FILENAME``` in the current directory you are in.

Create a new database in the new server either from command line using ```mysql cli``` or from ```cpanel``` or any kind of database administration tool and then ```ssh``` into the new server. Then you can import the copied database dump into the newly created database using the following command,

```bash mysql import

mysql -u DB_USER -p DB_NAME < EXPORT_FILENAME.sql

```
It will ask for password, will take some time and eventually import the database into the new server.

## Updating Database config in Code and base url in database

Like I said copy over the magento codebase the say way with ```scp``` or in any other way you want. Now update the database config in ```app/etc/local.xml``` file.

You also have to update the ```base_url``` in ```core_config_data``` table of magento which it uses internally to render links throughout the site. Load the ```core_config_data``` table into any database browser like ```Sequel Pro``` or ```PHPMyAdmin``` . You will see that table has a column named ```path``` . Search for path values of ```web/unsecure/base_url``` and ```web/secure/base_url``` and replace the value which is of the old site with the new web root of your magento installation you copied a while ago.

As you may have guessed from the path names ```secure``` and ```unsecure``` ```base_url``` means what they should mean and they are the ```http``` and ```https``` urls of your new magento home.

You can now load the new url in browser and it should load the new site. If it doesn't then you might have to clear the cache of the site. The ```cache``` directory is in ```app/var/cache``` . Remove the contents of that directory and you should be done. You could remove the contents of that directory from command line using the following command too

```bash rm

rm -rf ./app/var/cache/*

```

If you still face any problem in loading the new site then you should try to debug the specific problem you are having. Thats all for today. Enjoy!






