# Instructions

Day by day traffic is increasing on one of the websites managed by the `Nautilus`production support team. Therefore, the team has observed a degradation
 in website performance. Following discussions about this issue, the team has decided to deploy this application on a high availability stack i.e on `Nautilus` infra in `Stratos DC`. They started the migration last month and it is almost done, as only the LBR server configuration is pending. Configure LBR server as per the information given below:

a. Install `nginx` on `LBR` (load balancer) server.

b. Configure load-balancing with the an `http` context making use of all `App Servers`.

c. Make sure you do not update the apache port that is already defined in the apache configuration on all app servers, also make sure apache service is up and running on all app servers.

d. Once done, you can access the website using `StaticApp` button on the top bar.

# Solution

Check the port at least in 2 Apps just to be sure:

`ssh tony@stapp01`

`vi /etc/httpd/conf/httpd.conf`

`Check the port: Listen 5004`

`ssh steve@stapp02`

`cat /etc/httpd/conf/httpd.conf`

`Check the port: Listen 5004`

Connect to LBR:

`ssh loki@stlb01`

`sudo su -`

`yum update`

`yum install nginx`

`vi /etc/nginx/nginx.conf`

Add this part:
```bash
include /etc/nginx/conf.d/*.conf;
upstream app_servers {
    server stapp01:5004;
    server stapp02:5004;
    server stapp03:5004;
# Add more servers as needed
}
server {
    listen       80;
    listen       [::]:80;
    location / {
    proxy_pass http://app_servers;
    }
```

`systemctl restart nginx`

============================================================================================================================

# Instructions

We have LEMP stack configured on apps and database server in `Stratos` DC. Its using Nginx + php-fpm, for now we have deployed a sample php page on these apps. Due to some misconfiguration the php page is not loading on the web server. Seems like at least two app servers are having issues. Find below more details and make sure website works on LBR URL and locally on each app as well.

1. Nginx is supposed to run on port `80` on all app servers.

2. Nginx document root is `/var/www/html/`

3. Test the webpage on LBR URL (use `LBR` button on the top bar) and locally on each app server to make sure it works. It must not display any error message or nginx default page.

# Solution

`ssh tony@stapp01`

`ssh steve@stapp02`

`ssh banner@stapp03`

`sudo su -`

`vi /etc/nginx/nginx.conf`

Make changes so blocks look like on App3:

```bash
   #On App 1:
       server {
        listen       8084 default_server;
        listen       [::]:8084 default_server;
        server_name  _;
        root         /var/www/html/;
        index index.html index.htm;   #fix this one too
   #On App 2:
       server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /var/www/html/;
        index index.php index.html index.htm;
        
        <...>
        fastcgi_pass unix:/var/opt/remi/php74/run/php-fpm/www;
   #On App 3:
       server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html/;
        index index.php index.html index.htm;
 
        <...>
        fastcgi_pass unix:/var/opt/remi/php74/run/php-fpm/www.sock;
```

Test the configuration

```bash
[root@stapp03 ~]# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
[root@stapp03 ~]#
```

`systemctl reload nginx`

`systemctl restart nginx`

`systemctl status nginx`

`curl http://localhost`

```bash

[root@stapp01 ~]# curl http://localhost
App is able to connect to the database using user kodekloud_sam
[root@stapp02 ~]# curl http://localhost
App is able to connect to the database using user kodekloud_sam
[root@stapp03 ~]# curl http://localhost
App is able to connect to the database using user kodekloud_sam
```

============================================================================================================================

# Instructions

The `Nautilus` application development team has shared that they are planning to deploy one newly developed application on `Nautilus` infra in `Stratos DC`. The application uses PostgreSQL database, so as a pre-requisite we need to set up PostgreSQL database server as per requirements shared below:

PostgreSQL database server is already installed on the `Nautilus` database server.

a. Create a database user `kodekloud_gem` and set its password to `LQfKeWWxWD`.

