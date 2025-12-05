# Instructions

The Nautilus devops team got some requirements related to some Apache config changes. They need to setup some redirects for some URLs. There might be some more changes need to be done. Below you can find more details regarding that:

1.) `httpd` is already installed on `app server 3`. Configure Apache to listen on port `5002`.

1. Configure Apache to add some redirects as mentioned below:
    
    a.) Redirect `http://stapp03.stratos.xfusioncorp.com:<Port>/` to `http://www.stapp03.stratos.xfusioncorp.com:<Port>/` i.e `non www` to `www`. This must be a permanent redirect i.e `301`
    
    b.) Redirect `http://www.stapp03.stratos.xfusioncorp.com:<Port>/blog/` to `http://www.stapp03.stratos.xfusioncorp.com:<Port>/news/`. This must be a temporary redirect i.e `302`.


# Solution

`ssh banner@stapp03`

`sudo su -`

Open the Apache configuration file for editing: `sudo vi /etc/httpd/conf/httpd.conf`

Locate the line that specifies the `Listen` directive and change it to:

```bash
#Listen 12.34.56.78:80
Listen 5002
```

Configure the redirects by adding the following lines to the Apache configuration file, preferably at the end:

```bash
<VirtualHost *:5002>
    ServerName stapp03.stratos.xfusioncorp.com
    Redirect permanent / http://www.stapp03.stratos.xfusioncorp.com:5002/
</VirtualHost>

<VirtualHost *:5002>
    ServerName www.stapp03.stratos.xfusioncorp.com
    Redirect temp /blog/ http://www.stapp03.stratos.xfusioncorp.com:5002/news/
</VirtualHost>
```

Restart the Apache service for the changes to take effect: `sudo systemctl restart httpd`

Check the result.

Test the non-www to www permanent redirect:`curl -I http://stapp03.stratos.xfusioncorp.com:5002/`

The `-I` option is used to fetch the HTTP headers only. Look for the `Location` header in the response. It should contain `http://www.stapp03.stratos.xfusioncorp.com:5002/` and the response code should be `301 Moved Permanently`.

```bash
[root@stapp03 ~]# curl -I http://stapp03.stratos.xfusioncorp.com:5002/
HTTP/1.1 301 Moved Permanently
Date: Sat, 04 May 2024 17:07:30 GMT
Server: Apache/2.4.6 (CentOS) PHP/7.2.26
Location: http://www.stapp03.stratos.xfusioncorp.com:5002/
Content-Type: text/html; charset=iso-8859-1
```

Test the temporary redirect:curl -I http://www.stapp03.stratos.xfusioncorp.com:5002/blog/
Again, look for the Location header in the response. It should contain http://www.stapp03.stratos.xfusioncorp.com:5002/news/ and the response code should be 302 Found.

```bash
[root@stapp03 ~]# curl -I http://www.stapp03.stratos.xfusioncorp.com:5002/blog/
HTTP/1.1 302 Found
Date: Sat, 04 May 2024 17:08:17 GMT
Server: Apache/2.4.6 (CentOS) PHP/7.2.26
Location: http://www.stapp03.stratos.xfusioncorp.com:5002/news/
Content-Type: text/html; charset=iso-8859-1
```

============================================================================================================================
# Instructions

Some of the developers from `Nautilus` project team have asked for SFTP access to at least one of the app server in `Stratos DC`. After going through the requirements,  the system admins team has decided to configure the SFTP server on `App Server 1` server in `Stratos Datacenter`. Please configure it as per the following instructions:

a. Create a SFTP user `javed` and set its password to `BruCStnMT5`. There is already a group called `ftp`, you can utilise the same.

b. Password authentication should be enabled for this user.

c. SFTP user should only be allowed to make SFTP connections.

# Solution

`ssh tony@stapp01`

`sudo su -`

`sudo useradd javed  -s /bin/false -m -G ftp`

`sudo passwd javed`

`chown -R root:root /home/javed`

`vi /etc/ssh/sshd_config`

```bash
Subsystem sftp internal-sftp
Match User javed
	ForceCommand internal-sftp
	PasswordAuthentication yes
	ChrootDirectory %h
	PermitTunnel no
	AllowAgentForwarding no
	AllowTcpForwarding no
	X11Forwarding no
```

`sudo systemctl restart sshd`

`sftp javed@localhost`


============================================================================================================================

# Instructions

The `Nautilus` application development team recently finished the beta version of one of their Java-based applications, which they are planning to deploy on one of the app servers in `Stratos DC`. After an internal team meeting, they have decided to use the `tomcat` application server. Based on the requirements mentioned below complete the task:

