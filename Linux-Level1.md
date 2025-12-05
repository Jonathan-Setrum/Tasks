Linux Level 1

# Instructions

For security reasons the `xFusionCorp Industries` security team has decided to use custom Apache users for each web application hosted, rather than its default user. This will be the Apache user, so it shouldn't use the default home directory. Create the user as per requirements given below:

a. Create a user named `jim` on the `App server 2` in Stratos Datacenter.

b. Set its UID to `1582` and home directory to `/var/www/jim`.


# Solution

ssh into the App Server 2: `ssh steve@172.16.238.11`

This command creates a new user with the username "jim", sets the UID to 1582, creates the home directory `/var/www/jim`, and the `-m` option ensures that the home directory is created: `sudo useradd -u 1582 -d /var/www/jim -m jim` 

To check if the user "jim" was created successfully, you can use the following command: `id jim`

Running this command will display information about the user "jim", including the UID, GID (group ID), and the groups the user belongs to. If the user was created successfully, you will see output similar to the following:

======================================================================================================================

# Instructions

There are specific access levels for users defined by the `xFusionCorp Industries`
 system admin team. Rather than providing access levels to every 
individual user, the team has decided to create groups with required 
access levels and add users to that groups as needed. See the following 
requirements:

a. Create a group  named `nautilus_developers` in all App servers in `Stratos Datacenter`.