b. Create a database `kodekloud_db6` and grant full permissions to user `kodekloud_gem` on this database.

`Note:` Please do not try to restart PostgreSQL server service.

# Solution

`ssh peter@stdb01`

`sudo su -`

`yum install -y postgresql-server postgresql-contrib`

`postgresql-setup initdb`

If you are getting errors, follow these steps:

```bash
[root@stdb01 ~]# postgresql-setup initdb
WARNING: using obsoleted argument syntax, try --help
WARNING: arguments transformed to: postgresql-setup --initdb --unit postgresql
 * Initializing database in '/var/lib/pgsql/data'
ERROR: Data directory /var/lib/pgsql/data is not empty!
ERROR: Initializing database failed, possibly see /var/lib/pgsql/initdb_postgresql.log
[root@stdb01 ~]# ^C
[root@stdb01 ~]# postgresql-setup --initdb --unit postgresql
 * Initializing database in '/var/lib/pgsql/data'
ERROR: Data directory /var/lib/pgsql/data is not empty!
ERROR: Initializing database failed, possibly see /var/lib/pgsql/initdb_postgresql.log
[root@stdb01 ~]# systemctl stop postgresql
[root@stdb01 ~]# mv /var/lib/pgsql/data /var/lib/pgsql/data.backup
[root@stdb01 ~]# postgresql-setup --initdb --unit postgresql
 * Initializing database in '/var/lib/pgsql/data'
 * Initialized, logs are in /var/lib/pgsql/initdb_postgresql.log
[root@stdb01 ~]#
```
`systemctl enable postgresql && systemctl start postgresql && systemctl status postgresql`

`sudo -u postgres psql`

`create user kodekloud_gem with encrypted password 'LQfKeWWxWD';`

`create database kodekloud_db6 owner kodekloud_gem;`

`grant all privileges on database kodekloud_db6 to kodekloud_gem;`

```bash
[root@stdb01 ~]# sudo -u postgres psql
postgres=# create user kodekloud_top with encrypted password 'LQfKeWWxWD'; 
CREATE ROLE
postgres=# create database kodekloud_db1 owner kodekloud_top;
CREATE DATABASE
postgres=# grant all privileges on database kodekloud_db1 to kodekloud_top;
GRANT
postgres=# exit
[root@stdb01 ~]#
```

 `vi /var/lib/pgsql/data/postgresql.conf`

 ```bash
[root@stdb01 ~]# cat /var/lib/pgsql/data/postgresql.conf |grep localhost
#listen_addresses = 'localhost'         # what IP address(es) to listen on;
                                        # defaults to 'localhost'; use '*' for all
[root@stdb01 ~]# vi /var/lib/pgsql/data/postgresql.conf
[root@stdb01 ~]# cat /var/lib/pgsql/data/postgresql.conf |grep localhost
listen_addresses = 'localhost'          # what IP address(es) to listen on;
                                        # defaults to 'localhost'; use '*' for all
```

`vi /var/lib/pgsql/data/pg_hba.conf`

```bash
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
```

`systemctl restart postgresql`

`systemctl status postgresql`

```bash
[root@stdb01 ~]# psql -U kodekloud_gem  -d kodekloud_db6 -h 127.0.0.1 -W
Password: 
psql (13.14)
Type "help" for help.

kodekloud_db6=> exit
[root@stdb01 ~]#  psql -U kodekloud_gem  -d kodekloud_db6 -h localhost -W
Password: 
psql (13.14)
Type "help" for help.

kodekloud_db6=> exit
[root@stdb01 ~]# 
```

============================================================================================================================
# Instructions

The Nautilus DevOps team is working on to develop a bash script to automate some tasks. As per the requirements shared with the team database related tasks needed to be automated. Below you can find more details about the same:

1. Write a bash script named `/opt/scripts/database.sh` on `Database Server`. The mariadb database server is already installed on this server.

2. Add code in the script to perform some database related operations as per conditions given below:

