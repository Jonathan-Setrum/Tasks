# Instructions

The `Nautilus` system admins team has prepared scripts to automate several day-to-day tasks. They want them to be deployed on all app servers in `Stratos DC` on a set schedule. Before that they need to test similar functionality with a sample cron job. 
Therefore, perform the steps below:

a. Install `cronie` package on all `Nautilus` app servers and start `crond` service.

b. Add a cron `*/5 * * * * echo hello > /tmp/cron_text` for `root` user.

# Solution

ssh tony@stapp01

ssh steve@stapp02

ssh banner@stapp03

Run the appropriate command based on the package manager used by your servers:`sudo yum install cronie`

After installing the `cronie` package, start the `crond` service: `sudo systemctl start crond`

To check if the `crond` service is running on a Nautilus app server, you can use the following command: `sudo systemctl status crond`

Open the crontab editor for the root user by executing the following command: `sudo crontab -e -u root`

In the crontab editor, add the following line: `*/5 * * * * echo hello > /tmp/cron_text`

Then open /tmp/ after 5min the file will appear there

============================================================================================================================
# Instructions

During the monthly compliance meeting, it was pointed out that several servers in the `Stratos DC`do not have a valid banner. The security team has provided serveral approved templates which should be applied to the servers to maintain compliance. These will be displayed to the user upon a successful login.

Update the `message of the day` on all application and db servers for `Nautilus`. Make use of the approved template located at `/home/thor/nautilus_banner` on jump host

# Solution


scp /home/thor/nautilus_banner tony@stapp01:/tmp/

scp /home/thor/nautilus_banner steve@stapp02:/tmp/

scp /home/thor/nautilus_banner banner@stapp03:/tmp/

scp /home/thor/nautilus_banner peter@stdb01:/tmp/

ssh tony@stapp01

ssh steve@stapp02

ssh banner@stapp03

ssh peter@stdb01

ssh to each App Server

sudo mv /tmp/nautilus_banner /etc/motd

sudo chmod +x /etc/motd

============================================================================================================================

# Instructions

The Nautilus team doesn't want its data to be accessed by any of the other groups/teams due to security reasons and want their data to be 
strictly accessed by the `sysops` group of the team.

Setup a collaborative directory `/sysops/data` on `app server 3` in `Stratos Datacenter`.

The directory should be group owned by the group `sysops` and the group should own the files inside the directory. The directory should be `read/write/execute` to the user and group owners, and `others` should not have any access.

# Solution

ssh banner@stapp03

Create the `/sysops/data` directory: `sudo mkdir -p /sysops/data`

Set the group ownership of the directory to `sysops`: `sudo chown :sysops /sysops/data`

The `chown` command changes the group ownership of the directory to `sysops`. The `:` before `sysops` indicates that only the group ownership is being changed.

Set the group ownership of the files inside the directory: `sudo chown -R :sysops /sysops/data`

Set the permissions of the directory to read, write, and execute for the user and group owners: `sudo chmod ug+rwx /sysops/data`

Restrict access to others: `sudo chmod o-rwx /sysops/data`

To verify the ownership of the directory: `ls -ld /sysops/data`

This command will display the ownership and permissions of the `/sysops/data` directory. The group ownership should be set to `sysops`.

To check the ownership and permissions of the files and subdirectories inside the `/sysops/data` directory: `sudo ls -l /sysops/data`

To verify the permissions of the directory: `stat -c "%a %n" /sysops/data`

This command will display the permissions of the `/sysops/data` directory in numeric format. The expected permissions should be `770`, indicating read, write, and execute permissions for the user and group owners, and no access for others.

============================================================================================================================
# Instructions

There is some data on `Nautilus App Server 3` in `Stratos DC`. Data needs to be altered in several of the files. On `Nautilus App Server 3`, alter the `/home/BSD.txt` file as per details given below:

a.  Delete all lines containing word `following` and save results in `/home/BSD_DELETE.txt` file. (Please be aware of case sensitivity)

b.  Replace all occurrence of word  `the` to `their` and save results in `/home/BSD_REPLACE.txt` file.