b. Add the user `mohammed` to  `nautilus_developers` group in all App servers. (create the user if doesn't exist).

# Solution

ssh into the App Server 1: `ssh tony@172.16.238.10`

ssh into the App Server 2: `ssh steve@172.16.238.11`

ssh into the App Server 3: `ssh banner@172.16.238.12`

To list all existing groups, you can run the command: `cat /etc/group`

Create the group "nautilus_developers" by running the following command on each App server:  `sudo groupadd nautilus_developers`

To check if the "nautilus_developers" group was created, you can use the following command: `grep -E "^nautilus_developers:" /etc/group`

To list all existing users, you can run the command: `cat /etc/passwd`

Create the user "mohammed" if they don't already exist. Run the following command on each App server: `sudo useradd mohammed`

To check if the user "mohammed" exists, you can use the following command: `grep -E "^mohammed:" /etc/passwd` This command searches for a line that starts with "mohammed:" in the `/etc/passwd` file. If the user exists, it will display information about the user, including the username, UID, home directory, and shell.

Add the user "mohammed" to the "nautilus_developers" group on each App server by running the following command: `sudo usermod -aG nautilus_developers mohammed`

- `a`: This option stands for "append" or "add". It is used to add the user to the specified group(s) without removing them from any existing groups. It ensures that the user's current group memberships are preserved while adding the new group.
- `G`: This option specifies the group(s) to which the user should be added. Multiple groups can be specified by separating them with commas. In this case, the group `nautilus_developers` is specified.

======================================================================================================================

# Instructions

The System admin team of `xFusionCorp Industries` has installed a backup agent tool on all app servers. As per the tool's requirements they need to create a user with a non-interactive shell.

Therefore, create a user named `mariyam` with a non-interactive shell on the `App Server 1`.

# Solution

ssh into the App Server 1: `ssh tony@172.16.238.10`

Run the following command to create the user "mariyam" with a non-interactive shell: `sudo useradd --shell /bin/false mariyam`

This command creates a user named "mariyam" and sets the shell to `/bin/false`, which is a non-interactive shell. Non-interactive shells are typically used for system accounts or accounts that do not require direct login access.

To check if a user named "mariyam" was successfully created on App Server 1, you can use the following command: `id mariyam`

Running this command will display information about the user "mariyam", including the UID (user ID), GID (group ID), and the groups the user belongs to. If the user was created successfully, you will see output similar to the following:  `uid=1001(mariyam) gid=1001(mariyam) groups=1001(mariyam)`

======================================================================================================================

# Instructions

The system admins team of `xFusionCorp Industries` has set up a new tool on all app servers, as they have a requirement to create a service user account that will be used by that tool.

Create a user named `anita` in `App Server 2` without a home directory.

# Solution

ssh into the App Server 2: `ssh steve@172.16.238.11`

Run the following command to create the user "anita" without a home directory: `sudo useradd --no-create-home anita`

This command creates a user named "anita" without creating a home directory for the user. The `--no-create-home` option ensures that a home directory is not created during the user creation process.

To check if the user was created: `grep "anita:" /etc/passwd`

To remove user: `sudo userdel --remove anita`

======================================================================================================================

# Instructions

A developer named kirsty has been assigned `Nautilus` project temporarily as a backup resource. As a temporary resource for this project, we need a temporary user for kirsty. It’s a good idea to create a user with an expiration date so that the user won't be able to access servers beyond that point.

Therefore, create a user named `kirsty` on the `App Server 1` in `Stratos Datacenter`. Set `expiry date` to `2021-02-17`. Make sure the user is created as per standard and is in lowercase.

# Solution

ssh into the App Server 1: `ssh tony@172.16.238.10`

Run the following command to create the user "kirsty" with a lowercase username: `sudo useradd --expiredate 2021-02-17 --create-home --shell /bin/bash kirsty`

To check if the user "kirsty" was created, you can use the `grep` command to search for the username in the `/etc/passwd` file: `grep "^kirsty:" /etc/passwd`

If the user was created successfully, you should see output similar to the following: `kirsty:x:1001:1001::/home/kirsty:/bin/bash`

To check the expiry date of the user "kirsty", you can use the `chage` command: `sudo chage -l kirsty`

This command displays the current aging information for the user "kirsty", including the expiry date. If the user has an expiry date set, you will see output similar to the following:

Last password change                                    : Nov 26, 2023

Password expires                                        : never

Password inactive                                       : never

Account expires                                         : Feb 17, 2021

Minimum number of days between password change          : 0

Maximum number of days between password change          : 99999

Number of days of warning before password expires       : 7


Or this option:

`sudo useradd --expiredate 2021-02-17 --create-home --user-group --shell /bin/bash --password $(openssl passwd -1) kirsty`

This command creates a user named "kirsty" with the specified options:

- `--expiredate 2021-02-17`: Sets the expiry date for the user account to 2021-02-17.
- `--create-home`: Creates a home directory for the user.
- `--user-group`: Creates a group with the same name as the user and assigns it as the primary group.
- `--shell /bin/bash`: Sets the user's default shell to `/bin/bash`.
- `--password $(openssl passwd -1)`: Generates an encrypted password for the user. This is an optional step, and you can set the password manually using the `passwd` command later if desired.
- `kirsty`: Specifies the username as "kirsty".

Make sure to replace `--password $(openssl passwd -1)` with `--password <encrypted_password>` if you have a specific password in mind. However, note that it is generally recommended to set the password using the `passwd` command after creating the user.

======================================================================================================================

# Instructions

There was some users data copied on `Nautilus App Server 1` at `/home/usersdata` location by the `Nautilus` production support team in `Stratos DC`.
 Later they found that they mistakenly mixed up different user data there. Now they want to filter out some user data and copy it to another location. Find the details below:

On `App Server 1` find all files (not directories) owned by user `javed` inside `/home/usersdata` directory and copy them all `while keeping the folder structure` (preserve the directories path) to `/blog` directory.

# Solution

ssh into the App Server 1: `ssh tony@172.16.238.10`

Check files: `ls -la | grep javed`

Run this command: `find /home/usersdata -type f -user javed -exec cp --parents {} /blog \;`

Let's break down the command:

- `find`: The command used to search for files and directories.
- `/home/usersdata`: The directory where the search will be performed.
- `type f`: Specifies that only files should be considered, excluding directories.
- `user javed`: Filters the search to files owned by the user "javed".
- `exec cp --parents {} /blog \;`: Executes the `cp` command to copy the found files to the "/blog" directory while preserving the directory structure. The `{}` represents the found files, and `-parents` ensures that the directories' path structure is maintained during the copy.

======================================================================================================================
# Instructions

After doing some security audits of servers, `xFusionCorp Industries` security team has implemented some new security policies. One of them is to disable direct root login through SSH.

Disable direct SSH root login on all app servers in `Stratos Datacenter`.

# Solution

ssh into the App Server 1: `ssh tony@172.16.238.10`

ssh into the App Server 2: `ssh steve@172.16.238.11`

ssh into the App Server 3: `ssh banner@172.16.238.12`

Open the SSH server configuration file (usually located at `/etc/ssh/sshd_config`) using a text editor such as `vi:` `sudo vi /etc/ssh/sshd_config`

Modify the value of `PermitRootLogin` to `no` or `without-password`. This disables direct root login.

- Setting it to `no` will completely disable root login.
- Setting it to `without-password` will allow root login only with public key authentication.


Restart the SSH server daemon (`sshd`)  `sudo systemctl restart sshd`

When you run `sudo systemctl restart sshd`, it will stop the SSH server daemon if it is currently running and then start it again. This command is often used after making changes to the SSH server configuration file (`sshd_config`) to apply the new configuration without requiring a system reboot. Restarting the SSH server ensures that the changes take effect and that the updated configuration is loaded.

======================================================================================================================
# Instructions

On `Nautilus` storage server in `Stratos DC`, there is a storage location named `/data`, which is used by different developers to keep their data (non confidential data). One of the developers named `yousuf` has raised a ticket and asked for a copy of their data present in `/data/yousuf` directory on storage server. `/home` is a FTP location on storage server itself where developers can download their data. Below are the instructions shared by the system admin team to accomplish this task.

a. Make a `yousuf.tar.gz` compressed archive of `/data/yousuf` directory and move the archive to `/home` directory on Storage Server.

# Solution

ssh into the Nautilus Storage Server: `ssh natasha@172.16.238.15`

Check the files: `cd /data/yousuf`

To create a compressed archive named "yousuf.tar.gz" of the "/data/yousuf" directory and move it to the "/home" directory on the Storage Server, you can use the following commands: 

`tar -czvf yousuf.tar.gz /data/yousuf`

tar -czvf yousuf.tar.gz /data/yousuf: This command creates a compressed tar archive named "yousuf.tar.gz" of the "/data/yousuf" directory. Here's what each option does:

    -c: Create a new archive.
    -z: Compress the archive using gzip.
    -v: Verbose mode, display the progress and details of the archiving process.
    -f: Specify the name of the archive file.

`sudo mv yousuf.tar.gz /home`

Check files in /home: `cd /home`

======================================================================================================================

# Instructions

There are new requirements to automate a backup process that was performed manually by the `xFusionCorp Industries` system admins team earlier. To automate this task, the team has developed a new bash script `xfusioncorp.sh` They have already copied the script on all required servers, however 
they did not make it executable on one the app server i.e `App Server 1` in `Stratos Datacenter`.

Please give executable permissions to `/tmp/xfusioncorp.sh` script on `App Server 1`.  Also make sure every user can execute it.

# Solution

ssh into the App Server 1: `ssh tony@172.16.238.10`

Check files: `cd /tmp`

Change permissions: `sudo chmod +rx xfusioncorp.sh`

- `chmod`: The command used to change file permissions.
- `+rx`: The `+` sign grants the specified permissions, and `r` and `x` represent read and execute permissions, respectively. By using `+rx`, you are granting both read and execute permissions to the file.
- `file_name`: The name of the file you want to modify the permissions for.

Note: To make a file executable, it must also be readable.

Check the result: `ls -l xfusioncorp.sh`

In this case, the owner has read and execute permissions, while the group and other users have read and execute permissions as well. This means that all users (owner, group, and others) can read and execute the file.


======================================================================================================================
# Instructions

The `Nautilus` security team performed an audit on all servers present in `Stratos DC`. During the audit some critical data/files were identified which were 
having the wrong permissions as per security standards. Once the report was shared with the production support team, they started fixing the issues one by one. It has been identified that one of the files named `/etc/hosts` on `Nautilus App 1` server has wrong permissions, so that needs to be fixed and the correct ACLs needs to be set.

1. The user owner and group owner of the  file should be `root` user.

2. `Others` must have `read only` permissions on the file.

3. User `yousuf` must not have any permission on the file.

4. User `eric` should have `read only` permission on the file.

# Solution

ssh into the App Server 1: `ssh tony@172.16.238.10`

Check ACL of the file: `getfacl /etc/hosts`

![image](https://github.com/janaom/KodeKloud-Engineer-2.0/assets/83917694/b61e3e4a-d8cc-46de-bdba-11582742f8b9)

- `# owner: root`: This line indicates that the owner of the file is the root user.
- `# group: root`: This line indicates that the group owner of the file is the root group.
- `user::rw-`: This line represents the permissions for the file owner (root). Here's what each character signifies:
    - `user`: Refers to the file owner.
    - `::`: Separates the user/group specification from the permissions.
    - `rw-`: Indicates that the owner has read (`r`) and write (`w`) permissions, but no execute (``) permission.
- `group::r--`: This line represents the permissions for the group owner (root). Here's what each character signifies:
    - `group`: Refers to the group owner.
    - `::`: Separates the user/group specification from the permissions.
    - `r--`: Indicates that the group has read (`r`) permission, but no write (``) or execute (``) permission.
- `other::r--`: This line represents the permissions for other users (users not in the owner group). Here's what each character signifies:
    - `other`: Refers to other users.
    - `::`: Separates the user/group specification from the permissions.
    - `r--`: Indicates that other users have read (`r`) permission, but no write (``) or execute (``) permission.

To ensure that others have read-only permissions on a file, you can use the `chmod` command with the appropriate permissions: `sudo chmod o=r /etc/hosts`

To modify the access control list (ACL) and set permissions for the user "yousuf" to have no access on a file, you can use the following command: `sudo setfacl -m u:yousuf:--- /etc/hosts`

In this command, `-m` specifies the modification of the access control list (ACL), `u:eric` indicates the user "eric" whose permissions should be modified, and `r` grants read-only permission: `sudo setfacl -m u:eric:r /etc/hosts`

Check the result 

======================================================================================================================
# Instructions

The backup server in the `Stratos DC` contains several template XML files used by the Nautilus application. However, these template XML files must be populated with valid data before they can be used. One of the daily tasks of a system admin working in the xFusionCorp industries is to apply string and file manipulation commands!

Replace all occurances of the string `About` to `Marine` on the XML file `/root/nautilus.xml` located in the backup server.

# Solution

ssh into backup server: `ssh clint@172.16.238.16`

To check if the file exists, run this: `sudo bash -c 'if [ -f /root/nautilus.xml ]; then echo "File exists."; else echo "File does not exist."; fi'`

Or become the root: `sudo su -`

Check all the lines in the "/root/nautilus.xml" file that include the word "About”: `cat /root/nautilus.xml  |grep About`

Use the following command to replace all occurrences of the string "About" with "Marine" in the XML file "/root/nautilus.xml": `sed -i 's/About/Marine/g' nautilus.xml`

- `sed`: A command-line tool for stream editing.
- `i`: Modifies the file in-place (i.e., edits the file directly).
- `'s/About/Marine/g'`: The `s` command in `sed` is used for substitution. It replaces all occurrences of "About" with "Marine" in each line of the file. The `g` flag ensures that all occurrences are replaced, not just the first occurrence.

Check the result


======================================================================================================================

# Instructions

One of the `Nautilus` developers has copied confidential data on the jump host in `Stratos DC`. That data must be copied to one of the app servers. Because developers do not have access to app servers, they asked the system admins team to accomplish the task for them.

Copy `/tmp/nautilus.txt.gpg` file from jump server to `App Server 3`  at location `/home/data`.

# Solution

The `scp` command is used to securely copy files between hosts on a network:`scp /tmp/nautilus.txt.gpg banner@172.16.238.12:/home/data`

ssh into the App Server 3 and check the result: `ssh banner@172.16.238.12`


======================================================================================================================
# Instructions

To stick with the security compliances, the Nautilus project team has decided to apply some restrictions on crontab access so that only allowed users can create/update the cron jobs. Limit crontab access to below specified users on `App Server 2`.

Allow crontab access to `rose` user and deny the same to `jerome` user.

# Solution

ssh into the App Server 2: `ssh steve@172.16.238.11`

`sudo su`

By executing this command with root privileges, the "rose" user will be allowed to use crontab: `echo rose >> /etc/cron.allow`

By executing this command, the "jerome" user will be denied crontab access: `echo jerome >> /etc/cron.deny`

check results with `cat`:

`sudo systemctl restart crond.service`

`sudo systemctl status crond.service`

This command will display the crontab entries for the "rose" user. If the user has any cron jobs set up, you will see them listed in the output: `sudo crontab -u rose -l` (in our case no cron jobs)


======================================================================================================================
# Instructions

New tools have been installed on the app servers in `Stratos Datacenter`.Some of these tools can only be managed from the graphical user interface. Therefore, there are some requirements for these app servers as below.

On all App servers in `Stratos Datacenter`, change the `default runlevel` so that they can boot in `GUI (graphical user interface)` by default. Please do not try to `reboot` these servers after completing this task.

# Solution

ssh into the App Server 1: `ssh tony@172.16.238.10`

ssh into the App Server 2: `ssh steve@172.16.238.11`

ssh into the App Server 3: `ssh banner@172.16.238.12`

Switch to the root user: `sudo su -`

The command will display the current default target, which can be `graphical.target` for GUI mode or `multi-user.target` for non-GUI mode: `systemctl get-default`

By changing the default target to `graphical.target`, the system will boot into the graphical user interface (GUI) by default when it starts up: `systemctl set-default graphical.target`

It will display the updated default target, which should now be `graphical.target` if the previous command was executed successfully: `systemctl get-default`


I got an error with App Server 3 :

`Failed to set default target: The name org.freedesktop.PolicyKit1 was not provided by any .service files`

By starting the `polkit` service, you activate it and allow it to handle authorization requests: `sudo systemctl start polkit`

If that didn’t work install the `polkit` package using the `dnf`:`sudo dnf install polkit`

This command is useful when you want to find packages related to a specific functionality or library: `sudo dnf search polkit`

`sudo systemctl set-default graphical.target`

`systemctl get-default`

======================================================================================================================
# Instructions

During the daily standup, it was pointed out that the timezone across `Nautilus Application Servers` in `Stratos Datacenter` doesn't match with that of the local datacenter's timezone, which is `America/Bogota`.

Correct the mismatch.

# Solution

ssh into the App Server 1: `ssh tony@172.16.238.10`

ssh into the App Server 2: `ssh steve@172.16.238.11`

ssh into the App Server 3: `ssh banner@172.16.238.12`

Check the current timezone configuration: `timedatectl show`

Update the timezone setting: `sudo timedatectl set-timezone America/Bogota`

Verify the timezone synchronization: `timedatectl show | grep Timezone`

======================================================================================================================

# Instructions

The system admin team of `xFusionCorp Industries` has noticed an issue with some servers in `Stratos Datacenter`where some of the servers are not in sync w.r.t time. Because of this, several application functionalities have been impacted. To fix this issue the team has started using common/standard NTP servers. They are finished with most of the servers except `App Server 2`. Therefore, perform the following tasks on this server:

1. Install and configure NTP server on `App Server 2`.

2. Add NTP `server 1.sg.pool.ntp.org` in NTP configuration on `App Server 2`.

3. Please do not try to start/restart/stop `ntp` service, as we already have a restart for this service scheduled for tonight and we don't want these changes to be applied right now.

# Solution

ssh into the App Server 2: `ssh steve@172.16.238.11`

Install the NTP server software. The specific package and installation method may vary depending on the operating system you are using. This one worked for me:`sudo yum install ntp`

Check the status: `sudo systemctl list-unit-files | grep ntp`

Enable the service:

`sudo systemctl enable ntpd.service`

`sudo systemctl enable ntpdate.service`

Add NTP server 1.sg.pool.ntp.org in NTP configuration on App Server 2:`sudo vi /etc/ntp.conf`

Add the following line to the server configuration section: `server 1.sg.pool.ntp.org`

`systemctl status ntpd`

`sudo systemctl start ntpd`

======================================================================================================================
# Instructions

The `Nautilus` system admins team recently deployed a web UI application for their backup utility running on the `Nautilus backup server` in `Stratos Datacenter`. The application is running on port `8089`. They have `firewalld` installed on that server. The requirements that have come up include the following:

Open all incoming connection  on `8089/tcp` port.  Zone should be `public`.

# Solution

ssh into the Backup server: `ssh clint@172.16.238.16`

Run the following command to open port 8089/tcp and set the zone to public: `sudo firewall-cmd --zone=public --add-port=8089/tcp --permanent`This command adds a rule to the public zone of firewalld, allowing incoming connections on port 8089/tcp. The `--permanent` flag ensures that the rule persists across system reboots.

Reload the firewalld configuration for the changes to take effect: `sudo firewall-cmd --reload`

To check if the firewall rule allowing incoming connections on port 8089/tcp has been successfully applied, you can perform the following steps:

- `curl http://172.16.238.16:8089` If you receive a response or see the web UI, it indicates that the firewall rule is allowing incoming connections on port 8089/tcp.
- or you can verify the status of the firewall rule on the Nautilus backup server by running the following command: `sudo firewall-cmd --zone=public --list-ports`

======================================================================================================================
# Instructions

On our `Storage` server in `Stratos Datacenter` we are having some issues where `nfsuser`user is holding hundred of processes, which is degrading the 
performance of the server. Therefore, we have a requirement to limit its maximum processes. Please set its maximum process limits as below:

a. soft limit = `1027`

b. hard_limit = `2025`

# Solution

ssh into the Storage server: `ssh natasha@172.16.238.15`

Edit the system limits configuration file: `sudo vi /etc/security/limits.conf`

This sets the soft limit to 1027 and the hard limit to 2025 for the maximum number of processes:

`nfsuser soft nproc 1027`
`nfsuser hard nproc 2025`
Check the result:

To determine the hard limit for the maximum number of userprocesses, you can run the following command: `sudo su -c "su -c 'ulimit -Hu' -s /bin/sh nfsuser"`

======================================================================================================================
# Instructions

The xFusionCorp Industries security team recently did a security audit of their infrastructure and came up with ideas to improve the application and server security. They decided to use SElinux for an additional security layer. They are still planning how they will implement it; however, they have decided to start testing with app servers, so based on the recommendations they have the following requirements:

Install the required packages of SElinux on `App server 2` in `Stratos Datacenter`and disable it permanently for now; it will be enabled after making some required configuration changes on this host. Don't worry about rebooting the server as there is already a reboot scheduled for tonight's maintenance window. Also ignore the status of SElinux command line right now; the final status after reboot should be `disabled`.

# Solution

ssh into the App Server 2: `ssh steve@172.16.238.11`

Run the following command to update the package manager and ensure you have the latest package information: `sudo yum update`

Install the required SELinux packages by running the following command: `sudo yum install selinux-policy selinux-policy-targeted`

To disable SELinux permanently, you need to modify the SELinux configuration file: `sudo vi /etc/selinux/config`

![image](https://github.com/janaom/KodeKloud-Engineer-2.0/assets/83917694/c8edad1d-bda4-4194-b8a8-a561ad25a496)

Change `SELINUX=enforcing` to `SELINUX=disabled`

Check the status: `sestatus`

You should see: `SELinux status: disabled`

======================================================================================================================