a. Create a new database named `kodekloud_db01`. If this database already exists on the server then script should print a message `Database already exists` and if the database does not exist then create the same and script should print `Database kodekloud_db01 has been created`. Further, create a user named `kodekloud_roy` and set its password to `asdfgdsd`, also give full access to this user on newly created database (remember to use wildcard host while creating the user).

b. Now check if the database (if it was already there) already contains some data (tables)`if so then script should print 'database is not empty` otherwise import the database dump `/opt/db_backups/db.sql` and print `imported database dump into kodekloud_db01 database.`

c. Take a mysql dump which should be named as `kodekloud_db01.sql` and save it under `/opt/db_backups/` directory.

# Solution

`ssh peter@stdb01`

`sudo su -`

`cd /opt/scripts`

`vi database.sh`

```bash
#!/bin/bash

DB_NAME="kodekloud_db01"
DB_USER="kodekloud_roy"
DB_USER_PASS="asdfgdsd"

if mysql -u root -e "use $DB_NAME" 2>/dev/null; then
  echo "Database already exists"
else
  mysql -u root -e "create database $DB_NAME"
  echo "Database $DB_NAME has been created"

  mysql -u root -e "CREATE USER '$DB_USER'@'%' IDENTIFIED BY '$DB_USER_PASS';"
  mysql -u root -e "GRANT ALL PRIVILEGES ON $DB_NAME.* TO '$DB_USER'@'%';"
fi

if mysql -u root $DB_NAME -e "SHOW TABLES" | grep -q "."; then
  echo "database is not empty"
else
  mysql -u root $DB_NAME < /opt/db_backups/db.sql
  echo "imported database dump into $DB_NAME database."
fi

mysqldump -u root $DB_NAME > /opt/db_backups/$DB_NAME.sql
```

`chmod +x  database.sh`

`sh database.sh`

```bash
[root@stdb01 scripts]# sh database.sh 
Database already exists
imported database dump into kodekloud_db01 database.
[root@stdb01 scripts]# mysqlshow
+--------------------+
|     Databases      |
+--------------------+
| information_schema |
| kodekloud_db01     |
| mysql              |
| performance_schema |
+--------------------+
[root@stdb01 scripts]# mysqlshow kodekloud_db01 
Database: kodekloud_db01
+-----------------------+
|        Tables         |
+-----------------------+
| wp_commentmeta        |
| wp_comments           |
| wp_links              |
| wp_options            |
| wp_postmeta           |
| wp_posts              |
| wp_term_relationships |
| wp_term_taxonomy      |
| wp_termmeta           |
| wp_terms              |
| wp_usermeta           |
| wp_users              |
+-----------------------+
[root@stdb01 scripts]# ls -ls /opt/db_backups/
total 92
48 -rw-r--r-- 1 root root 46379 Aug  1 15:25 db.sql
44 -rw-r--r-- 1 root root 44917 Aug  1 15:45 kodekloud_db01.sql
```

============================================================================================================================

# Instructions

xFusionCorp Industries is planning to host a `WordPress` website on their infra in `Stratos Datacenter`. They have already done infrastructure configuration—for example, on the storage server they already have a shared directory `/vaw/www/html` that is mounted on each app host under `/var/www/html` directory. Please perform the following steps to accomplish the task:

a. Install httpd, php and its dependencies on all app hosts.

b. Apache should serve on port `6200` within the apps.

c. Install/Configure `MariaDB server` on DB Server.

d. Create a database named `kodekloud_db2` and create a database user named `kodekloud_cap` identified as password `GyQkFRVNr3`. Further make sure this newly created user is able to perform all operation on the database you created.

e. Finally you should be able to access the website on LBR link, by clicking on the `App` button on the top bar. You should see a message like `App is able to connect to the database using user kodekloud_cap`

# Solution

On DB Server:

`ssh peter@stdb01`

`yum install mariadb-server`

`systemctl start mariadb`

`systemctl enable mariadb`