`Note:`  Let's say you are asked to replace word `to` with `from`. In that case, make sure not to alter any words containing the string itself; for example up**to**, contribu**to**r etc.

# Solution

ssh banner@stapp03

sudo su -

This command reads the contents of the file `/home/BSD.txt` and filters out the lines that contain the word "following", displaying them as output: `cat /home/BSD.txt |grep following`

This command uses the `sed` command to delete lines containing the word "following" from the file `/home/BSD.txt`. The filtered content is then redirected to the file `/home/BSD_DELETE.txt`: `sed '/\<following\>/d' /home/BSD.txt > /home/BSD_DELETE.txt`

This command reads the contents of the file `/home/BSD_DELETE.txt` and filters out the lines that contain the word "following", displaying them as output: `cat /home/BSD_DELETE.txt |grep following`

This command reads the contents of the file `/home/BSD.txt` and filters out the lines that contain the word "the", displaying them as output: `cat /home/BSD.txt |grep the`

This command uses the `sed` command to replace all occurrences of the word "the" (considered as a whole word) with the word "their" in the file `/home/BSD.txt`. The modified content is then redirected to the file `/home/BSD_REPLACE.tx`: `sed 's/\bthe\b/their/g' /home/BSD.txt > /home/BSD_REPLACE.txt`

This command reads the contents of the file `/home/BSD_REPLACE.txt` and filters out the lines that contain the word "the", displaying them as output: `cat BSD_REPLACE.txt |grep the`

============================================================================================================================
# Instructions

The system admins team of `xFusionCorp Industries` has set up some scripts on `jump host` that run on regular intervals and perform operations on all app servers in `Stratos Datacenter`. To make these scripts work properly we need to make sure the `thor` user on jump host has password-less SSH access to all app servers through their respective sudo users (i.e `tony` for app server 1). Based on the requirements, perform the following:

Set up a password-less authentication from user `thor` on jump host to all app servers through their respective sudo users.

# Solution

ssh-keygen -t rsa

ssh-copy-id -i ~/.ssh/id_rsa.pub tony@stapp01

ssh tony@stapp01

ssh-copy-id -i ~/.ssh/id_rsa.pub steve@stapp02

ssh steve@stapp02

ssh-copy-id -i ~/.ssh/id_rsa.pub banner@stapp03

ssh banner@stapp03

============================================================================================================================
# Instructions

During a routine security audit, the team identified an issue on the Nautilus App Server. Some malicious content was identified within the website code. After digging into the issue they found that there might be more infected files. Before doing a cleanup they would like to find all similar files and copy them to a safe location for further investigation. Accomplish the task as per the following requirements:

a. On `App Server 2` at location `/var/www/html/media` find out all files (not directories) having `.php` extension.

b. Copy all those files along with their `parent directory structure` to location `/media` on same server.

c. Please make sure not to copy the entire `/var/www/html/media` directory content.

# Solution

ssh steve@stapp02

Find all files with a .php extension in the /var/www/html/media directory:`find /var/www/html/media -type f -name "*.php"`

sudo su

Copy the files to the /media directory on the same server while preserving the directory structure:

mkdir -p /media

find /var/www/html/media -type f -name "*.php" -exec cp --parents {} /media \;

This command creates the /media directory (`mkdir -p /media`) if it doesn't exist. Then, it uses `find` to search for files with the .php extension and copies them to the /media directory while preserving the directory structure (`-exec cp --parents {} /media \;`).

Verify that the files and their parent directory structure were copied to the /media directory: `ls -R /media`


============================================================================================================================
# Instructions

As per new application requirements shared by the `Nautilus` project development team, serveral new packages need to be installed on all app servers in `Stratos Datacenter`. Most of them are completed except for `epel-release`.

Therefore, install the  `epel-release` package on all `app-servers`.

# Solution

ssh tony@stapp01

ssh steve@stapp02

ssh banner@stapp03

Once connected to a server, execute the appropriate command to install the `epel-release` package: `sudo yum install epel-release`