a.  Install `tomcat` server on `App Server 2`.

b. Configure it to run on port `6300`.

c. There is a `ROOT.war` file on `Jump host` at location `/tmp`.

Deploy it on this tomcat server and make sure the webpage works directly on base URL i.e `curl http://stapp02:6300`

# Solution

from thor@jump_host:

`scp /tmp/ROOT.war steve@stapp02:/tmp`

`ssh steve@stapp02`

`sudo yum  update`

`sudo yum install tomcat`

`sudo su -`

`vi /usr/share/tomcat/conf/server.xml`

```bash
   #change ports
    <Connector port="6300" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    <!-- A "Connector" using the shared thread pool-->
    <!--
    <Connector executor="tomcatThreadPool"
               port="6300" protocol="HTTP/1.1"

```
`cp /tmp/ROOT.war /usr/share/tomcat/webapps`

`systemctl restart tomcat`

`systemctl status tomcat`

`curl -i http://stapp02:6300`

============================================================================================================================
# Instructions

Our monitoring tool has reported an issue in `Stratos Datacenter`. One of our app servers has an issue, as its Apache service is not reachable on port `3000`(which is the Apache port). The service itself could be down, the firewall could be at fault, or something else could be causing the 
issue.

Use tools like `telnet`, `netstat`, etc. to find and fix the issue. Also make sure Apache is reachable from the jump host without compromising any security settings.

Once fixed, you can test the same using command `curl http://stapp01:3000` command from jump host.

# Solution

Check which app has an issue:

`sudo curl 172.16.238.10:3000`

`sudo curl 172.16.238.11:3000`           

`sudo curl 172.16.238.12:3000`   

In my case it was stapp01

`ssh tony@stapp01`

`sudo su -`

`systemctl status httpd &&  systemctl start httpd`

```bash
May 12 11:12:51 stapp01.stratos.xfusioncorp.com httpd[783]: (98)Address already in use: AH00072: make_sock: could not bind to 
address 0.0.0.0:3000
```

`netstat -tulnp`

```bash
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      568/sshd            
tcp        0      0 127.0.0.1:3000          0.0.0.0:*               LISTEN      722/sendmail: accep 
tcp        0      0 127.0.0.11:45349        0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      568/sshd            
udp        0      0 127.0.0.11:52284        0.0.0.0:*                           -
```

`kill 722`

`systemctl start httpd`

`systemctl enable httpd`

`systemctl status -l httpd`

`iptables -I INPUT -p tcp --dport 3000 -j ACCEPT`

`service iptables save`

Check the result: `curl http://stapp01:3000`

============================================================================================================================

# Instructions

We have one of our websites up and running on our `Nautilus` infrastructure in `Stratos DC`. Our security team has raised a concern that right now Apache’s port i.e `5000`is open for all since there is no firewall installed on these hosts. So we have decided to add some security layer for these hosts and after discussions and recommendations we have come up with the following requirements:

1. Install `iptables` and all its dependencies on each app host.

2. Block incoming port `5000` on all apps for everyone except for LBR host.

3. Make sure the rules remain, even after system reboot.


# Solution

`ssh tony@stapp01`

`ssh steve@stapp02`

`ssh banner@stapp03`

`sudo su -`

`sudo yum install iptables-services -y`

`sudo systemctl start iptables`

`sudo systemctl enable iptables`

`sudo systemctl status iptables`

`sudo iptables -nvL`

`sudo iptables -R INPUT 5 -p tcp --dport 5000 -s 172.16.238.14 -j ACCEPT`

`sudo iptables -A INPUT -p tcp --dport 5000 -j DROP`

`sudo service iptables save`

`curl 172.16.238.10:5000`

`curl 172.16.238.11:5000`

`curl 172.16.238.12:5000`

============================================================================================================================
# Instructions

`Nautilus` system admin's team is planning to deploy a front end application for their backup utility on `Nautilus Backup Server`, so that they can manage the backups of different websites from a graphical user interface. They have shared requirements to set up the same; please accomplish the tasks as per detail given below:

a. Install `Apache` Server on `Nautilus Backup Server` and configure it to use `8087` port (do not bind it to `127.0.0.1` only, keep it default i.e let Apache listen on server's IP, hostname, localhost, 127.0.0.1 etc).

b. Install `Nginx` webserver on `Nautilus Backup Server` and configure it to use `8092`.

c. Configure `Nginx` as a reverse proxy server for `Apache`.

d. There is a sample index file `/home/thor/index.html` on `Jump Host`, copy that file to `Apache's` document root.

e. Make sure to start `Apache` and `Nginx` services.

f. You can test final changes using `curl` command, e.g `curl http://<backup server IP or Hostname>:8092`.

# Solution

`scp /home/thor/index.html clint@stbkp01:/tmp/`

`ssh clint@stbkp01`

`sudo su -`

`yum install httpd`

`yum install epel-release`

`yum install  nginx`

`vi /etc/httpd/conf/httpd.conf`

Change: Listen 8087

`vi /etc/nginx/nginx.conf`

Make changes

```bash
    server {
        listen       8092 default_server;
        listen       [::]:8092 default_server;
        server_name  172.16.238.16;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        proxy_pass http://172.16.238.16:8092;
        }
```

`cp /tmp/index.html /var/www/html/`

`systemctl start httpd`

`systemctl start nginx`

```bash
[root@stbkp01 ~]# curl http://172.16.238.16:8087
Welcome to xFusionCorp Industries!
[root@stbkp01 ~]# curl http://172.16.238.16:8092
<html>
<head><title>500 Internal Server Error</title></head>
<body bgcolor="white">
<center><h1>500 Internal Server Error</h1></center>
<hr><center>nginx/1.14.1</center>
</body>
</html>
[root@stbkp01 ~]#
```

============================================================================================================================

# Instructions

`xFusionCorp Industries` has hosted several static websites on `Nautilus Application Servers` in `Stratos DC`. There are some confidential directories in the document root that need to be password protected. Since they are using `Apache` for hosting the websites, the production support team has decided to use `.htaccess` with basic `auth`. There is a website that needs to be uploaded to `/var/www/html/itadmin` on `Nautilus App Server 2`. However, we need to set up the authentication before that.

1. Create `/var/www/html/itadmin` direcotry if doesn't exist.

2. Add a user `jim` in `htpasswd` and set its password to `TmPcZjtRQx`.

3. There is a file `/tmp/index.html` present on `Jump Server`. Copy the same to the directory you created, please make sure default document root should remain `/var/www/html`. Also website should work on URL `http://<app-server-hostname>:8080/itadmin/`

# Solution

`scp /tmp/index.html steve@stapp02:/home/steve/`

`ssh steve@stapp02`

`sudo su -`

`sudo systemctl start httpd`

`sudo systemctl enable httpd`

`sudo systemctl status httpd`

`cd /var/www/html/`

`sudo vi .htaccess`

```bash
  AuthType Basic
  AuthName "Secure Content"
  AuthUserFile /var/www/html/itadmin/.htpasswd
  require valid-user
```

`sudo mkdir itadmin`

`sudo mv /home/steve/index.html /var/www/html/itadmin/`

`sudo htpasswd -c /var/www/html/itadmin/.htpasswd jim`

`cd /etc/httpd/conf/`

`sudo vi httpd.conf`

```bash
<Directory /var/www/html>
    AllowOverride AuthConfig
    Require all denied
</Directory>
```

`sudo systemctl restart httpd`

`sudo curl http://localhost:8080`

`sudo curl http://localhost:8080 -u jim`

============================================================================================================================
# Instructions

The production support team of xFusionCorp Industries has deployed some of the latest monitoring tools to keep an eye on every service, application, etc. running on the systems. One of the monitoring systems reported about Apache service unavailability on one of the app servers in `Stratos DC`.

Identify the faulty app host and fix the issue. Make sure Apache service is up and running on all app hosts. They might not hosted any code yet on these servers so you need not to worry about if Apache isn't serving any pages or not, just make sure service is up and running. Also, make sure Apache is running on port `3003` on all app servers.

# Solution

`ssh tony@stapp01`

`ssh steve@stapp02`

`ssh banner@stapp03`

`sudo su -`

`systemctl start httpd`

Issue on the first app

```bash
[root@stapp01 ~]# systemctl start httpd
Job for httpd.service failed because the control process exited with error code. See "systemctl status httpd.service" and "journalctl -xe" for details.
```

`systemctl status httpd -l`

`netstat -ntpl`

```bash
[root@stapp01 ~]# netstat -ntpl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:3003          0.0.0.0:*               LISTEN      553/sendmail: accep 
tcp        0      0 127.0.0.11:39261        0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/init              
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      429/sshd            
tcp6       0      0 :::111                  :::*                    LISTEN      412/rpcbind         
tcp6       0      0 :::22                   :::*                    LISTEN      429/sshd
```

`kill 553`

`systemctl start httpd`

`systemctl enable httpd`

```bash
[root@stapp01 ~]# netstat -ntpl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:3003            0.0.0.0:*               LISTEN      649/httpd           
tcp        0      0 127.0.0.11:39261        0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/init              
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      429/sshd            
tcp6       0      0 :::111                  :::*                    LISTEN      412/rpcbind         
tcp6       0      0 :::22                   :::*                    LISTEN      429/sshd
```

`curl localhost:3003`
============================================================================================================================

# Instructions

We have a requirement where we want to password protect a directory in the Apache web server document root. We want to password protect `http://<website-url>:<apache_port>/protected` URL as per the following  requirements (you can use any `website-url` for it like `localhost` since there are no such specific requirements as of now). Setup the same on `App server 3` as per below mentioned requirements:

a. We want to use basic authentication.

b. We do not want to use `htpasswd` file based authentication. Instead, we want to use `PAM` authentication, i.e `Basic Auth + PAM` so that we can authenticate with a Linux user.

c. We already have a user `ammar` with password `LQfKeWWxWD` which you need to provide access to.

d. You can test the same using a curl command from jump host `curl http://<website-url>:<apache_port>/protected`.

# Solution

`ssh banner@stapp03`

`sudo su -`


# dnf -y install mod_authnz_pam
# vi /etc/httpd/conf.modules.d/55-authnz_pam.conf

vi /etc/httpd/conf.d/authnz_pam.conf
<Directory "/var/www/html/auth-pam">
    AuthType Basic
    AuthName "PAM Authentication"
    AuthBasicProvider PAM
    AuthPAMService httpd-auth
    Require valid-user
</Directory>

# vi /etc/pam.d/httpd-auth
# create new
auth       include      system-auth
account    include      system-auth

# change permission httpd can read shadow
# chgrp apache /etc/shadow
# chmod 440 /etc/shadow
# systemctl reload httpd

`curl -u ammar:LQfKeWWxWD http://localhost:8080/protected/`

```bash
[root@stapp03 ~]# curl -u ammar:LQfKeWWxWD http://localhost:8080/protected/
This is KodeKloud Protected Directory
```

============================================================================================================================
# Instructions

The system admins team of `xFusionCorp Industries` needs to deploy a new application on `App Server 2` in `Stratos Datacenter`. They have some pre-requites to get ready that server for application deployment. Prepare the server as per requirements shared below:

1. Install and configure `nginx` on `App Server 2`.

2. On `App Server 2` there is a self signed SSL certificate and key present at location `/tmp/nautilus.crt` and `/tmp/nautilus.key`. Move them to some appropriate location and deploy the same in Nginx.

3. Create an `index.html` file with content `Welcome!` under Nginx document root.

4. For final testng try to access the `App Server 2` link (either hostname or IP) from `jump host` using curl command. For example `curl -Ik https://<app-server-ip>/`.

# Solution

`ssh steve@stapp02`

`sudo su -`

`yum install epel-release -y`

`yum install  -y nginx`

`systemctl start nginx`

`systemctl enable nginx`

`systemctl status nginx`

`yum -y install mod_ssl`

`sudo unlink index.html`

`vi index.html  add Welcome!`

`cd /tmp`

`chmod 777 nautilus.crt nautilus.key`

`cp nautilus.crt /usr/share/nginx/html/`

`cp nautilus.key /usr/share/nginx/html/`

`cd /usr/share/nginx/html/`

`vi /etc/nginx/nginx.conf`

adjust the file

```bash
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        listen       443 ssl;
        server_name  172.16.238.11;
        root         /usr/share/nginx/html;
        index        index.html
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
        ssl_ciphers ECDHE+RSAGCM:ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:!aNULL!eNull:!EXPORT:!DES:!3DES:!MD5:!DSS;
        ssl_certificate      /usr/share/nginx/html/nautilus.crt;
        ssl_certificate_key  /usr/share/nginx/html/nautilus.key;

```

`systemctl restart nginx`

```bash
thor@jump_host ~$ curl -Ik https://172.16.238.11/
HTTP/1.1 200 OK
Server: nginx/1.14.1
Date: Sat, 18 May 2024 15:10:33 GMT
Content-Type: text/html
Content-Length: 9
Last-Modified: Sat, 18 May 2024 15:04:29 GMT
Connection: keep-alive
ETag: "6648c37d-9"
Accept-Ranges: bytes
```