`systemctl status mariadb`

`mysql -u root -p`

```bash
create database kodekloud_db2;
use kodekloud_db2;
create user kodekloud_cap identified by 'GyQkFRVNr3'; 
select host, user, password from mysql.user;
grant all on kodekloud_db2.* to 'kodekloud_cap' IDENTIFIED BY 'GyQkFRVNr3' WITH GRANT OPTION;
grant all privileges on kodekloud_db2.* to 'kodekloud_cap'@'%' WITH GRANT OPTION;
exit
```

```bash
[root@stdb01 ~]# mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 10.5.22-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> create database kodekloud_db2;
Query OK, 1 row affected (0.000 sec)

MariaDB [(none)]> use kodekloud_db2;
Database changed
MariaDB [kodekloud_db2]> create user kodekloud_cap identified by 'GyQkFRVNr3'; 
Query OK, 0 rows affected (0.001 sec)

MariaDB [kodekloud_db2]> select host, user, password from mysql.user;
+-----------+---------------+-------------------------------------------+
| Host      | User          | Password                                  |
+-----------+---------------+-------------------------------------------+
| localhost | mariadb.sys   |                                           |
| localhost | root          | invalid                                   |
| localhost | mysql         | invalid                                   |
| %         | kodekloud_cap | *FF0F3C6657DD0E5CD304C0364FFA63CBC03493D8 |
+-----------+---------------+-------------------------------------------+
4 rows in set (0.001 sec)

MariaDB [kodekloud_db2]> grant all on kodekloud_db2.* to 'kodekloud_cap' IDENTIFIED BY 'GyQkFRVNr3' WITH GRANT OPTION;
Query OK, 0 rows affected (0.001 sec)

MariaDB [kodekloud_db2]> grant all privileges on kodekloud_db2.* to 'kodekloud_cap'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.001 sec)

MariaDB [kodekloud_db2]>
```

`systemctl restart mariadb`

`systemctl status mariadb`

On each App server:

`ssh tony@stapp01`

`ssh steve@stapp02`

`ssh banner@stapp03`

`sudo su -`

Run these commands for each app:

`yum install -y net-tools mc`

`yum install -y httpd php.x86_64 php-mysqlnd.x86_64`

`vi /etc/httpd/conf/httpd.conf`

Change:

Listen 6200

`systemctl restart httpd`

Check the results:

```bash
[root@stapp01 ~]# curl http://stlb01
App is able to connect to the database using user kodekloud_cap
[root@stapp02 ~]# curl http://stlb01
App is able to connect to the database using user kodekloud_cap
[root@stapp03 ~]# curl http://stlb01
App is able to connect to the database using user kodekloud_cap
```

============================================================================================================================

# Instructions

We need to setup a database server on `Nautilus DB Server` in `Stratos Datacenter`. Please perform the below given steps on DB Server:

a. Install/Configure MariaDB server.

b. Create a database named `kodekloud_db2`.

c. Create a user called `kodekloud_joy` and set its password to `8FmzjvFU6S`.

d. Grant full permissions to user `kodekloud_joy` on database `kodekloud_db2`.

# Solution

`ssh peter@stdb01`

`sudo su -`

`yum install openssh-clients -y`

`yum install -y mariadb-server`

`yum install -y mariadb*`

```bash
[root@stdb01 ~]# yum install -y mariadb
Last metadata expiration check: 0:00:45 ago on Wed Jul 31 10:40:23 2024.
Package mariadb-3:10.5.22-1.el9.x86_64 is already installed.
Dependencies resolved.
Nothing to do.
Complete!
```

`systemctl restart mariadb`

`systemctl enable mariadb && systemctl start mariadb && systemctl status mariadb`

`sudo mysql_secure_installation`  

```bash
[root@stdb01 ~]# sudo mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] n
 ... skipping.

You already have your root account protected, so you can safely answer 'n'.

Change the root password? [Y/n] Y     --Not sure how is should be, but I entered pass '8FmzjvFU6S' here
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] n
 ... skipping.

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
[root@stdb01 ~]#
```

