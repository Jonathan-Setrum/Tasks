# Instructions

The Nautilus development team shared requirements with the DevOps team regarding new application development.—specifically, they want to set up
 a Git repository for that project. Create a Git repository on `Storage server` in Stratos DC as per details given below:

1. Install `git` package using `yum` on `Storage server`.
2. After that create a bare repository `/opt/cluster.git` (make sure to use exact name).

# Solution

ssh into the Nautilus Storage Server: `ssh natasha@172.16.238.15`

Run the following command as the root user or with sudo privileges to update the package manager's cache: `sudo yum update`

Once the package manager's cache is updated, you can install git by running the following command: `sudo yum install git`

After the installation is complete, you can verify that git is installed by checking its version: `git --version`


Change the current directory to the `/opt` directory: `cd /opt`

Create the bare repository using the `git init` command with the `--bare` flag: `git init --bare cluster.git`

If you get ‘fatal: cannot mkdir cluster.git: Permission denied’ error, try: `sudo git init --bare /opt/cluster.git`

Verify that the bare repository was created successfully by listing the contents of the `/opt` directory: `ls -l`

============================================================================================================================

# Instructions

DevOps team created a new Git repository last week; however, as of now 
no team is using it. The Nautilus application development team recently 
asked for a copy of that repo on `Storage server` in Stratos DC. Please clone the repo as per details shared below:

1. The repo that needs to be cloned is `/opt/beta.git`
2. Clone this git repository under `/usr/src/kodekloudrepos` directory. Please do not try to make any changes in the repo.

# Solution

ssh into the Nautilus Storage Server: `ssh natasha@172.16.238.15`

`cd /usr/src/kodekloudrepos`

`git clone /opt/beta.git` if you get ‘fatal: could not create work tree dir 'beta': Permission denied’ use sudo: `sudo git clone /opt/beta.git /usr/src/kodekloudrepos/beta`

Check the result: `ls -l`

============================================================================================================================

# Instructions

There is a Git server used by the Nautilus project teams. Recently a new developer `Jon`joined the team and needs to start working on a project. To do so, he needs to fork an existing Git repository. Below you can find more details:

- Click on the `Gitea UI` button on the top bar. You should be able to access the `Gitea` page.
- Login to `Gitea` server using username `jon` and password `Jon_pass123`.
- There you will see a Git repository `sarah/story-blog`, `fork` it under `jon` user.

`Note:` For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

# Solution

============================================================================================================================

# Instructions

The Nautilus development team started with new project development. They have created different Git repositories to manage respective project's 
source code. One of the repositories `/opt/demo.git` was created recently. The team has given us a sample `index.html` file that is currently present on `jump host` under `/tmp` directory. The repository has been cloned at `/usr/src/kodekloudrepos` on `storage server` in `Stratos DC`.

Copy sample `index.html` file from `jump host` to `storage server` under cloned repository at `/usr/src/kodekloudrepos/demo`, further `add/commit` the file and push to the `master` branch.

# Solution

Check the file location

Copy file: `sudo scp /tmp/index.html natasha@ststor01:/tmp`

ssh into the Storage server: `ssh natasha@ststor01`

Copy file to the destination: `cp index.html /usr/src/kodekloudrepos/demo`

Go to `cd /usr/src/kodekloudrepos/demo`

`git status`

`git add index.html`

`git commit -m "add index.html"`

`git push origin master`

============================================================================================================================

# Instructions

Nautilus developers are actively working on one of the project repositories, `/usr/src/kodekloudrepos/apps`. They were doing some testing and created few test branches, now they want to clean those test branches. Below are the requirements that have been shared with the DevOps team:

On `Storage server` in Stratos DC delete a branch named `xfusioncorp_apps` from `/usr/src/kodekloudrepos/apps` git repo.

# Solution

ssh into the Storage server: `ssh natasha@ststor01`

`cd /usr/src/kodekloudrepos/apps`

`git config --global --add safe.directory /usr/src/kodekloudrepos/apps`

Check if this branch is there `git branch --list xfusioncorp_apps`

Check the branches `git branch`

Change to the master `sudo git checkout master`

Change the permissions:

`sudo chown -R natasha /usr/src/kodekloudrepos/apps`

`sudo chmod -R u+rwx /usr/src/kodekloudrepos/apps`

Delete the branch `git branch -D xfusioncorp_apps`


============================================================================================================================