Check the result: `rpm -q epel-release`


============================================================================================================================
# Instructions

During the weekly meeting, the Nautilus DevOps team discussed about the automation and configuration management solutions that they want to 
implement. While considering several options, the team has decided to go with `Ansible` for now due to its simple setup and minimal pre-requisites. The team wanted to start testing using Ansible, so they have decided to use `jump host` as an Ansible controller to test different kind of tasks on rest of the servers.

Install `ansible` version `4.7.0` on `Jump host` using `pip3`only. Make sure Ansible binary is available globally on this system, i.e all users on this system are able to run Ansible commands.

# Solution

Install Ansible version 4.7.0 using pip3:`sudo pip3 install "ansible==4.7.0"`


============================================================================================================================

# Instructions

The `Nautilus` production support team and security team had a meeting last month in which they decided to use local yum repositories for maintaing packages needed for their servers. For now they have decided to configure a local yum repo on `Nautilus Backup Server`. This is one of the pending items from last month, so please configure a local yum repository on `Nautilus Backup Server` as per details given below.

a. We have some packages already present at location `/packages/downloaded_rpms/` on `Nautilus Backup Server`.

b. Create a yum repo named `localyum` and make sure to set `Repository ID` to `localyum`.  Configure it to use package's location `/packages/downloaded_rpms/`.

c. Install package `wget` from this newly created repo.

# Solution

ssh clint@stbkp01

Open a terminal on the Nautilus Backup Server and run the following command to create a new repository configuration file: `sudo vi /etc/yum.repos.d/localyum.repo`

add

```YAML
[localyum]
name=localyum
baseurl=file:///packages/downloaded_rpms/
enabled=1
gpgcheck=0
```

To install the `wget` package from the newly created `localyum` repository, run the following command: `sudo yum install wget --disablerepo="*" --enablerepo="localyum”`

`yum list wget`


============================================================================================================================
# Instructions

As per details shared by the development team, the new application release has some dependencies on the back end. There are some packages/services that need to be installed on all app servers under `Stratos Datacenter`. As per requirements please perform the following steps:

a. Install `postfix` package on all the application servers.

b. Once installed, make sure it is enabled to start during boot.

# Solution

ssh tony@stapp01

ssh steve@stapp02

ssh banner@stapp03

Run the following command to install the `postfix` package: `sudo yum install postfix`

After the installation completes successfully, enable the `postfix` service to start automatically during boot. Use the following command: `sudo systemctl enable postfix`

To check if the `postfix` package is installed, run the following command: `rpm -q postfix`

To check if the `postfix` service is enabled to start during boot, run the following command: `systemctl is-enabled postfix`

============================================================================================================================
# Instructions

We have some users on all app servers in `Stratos Datacenter`. Some of them have been assigned some new roles and responsibilities,  therefore their users need to be upgraded with sudo access so that they can perform admin level tasks.

a. Provide sudo access to user `rose` on all app servers.

b. Make sure you have set up password-less sudo for the user.

# Solution

ssh tony@stapp01

ssh steve@stapp02

ssh banner@stapp03

Add the user "rose" to the sudoers file by running the following command: `sudo visudo`

Add the following line below the `%sudo` or `%wheel` line to provide sudo access specifically to the "rose" user: `rose ALL=(ALL) NOPASSWD: ALL`

Check the result: `sudo cat /etc/sudoers |grep rose`


============================================================================================================================

# Instructions

The system admins team of xFusionCorp Industries has noticed intermittent issues with DNS resolution in several apps . `App Server 2` in `Stratos Datacenter` is having some DNS resolution issues, so we want to add some additional DNS nameservers on this server.

As a temporary fix we have decided to go with Google public DNS (ipv4). Please make appropriate changes on this server.

# Solution

ssh steve@stapp02

Open the network configuration file for editing. The location of the network configuration file may vary depending on the Linux distribution and version, but it is commonly found at `/etc/resolv.conf`. Use the following command to open the file in a text editor with superuser (sudo) privileges: `sudo vi /etc/resolv.conf`