`mysql -u root -p`

```bash
create database kodekloud_db2;
use kodekloud_db2;
create user kodekloud_joy identified by '8FmzjvFU6S'; #if you make a mistake use ALTER, e.g. ALTER USER 'kodekloud_joy'@localhost IDENTIFIED BY '8FmzjvFU6S';
select host, user, password from mysql.user;
grant all on kodekloud_db2.* to 'kodekloud_joy' IDENTIFIED BY '8FmzjvFU6S' WITH GRANT OPTION;
grant all privileges on kodekloud_db2.* to 'kodekloud_joy'@'%' WITH GRANT OPTION;
exit
  
  #Since I made a mistake in the password, but created the correct db, I went for these:
ALTER USER 'kodekloud_joy'@localhost IDENTIFIED BY '8FmzjvFU6S';
grant all on kodekloud_db2.* to 'kodekloud_joy' IDENTIFIED BY '8FmzjvFU6S' WITH GRANT OPTION;
grant all privileges on kodekloud_db2.* to 'kodekloud_joy'@'%' WITH GRANT OPTION;
```


Tbh, not sure if this part is necessary

`vi /etc/my.cnf`

```bash
[mysqld]
bind-address = 172.16.239.10

[client]
port = 3306
```

`systemctl restart mysql`

`mysql -u kodekloud_joy -p -h stdb01`

`mysql -u kodekloud_joy -p -h localhost`

```bash
[root@stdb01 peter]# mysql -u kodekloud_joy -p -h stdb01
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 10.5.22-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> exit
Bye
[root@stdb01 peter]# mysql -u kodekloud_joy -p -h localhost
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 4
Server version: 10.5.22-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

============================================================================================================================

# Instructions

xFusionCorp Industries is planning to host two static websites on their infra in `Stratos Datacenter`. The development of these websites is still in-progress, but we want to get the servers ready. Please perform the following steps to accomplish the task:

a. Install `httpd` package and dependencies on `app server 1`.

b. Apache should serve on port `5001`.

c. There are two website's backups `/home/thor/beta` and `/home/thor/cluster` on `jump_host`. Set them up on Apache in a way that `beta` should work on the link `http://localhost:5001/beta/` and `cluster` should work on link `http://localhost:5001/cluster/` on the mentioned app server.

d. Once configured you should be able to access the website using `curl` command on the respective app server, i.e `curl http://localhost:5001/beta/` and `curl http://localhost:5001/cluster/`

# Solution

`ssh tony@stapp01`

`sudo su -`

`yum install openssh-clients -y`

`yum install -y httpd`

`cd /etc/httpd/conf`

`sed -i 47s/80/5001/ httpd.conf`

```bash
[root@stapp01 conf]# grep -n -i listen httpd.conf
37:# Listen: Allows you to bind Apache to specific IP addresses and/or
41:# Change this to Listen on a specific IP address, but note that if
46:#Listen 12.34.56.78:80
47:Listen 80
[root@stapp01 conf]# sed -i 47s/80/5001/ httpd.conf
[root@stapp01 conf]# grep -n -i listen httpd.conf
37:# Listen: Allows you to bind Apache to specific IP addresses and/or
41:# Change this to Listen on a specific IP address, but note that if
46:#Listen 12.34.56.78:80
47:Listen 5001
```

`systemctl enable httpd && systemctl start httpd && systemctl status httpd`