Add the following lines below the existing `nameserver` line to add the Google public DNS nameserver: `nameserver 8.8.8.8`

After making these changes, the App Server 2 in the Stratos Datacenter should start using the Google public DNS nameservers for DNS resolution. This can help address the intermittent DNS resolution issues in the apps on that server.

To test the DNS resolution, you can use commands like `ping` or `nslookup` to check if DNS queries are resolving successfully. For example: `sudo ping google.com`

============================================================================================================================
# Instructions

To secure our `Nautilus` infrastructure in `Stratos Datacenter` we have decided to install and configure `firewalld`on all app servers. We have Apache and Nginx services running on these 
apps. Nginx is running as a reverse proxy server for Apache. We might have more robust firewall settings in the future, but for now we have decided to go with the given requirements listed below:

a. Allow all incoming connections on Nginx port, i.e `80`.

b. Block all incoming connections on Apache port, i.e `8080`.

c. All rules must be permanent.

d. Zone should be public.

e. If Apache or Nginx services aren't running already, please make sure to start them.

# Solution

ssh tony@stapp01

ssh steve@stapp02

ssh banner@stapp03

Install firewalld: `sudo yum install firewalld`

Start and enable firewalld to ensure it starts automatically on system boot:

```
sudo systemctl start firewalld
sudo systemctl enable firewalld
```

Configure the firewall rules as per the given requirements:

```
# Allow incoming connections on Nginx port (80)
sudo firewall-cmd --permanent --zone=public --add-port=80/tcp

# Block incoming connections on Apache port (8080)
sudo firewall-cmd --permanent --zone=public --remove-port=8080/tcp

# Reload the firewall to apply the changes
sudo firewall-cmd --reload
```