```bash
[root@stapp01 conf]# systemctl enable httpd && systemctl start httpd && systemctl status httpd
Created symlink /etc/systemd/system/multi-user.target.wants/httpd.service → /usr/lib/systemd/system/httpd.service.
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; preset: disabled)
     Active: active (running) since Sun 2024-07-21 08:13:20 UTC; 40ms ago
       Docs: man:httpd.service(8)
   Main PID: 3422 (httpd)
     Status: "Started, listening on: port 5001"
      Tasks: 5 (limit: 1340692)
     Memory: 7.3M
     CGroup: /docker/9fc20eb9250817d8cc4b8bd05f040e35e919f99aab39126f2f7deb737a66c619/system.slice/httpd.service
             ├─3422 /usr/sbin/httpd -DFOREGROUND
             ├─3429 /usr/sbin/httpd -DFOREGROUND
             ├─3430 /usr/sbin/httpd -DFOREGROUND
             ├─3431 /usr/sbin/httpd -DFOREGROUND
             └─3432 /usr/sbin/httpd -DFOREGROUND

Jul 21 08:13:20 stapp01.stratos.xfusioncorp.com systemd[1]: Starting The Apache HTTP Server...
Jul 21 08:13:20 stapp01.stratos.xfusioncorp.com httpd[3422]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using stapp01.stratos.xfusioncorp.com. Set the 'ServerName' directive globally to suppress this message
Jul 21 08:13:20 stapp01.stratos.xfusioncorp.com httpd[3422]: Server configured, listening on: port 5001
Jul 21 08:13:20 stapp01.stratos.xfusioncorp.com systemd[1]: Started The Apache HTTP Server.
```

Go back to thor

```bash
thor@jumphost ~$ sudo scp -r /home/thor/beta tony@172.16.238.10:/tmp
index.html                                                                                  100%  117   233.7KB/s   00:00 
thor@jumphost ~$ sudo scp -r /home/thor/cluster tony@172.16.238.10:/tmp
index.html                                                                                  100%  120   531.6KB/s   00:00
```

Go back to tony, if you are still inside /tmp, run: `mv beta cluster /var/www/html`

```bash
[root@stapp01 tmp]# mv beta cluster /var/www/html 
[root@stapp01 tmp]# cd /var/www/html
[root@stapp01 html]# ls
beta  cluster
```

`curl -4 http://stapp01:5001/beta/`

`curl -4 http://stapp01:5001/cluster/`

```bash
thor@jumphost ~$ curl http://stapp01:5001/beta/
<!DOCTYPE html>
<html>
<body>

<h1>KodeKloud</h1>

<p>This is a sample page for our beta website</p>

</body>
</html>thor@jumphost ~$ curl http://stapp01:5001/cluster/
<!DOCTYPE html>
<html>
<body>

<h1>KodeKloud</h1>

<p>This is a sample page for our cluster website</p>

</body>
</html>thor@jumphost ~$
```

============================================================================================================================
# Instructions

The `Nautilus` application development team is planning to launch a new PHP-based application, which they want to deploy on `Nautilus` infra in `Stratos DC`. The development team had a meeting with the production support team and they have shared some requirements regarding the infrastructure. Below are the requirements they shared:

a. Install `nginx` on `app server 1` , configure it to use port `8091` and its document root should be `/var/www/html`.

b. Install `php-fpm` version `8.1` on `app server 1`, it should listen on port `9000`.

c. Configure php-fpm and nginx to work together.

d. Once configured correctly, you can test the website using `curl http://stapp01:8091/index.php` command from jump host.

# Solution

`ssh tony@stapp01`

`sudo su -`

`yum install epel-release`

`yum install nginx`

`dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm`

`dnf module enable php:remi-8.1`

`dnf install -y php-fpm`

`php-fpm --version`

```bash
[root@stapp01 ~]# php-fpm --version
PHP 8.1.29 (fpm-fcgi) (built: Jun  5 2024 05:51:57)
Copyright (c) The PHP Group
Zend Engine v4.1.29, Copyright (c) Zend Technologies
```

To check the module:

`dnf module list php`

`dnf module enable php:remi-8.1`

```bash
[root@stapp01 ~]# dnf module list php
Last metadata expiration check: 0:09:38 ago on Mon Jul 29 15:45:43 2024.
CentOS Stream 9 - AppStream
Name                Stream                      Profiles                                 Summary                             
php                 8.1                         common [d], devel, minimal               PHP scripting language              
php                 8.2                         common [d], devel, minimal               PHP scripting language              

Remi's Modular repository for Enterprise Linux 9 - x86_64
Name                Stream                      Profiles                                 Summary                             
php                 remi-7.4                    common [d], devel, minimal               PHP scripting language              
php                 remi-8.0                    common [d], devel, minimal               PHP scripting language              
php                 remi-8.1 [e]                common [d], devel, minimal               PHP scripting language              
php                 remi-8.2                    common [d], devel, minimal               PHP scripting language              
php                 remi-8.3                    common [d], devel, minimal               PHP scripting language              

Hint: [d]efault, [e]nabled, [x]disabled, [i]nstalled
```

`vi /etc/nginx/nginx.conf`

`vi /etc/nginx/conf.d/php-fpm.conf`
`vi /etc/nginx/default.d/php.conf`

File should look like this:

```bash
    server {
        listen       8091;
        listen       [::]:8091;
        server_name  _;
        root         /var/www/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        location ~ \.php$ {

          try_files $uri = 404;
          include fastcgi_params;
          fastcgi_pass  127.0.0.1:9000;
          fastcgi_index index.php;     
          fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
         }
        location /
        {
        index  index.php index.html index.htm;
        }
```

`systemctl restart nginx`

```bash
[root@stapp01 ~]# curl http://stapp01:8091/index.php
Welcome to xFusionCorp Industries!
```

============================================================================================================================
# Instructions

The `Nautilus` application development team is planning to launch a new PHP-based application, which they want to deploy on `Nautilus` infra in `Stratos DC`. The development team had a meeting with the production support team and they have shared some requirements regarding the infrastructure. Below are the requirements they shared:

a. Install `nginx` on `app server 1` , configure it to use port `8099` and its document root should be `/var/www/html`.

b. Install `php-fpm` version `8.1` on `app server 1`, it must use the unix socket `/var/run/php-fpm/default.sock` (create the parent directories if don't exist).

c. Configure php-fpm and nginx to work together.

d. Once configured correctly, you can test the website using `curl http://stapp01:8099/index.php` command from jump host.

# Solution

`ssh tony@stapp01`

`sudo su -`

`yum install epel-release`

`yum install nginx`

`dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm`

`dnf module enable php:remi-8.1`

`dnf install -y php-fpm`

`php-fpm --version`

```bash
[root@stapp01 ~]# php-fpm --version
PHP 8.1.29 (fpm-fcgi) (built: Jun  5 2024 05:51:57)
Copyright (c) The PHP Group
Zend Engine v4.1.29, Copyright (c) Zend Technologies
```

`vi /etc/php-fpm.d/www.conf`

Change: user, group, listen

```bash
; RPM: apache user chosen to provide access to the same directories as httpd
user = nginx
; RPM: Keep a group allowed to write in log dir.
group = nginx
<...>
listen = /run/php-fpm/default.sock
```

`chown -R root:nginx /var/lib/php`

`vi /etc/nginx/nginx.conf`

`vi /etc/nginx/conf.d/php-fpm.conf`

`vi /etc/nginx/default.d/php.conf`

Should look like this:

```bash
    server {
        listen       8099;
        listen       [::]:8099;
        server_name  _;
        root         /var/www/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        location ~ \.php$ {
            try_files $uri =404;
            fastcgi_pass php-fpm;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }  
        error_page 404 /404.html;
        location = /404.html {
        }
```

`vi /etc/nginx/conf.d/php-fpm.conf`

Change here to 'default'

```bash
# PHP-FPM FastCGI server
# network or unix domain socket configuration

upstream php-fpm {
    server unix:/run/php-fpm/default.sock;
}
```

`systemctl restart php-fpm nginx`

```bash
[root@stapp01 ~]# curl http://stapp01:8099/index.php
Welcome to xFusionCorp Industries
```




============================================================================================================================