To check the existing Apache httpd & Nginx service status: `systemctl status httpd &&  systemctl status nginx

============================================================================================================================
# Instructions

`xFusionCorp Industries` has planned to set up a common email server in `Stork DC`. After several meetings and recommendations they have decided to use `postfix` as their `mail transfer agent` and `dovecot` as an `IMAP/POP3` server. We would like you to perform the following steps:

1. Install and configure `postfix` on `Stork DC` mail server.

2. Create an email account `siva@stratos.xfusioncorp.com` identified by `B4zNgHA7Ya`.

3. Set its mail directory to `/home/siva/Maildir`.

4. Install and configure `dovecot` on the same server.

# Solution

`ssh groot@stmail01`

`sudo su`

`rpm -qa | grep postfix` 

`rpm -qa | grep devecot`

`yum install postfix -y`

`vi /etc/postfix/main.cf`

Change these lines:

```
myhostname = [stmail01.stratos.xfusioncorp.com](http://stmail01.stratos.xfusioncorp.com/)

mydomain = [stratos.xfusioncorp.com](http://stratos.xfusioncorp.com/)

myorigin = $mydomain

inet_interfaces = all

#inet_interfaces = localhost

#mydestination = $myhostname, localhost.$mydomain, localhost
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain

mynetworks = 172.16.238.0/24, 127.0.0.0/8

home_mailbox = Maildir/
```

`systemctl start postfix`

`systemctl status postfix`



`useradd siva`

`passwd siva` (`B4zNgHA7Ya`)

`cat /etc/passwd | grep siva`


Add these commands:

```
telnet stmail01 25

EHLO localhost

mail from:siva@stratos.xfusioncorp.com

rcpt to:siva@stratos.xfusioncorp.com

DATA

Test mail

.
```

`su - siva`

`cd Maildir`


Check `new`


Go back to stmail01 the and install dovecot

`yum install dovecot -y`

`vi /etc/dovecot/dovecot.conf`

Update this line:

```
protocols = imap pop3 lmtp submissio
```

`vi /etc/dovecot/conf.d/10-mail.conf`

Change this line:

```
mail_location = maildir:~/Maildir
```

`vi /etc/dovecot/conf.d/10-auth.conf`

Change these lines:

```
disable_plaintext_auth = yes
auth_mechanisms = plain login
```

`vi /etc/dovecot/conf.d/10-master.conf`

Change these lines:

```
    user = postfix
    group = postfix
```

`systemctl start dovecot`

`systemctl status dovecot`

`telnet stmail01 110`

============================================================================================================================

# Instructions


Some users of the monitoring app have reported issues with xFusionCorp Industries mail server. They have a mail server in Stork DC where they are using postfix mail transfer agent. Postfix service seems to fail. Try to identify the root cause and fix it.

# Solution

`ssh groot@stmail01`

`sudo su -`

`systemctl start postfix`

`systemctl status postfix -l`

`cat /etc/postfix/main.cf |grep inet_interface`

Change this line:

```
#inet_interfaces = localhost
```

`systemctl start postfix`

`systemctl status postfix`


Check the service:

`telnet stmail01 25`

============================================================================================================================
# Instructions

There is a static website running in Stratos Datacenter. They have already configured the app servers and code is already deployed there. To make it work properly, they need to configure LBR server. There are number of options for that, but team has decided to go with HAproxy. FYI, apache is running on port 8087 on all app servers. Complete this task as per below details.

a. Install and configure HAproxy on LBR server using yum only and make sure all app servers are added to HAproxy load balancer. HAproxy must serve on default http port (Note: Please do not remove stats socket /var/lib/haproxy/stats entry from haproxy default config.).

b. Once done, you can access the website using StaticApp button on the top bar.

# Solution

`ssh loki@stlb01`

`sudo su -`

`yum install haproxy -y`

`cd /etc/haproxy`

`vi haproxy.cfg`

```python
frontend main
    bind *:80

#    server  app4 127.0.0.1:5004 check
```

`ssh tony@stapp01`

`sudo su -`

`cd /etc/httpd/conf`

`vi httpd.conf` (find Listen 8087)

go back to  `haproxy.cfg`  and make these changes:

```python
backend app
    balance     roundrobin
    server  stapp01 172.16.238.10:8087 check
    server  stapp02 172.16.238.11:8087 check
    server  stapp03 172.16.238.12:8087 check
```

`systemctl enable haproxy`

`systemctl start haproxy`

`systemctl status haproxy`


============================================================================================================================

# Instructions

`xFusionCorp Industries` has an application running on `Nautlitus` infrastructure in `Stratos Datacenter`. The monitoring tool  recognised that there is an issue with the `haproxy` service on `LBR` server. That needs to fixed to make the application work properly.

Troubleshoot and fix the issue, and make sure `haproxy` service is running on `Nautilus LBR` server. Once fixed, make sure you are able to access the website using `StaticApp` button on the top bar.

# Solution

`ssh loki@stlb01`

`sudo su -`

`systemctl status haproxy`

`haproxy -c -f /etc/haproxy/haproxy.cfg`

`cat /etc/haproxy/haproxy.cfg |grep haprox`


If you check `id haprox`, you will get: `id: haprox: no such user`

However, there if you check `id haproxy`, the result will be: `uid=188(haproxy) gid=188(haproxy) groups=188(haproxy)`

Just change this line in `haproxy.cfg`

```python
user        haproxy
```

Check again: `haproxy -c -f /etc/haproxy/haproxy.cfg`

You should see: `Configuration file is valid`

`systemctl start haproxy`

`systemctl status haproxy`

You should see: `Active: active (running)`

============================================================================================================================
# Instructions

There is a critical issue going on with the `Nautilus` application in `Stratos DC`. The production support team identified that the application is unable to connect to the database. After digging into the issue, the team found that mariadb service is down on the database server.

Look into the issue and fix the same.

# Solution

`ssh peter@stdb01`

`sudo su -`

`systemctl status mariadb`



`cd /bin`

`sudo yum remove mariadb-server`

`sudo yum install mariadb-server`

`systemctl start mariadb`

`systemctl status  mariadb`


============================================================================================================================
# Instructions

The production support team of `xFusionCorp Industries`is working on developing some bash scripts to automate different day to day tasks. One is to create a bash script for taking websites backup. 
They have a static website running on `App Server 2` in `Stratos Datacenter`, and they need to create a bash script named `ecommerce_backup.sh` which should accomplish the following tasks. (Also remember to place the script under `/scripts` directory on `App Server 2`).

a. Create a zip archive named `xfusioncorp_ecommerce.zip` of `/var/www/html/ecommerce` directory.

b. Save the archive in `/backup/` on `App Server 2`. This is a temporary storage, as backups from this location will be clean on weekly basis. Therefore, we also need to save this backup archive on `Nautilus Backup Server`.

c. Copy the created archive to `Nautilus Backup Server` server in `/backup/` location.

d. Please make sure script won't ask for password while copying the archive file. Additionally, the respective server user (for example, `tony` in case of `App Server 1`) must be able to run it.

# Solution

`ssh steve@stapp02`

`vi /scripts/ecommerce_backup.sh`

Add this script

```python
#!/bin/bash

zip -r /backup/xfusioncorp_ecommerce.zip /var/www/html/ecommerce

scp /backup/xfusioncorp_ecommerce.zip clint@stbkp01:/backup
```

`ssh-keygen`

`ssh-copy-id clint@stbkp01`

Try to connect: `ssh clint@stbkp01`

`logout`

`cd /scripts`

`sh ecommerce_backup.sh`

`cd /backup`

`ls`

`ssh clint@stbkp01`

`cd /backup`

`ls`


============================================================================================================================
# Instructions

We are working on hardening Apache web server on all app servers. As a part of this process we want to add some of the Apache response headers for security purpose. We are testing the settings one by one on all app servers. As per details mentioned below enable these headers for Apache:

1. Install `httpd` package on `App Server 3` using yum and configure it to run on `8089` port, make sure to start its service.
2. Create an `index.html` file under Apache's default document root i.e `/var/www/html` and add below given content in it.
    
    `Welcome to the xFusionCorp Industries!`
    
3. Configure Apache to enable below mentioned headers:
    
    `X-XSS-Protection` header with value `1; mode=block`
    
    `X-Frame-Options` header with value `SAMEORIGIN`
    
    `X-Content-Type-Options` header with value `nosniff`
    

`Note:` You can test using curl on the given app server as LBR URL will not work for this task.

# Solution

`ssh banner@stapp03`

`sudo su -`

`yum install httpd -y`

`vi /etc/httpd/conf/httpd.conf`

Find this part and change Listen to 8089 and add Header at the end

```python
#Listen 12.34.56.78:80
Listen 8089 

<...>

<IfModule mod_headers.c>

Header set X-XSS-Protection "1; mode=block"
Header always append X-Frame-Options SAMEORIGIN
Header set X-Content-Type-Options nosniff

</IfModule>
```

`vi /var/www/html/index.html`

Enter:

```python
Welcome to the xFusionCorp Industries!
```

`systemctl start httpd`

`systemctl status  httpd`

`curl http://localhost:8089`

`curl -i http://localhost:8089`

============================================================================================================================
# Instructions

`xFusionCorp Industries`uses some monitoring tools to check the status of every service, application, etc running on the systems. Recently, the monitoring system identified that Apache service is not running on some of the `Nautilus Application Servers` in `Stratos Datacenter`.

1. Identify the faulty `Nautilus Application Server` and fix the issue.  Also, make sure Apache service is up and running on all `Nautilus Application Servers`. Do not try to stop any kind of firewall that is already running.

2. Apache is running on `8089` port on all `Nautilus Application Servers` and its document root must be `/var/www/html` on all app servers.

3. Finally you can test from `jump host` using curl command to access Apache on all app servers and it should be reachable and you should get some static page. E.g. `curl http://172.16.238.10:8089/`.

# Solution

`ssh tony@stapp01`

`ssh steve@stapp02`

`ssh banner@stapp03`

`sudo su -`

`systemctl start httpd`

You should see a similar message

```python
Job for httpd.service failed because the control process exited with error code.
See "systemctl status httpd.service" and "journalctl -xe" for details.
```

`systemctl status  httpd.service`

```python
AH00526: Syntax error on line 45 of /etc/httpd/conf/httpd.conf:
Invalid command 'Listen 8089', perhaps misspelled or defined by a module not included in the server configura>

<...>

AH00526: Syntax error on line 122 of /etc/httpd/conf/httpd.conf:
DocumentRoot '/var/www/html;' is not a directory, or is not readable
```

`vi /etc/httpd/conf/httpd.conf`

Fix your errors

`systemctl start httpd  &&  systemctl  status httpd`

`curl http://172.16.238.10:8089/`


============================================================================================================================
# Instructions

We have confidential data that needs to be transferred to a remote location, so we need to encrypt that data.We also need to decrypt data we received from a remote location in order to understand its content.

On `storage server` in `Stratos Datacenter` we have private and public keys stored at `/home/*_key.asc`. Use these keys to perform the following actions.

- Encrypt `/home/encrypt_me.txt` to `/home/encrypted_me.asc`.

- Decrypt `/home/decrypt_me.asc` to `/home/decrypted_me.txt`. (Passphrase for decryption and encryption is `kodekloud`).

- The user ID you can use is `kodekloud@kodekloud.com`.

# Solution

`ssh natasha@ststor01`

`sudo su -`

`cd /home`

`gpg --import public_key.asc`

`gpg --import private_key.asc`

`gpg --list-keys`

`gpg --list-secret-keys`

`gpg --encrypt -r kodekloud@kodekloud.com --armor < encrypt_me.txt  -o encrypted_me.asc`

`gpg --decrypt decrypt_me.asc > decrypted_me.txt`

`cat decrypted_me.txt`

`cat decrypt_me.asc`

`cat encrypt_me.txt`

`cat encrypted_me.asc`


============================================================================================================================
# Instructions

The `Nautilus` DevOps team is ready to launch a new application, which they will deploy on app servers in `Stratos Datacenter`. They are expecting significant traffic/usage of `httpd` on app servers after that. This will generate massive logs, creating huge log files. To utilise the storage efficiently, they need to compress the log files and need to rotate old logs. Check the requirements shared below:

a. In all app servers install `httpd` package.

b. Using `logrotate` configure `httpd` logs rotation to monthly and keep only 3 rotated logs.

(If by default log rotation is set, then please update configuration as needed)

# Solution

`ssh tony@stapp01`

`ssh steve@stapp02`

`ssh banner@stapp03`

`sudo su -`

`yum install -y httpd`

`vi /etc/logrotate.d/httpd`

Change as per task

```python
/var/log/httpd/*log {
    monthly
    rotate 3
    missingok
    notifempty
    sharedscripts
    delaycompress
    postrotate
        /bin/systemctl reload httpd.service > /dev/null 2>/dev/null || true
    endscript
}
~
```

`systemctl start httpd`

`systemctl status httpd`


============================================================================================================================
# Instructions

We have a backup management application UI hosted on `Nautilus's` backup server in `Stratos DC`.  That backup management application code is deployed under Apache on the backup server itself, and Nginx is running as a reverse proxy on the same server. Apache and Nginx ports are `6200` and `8097`, respectively. We have iptables firewall installed on this server. Make the appropriate changes to fulfill the requirements mentioned below:

We want to open all incoming connections to Nginx's port and block all incoming connections to Apache's port. Also make sure rules are permanent.

# Solution

`ssh clint@stbkp01`

`sudo su -`

`systemctl status iptables`

`ss -tlnp |grep http`

`ss -tlnp |grep nginx`

`systemctl start iptables`

`systemctl status iptables`

`iptables -A INPUT -p tcp --dport 8097 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT`

`iptables -A INPUT -p tcp --dport 6200 -m conntrack --ctstate NEW -j REJECT`

`iptables -L --line-numbers`

`iptables -R INPUT 5 -p icmp -j REJECT`

`service iptables save`

`cat /etc/sysconfig/iptables`

`iptables -L -n -v`

============================================================================================================================
