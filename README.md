# 42cursus - Born2beroot

## Table of Contents
1. [Introduction](#introduction)
    - [What is a Virtual Machine?](#Virtual-Machine)
    - [How do Virtual Machines work?](#Virtual-Machines-work)
    - [What is LVM?](#What-is-LVM?)
    - [What is AppArmor?](#What-is-AppArmor?)
    - [What is the difference between Apt and Aptitute?](#Apt-and-Aptitute)
    - [How to use SSH?](#How-to-use-SSH?)
    - [How to implement UFW with SSH?](#UFW-with-SSH)
    - [What is cron and what is wall?](#what-is-cron)
2. [Installation](#installation)
3. [*sudo*](#sudo)
    - [Step 1: Installing *sudo*](#step-1-installing-sudo)
    - [Step 2: Adding User to *sudo* Group](#step-2-adding-user-to-sudo-group)
    - [Step 3: Running *root*-Privileged Commands](#step-3-running-root-privileged-commands)
    - [Step 4: Configuring *sudo*](#step-4-configuring-sudo)
4. [SSH](#ssh)
    - [Step 1: Installing & Configuring SSH](#step-1-installing--configuring-ssh)
    - [Step 2: Installing & Configuring UFW](#step-2-installing--configuring-ufw)
    - [Step 3: Connecting to Server via SSH](#step-3-connecting-to-server-via-ssh)
5. [User Management](#user-management)
    - [Step 1: Setting Up a Strong Password Policy](#step-1-setting-up-a-strong-password-policy)
       - [Password Age](#password-age)
       - [Password Strength](#password-strength)
    - [Step 2: Creating a New User](#step-2-creating-a-new-user)
    - [Step 3: Creating a New Group](#step-3-creating-a-new-group)
6. [*cron*](#cron)
    - [Setting Up a *cron* Job](#setting-up-a-cron-job)
7. [Monitoring](#monitoring)
8. [Bonus](#bonus)
    - [Installation](#1-installation)
    - [Linux Lighttpd MariaDB PHP *(LLMP)* Stack](#2-linux-lighttpd-mariadb-php-llmp-stack)
       - [Step 1: Installing Lighttpd](#step-1-installing-lighttpd)
       - [Step 2: Installing & Configuring MariaDB](#step-2-installing--configuring-mariadb)
       - [Step 3: Installing PHP](#step-3-installing-php)
       - [Step 4: Downloading & Configuring WordPress](#step-4-downloading--configuring-wordpress)
       - [Step 5: Configuring Lighttpd](#step-5-configuring-lighttpd)
    - [File Transfer Protocol *(FTP)*](#3-file-transfer-protocol-ftp)
       - [Step 1: Installing & Configuring FTP](#step-1-installing--configuring-ftp)
       - [Step 2: Connecting to Server via FTP](#step-2-connecting-to-server-via-ftp)

8. [Submission and peer-evaluation for 1337/42 Students](#peereval)

9. [evalknowledge.txt](#evalknowledge)

## Introduction
You will create your first machine in VirtualBox (or UTM if you can’t use VirtualBox) under specific instructions. Then, at the end of this project, you will be able to set up your own operating system while implementing strict rules.

### <a name="Virtual-Machine">What is a Virtual Machine?</a>
A virtual machine is a **software capable of installing an Operating System within itself, making the OS think that it is hosted on a real computer**. With virtual machines we can create virtual devices that will behave in the same way as physical devices, using their own CPU, memory, network interface and storage. This is possible because **the virtual machine is hosted on a physical device**, which is the one that provides the hardware resources to the VM. The software program that creates virtual machines is **the hypervisor**. The hypervisor is responsible for isolating the VM resources from the system hardware and making the necessary implementations so that the VM can use these resources.<br>
The devices that provide the hardware resources are called **host machines or hosts**. The different virtual machines that can be assigned to a host are called **guests or guest machines**. The hypervisor uses a part of the host machine's CPU, storage, etc., and distributes them among the different VMs.<br>
<br>
There can be multiple virtual machines on the same host and each of these will be isolated from the rest of the system. Thanks to this, we can run different operating systems on our machine. For each virtual machine, we can run a different operating system distribution. Each of these operating systems will behave as if they were hosted on a physical device, so we will have the same experience when using an OS on a physical machine and on a virtual machine.

### <a name="Virtual-Machines-work">How do Virtual Machines work?</a>
Virtualization allow us share a system with multiple virtual environments. The hypervisor manages the hardware system and separate the physical resources from the virtual environments. **The resources are managed followitn the needs, from the host to the guests.** When an user from a VM do a task that requires additional resources from the physical environment, the hypervisor manages the request so that the guest OS could access the resources of the physical environment.<br>
Once we know how they work, it is a good idea to see all the advantages we get from using virtual machines:
<ul>
 <li>Different guest machines hosted on our computer <b>can run different operating systems</b>, so we will have different OS working on the same machine.</li>
   <li>They provide an environment in which <b>to safely test unstable programs</b> to see if they will affect the system or not.</li>
   <li>We get <b>better use of shared resources.</b></li>
   <li>We <b>reduce costs</b> by reducing physical architecture.</li>
   <li>They are <b>easy to implement</b> because they provide mechanisms to clone a virtual machine to another physical device.</li>
</ul>

### <a name="What-is-LVM?">What is LVM?</a>
**LVM (Logical Volume Manager)** is an **abstraction layer between a storage device and a file system**. We get many advantages from using LVM, but the main advantage is that we have much more flexibility when it comes to managing partitions. Suppose we create four partitions on our storage disk. If for any reason we need to expand the storage of the first three partitions, we will not be able to because there is no space available next to them. In case we want to extend the last partition, we will always have the limit imposed by the disk. In other words, we will not be able to manipulate partitions in a friendly way. Thanks to LVM, all these problems are solved.<br>
By using LVM, **we can expand the storage of any partition** (now known as a logical volume) whenever we want without worrying about the contiguous space available on each logical volume. We can do this with available storage located on different physical disks (which we cannot do with traditional partitions). We can also move different logical volumes between physical devices. Of course, **services and processes will work the same way they always have**. But to understand all this, we have to know:
<ul>
 <li><b>Physical Volume (PV):</b> physical storage device. It can be a hard disk, an SD card, a floppy disk, etc. This device provides us with storage available to use.</li>
   <li><b>Volume Group (VG):</b> to use the space provided by a PV, it must be allocated in a volume group. It is like a virtual storage disk that will be used by logical volumes. VGs can grow over time by adding new VPs.</li>
   <li><b>Logical volume (LV):</b> these devices will be the ones we will use to create file systems, swaps, virtual machines, etc. If the VG is the storage disk, the LV are the partitions that are made on this disk.</li>
</ul>

### <a name="What-is-AppArmor?">What is AppArmor?</a>
AppArmor provides **Mandatory Access Control (MAC) security**. In fact, **AppAmor allows the system administrator to restrict the actions that processes can perform**. For example, if an installed application can take photos by accessing the camera application, but the administrator denies this privilege, the application will not be able to access the camera application. If a vulnerability occurs (some of the restricted tasks are performed), AppArmor blocks the application so that the damage does not spread to the rest of the system.<br>
In AppArmor, **processes are restricted by profiles**. Profiles can work in complain-mode and in enforce-mode. In enforce mode, AppArmor prohibits applications from performing restricted tasks. In complain-mode, AppArmor allows applications to do these tasks, but creates a registry entry to display the complaint.

### <a name="Apt-and-Aptitute">What is the difference between Apt and Aptitute?</a>
In Debian-based OS distributions, **the default package manager we can use is dpkg**. This tool allows us to install, remove and manage programs on our operating system. However, in most cases, these programs come with a list of dependencies that must be installed for the main program to function properly. One option is to manually install these dependencies. However, **APT (Advanced Package Tool)**, which is a tool that uses dpkg, **can be used to install all the necessary dependencies when installing a program**. So now we can install a useful program with a single command.<br>
APT can work with different back-ends and fron-ends to make use of its services. One of them is **apt-get**, which **allows us to install and remove packages**. Along with apt-get, there are also many tools like apt-cache to manage programs. In this case, **apt-get and apt-cache are used by apt**. Thanks to apt we can install .deb programs easily and without worrying about dependencies. But in case we want to use a graphical interface, we will have to use aptitude. **Aptitude also does better control of dependencies**, allowing the user to choose between different dependencies when installing a program.

### <a name="How-to-use-SSH?">How to use SSH?</a>
SSH or **Secure Shell** is a **remote administration protocol that allows users to control and modify their servers** over the Internet thanks to an authentication mechanism. Provides a mechanism to authenticate a user remotely, transfer data from the client to the host, and return a response to the request made by the client.<br>
SSH was created as an alternative to Telnet, which does not encrypt the information that is sent. **SSH uses encryption techniques** to ensure that all client-to-host and host-to-client communications are done in encrypted form. One of the advantages of SSH is that a user using Linux or MacOS can use SSH on their server to communicate with it remotely through their computer's terminal. Once authenticated, that user will be able to use the terminal to work on the server.<br><br>
The command used to connect to a server with ssh is:

    ssh {username}@{IP_host} -p {port}
    
There are three different techniques that SSH uses to encrypt:
<ul>
 <li><b>Symmetric encryption:</b> a method that uses the same secret key for both encryption and decryption of a message, for both the client and the host. Anyone who knows the password can access the message that has been transmitted.</li>
 <li><b>Asymmetric encryption:</b> uses two separate keys for encryption and decryption. These are known as the public key and the private key. Together, they form the public-private key pair.</li>
 <li><b>Hashing:</b> another form of cryptography used by SSH. Hash functions are made in a way that they don't need to be decrypted. If a client has the correct input, they can create a cryptographic hash and SSH will check if both hashes are the same.</li>
</ul>

### <a name="UFW-with-SSH">How to implement UFW with SSH</a>
**UFW (Uncomplicated Firewall)** is a software application responsible for ensuring that the system administrator can **manage iptables in a simple way**. Since it is very difficult to work with iptables, UFW provides us with an interface to modify the firewall of our device **(netfilter)** without compromising security. Once we have UFW installed, we can choose which ports we want to allow connections, and which ports we want to close. This will also be very useful with SSH, greatly improving all security related to communications between devices. 

### <a name="what-is-cron">What is cron and what is wall?</a>
Once we know a little more about how to build a server inside a Virtual Machine (remember that you also have to look in other pages apart from this README), we will see two commands that will be very helpful in case of being system administrators. These commands are:
- **Cron:** Linux task manager that allows us to execute commands at a certain time. We can automate some tasks just by telling cron what command we want to run at a specific time. For example, if we want to restart our server every day at 4:00 am, instead of having to wake up at that time, cron will do it for us.
- **Wall:** command used by the root user to send a message to all users currently connected to the server. If the system administrator wants to alert about a major server change that could cause users to log out, the root user could alert them with wall. 



## Installation
At the time of writing, the latest stable version of [Debian](https://www.debian.org) is *Debian 10 Buster*. Watch *bonus* installation walkthrough *(no audio)* [here](https://youtu.be/2w-2MX5QrQw).

## *sudo*

### Step 1: Installing *sudo*
Switch to *root* and its environment via `su -`.
```
$ su -
Password:
#
```
Install *sudo* via `apt install sudo`.
```
# apt install sudo
```
Verify whether *sudo* was successfully installed via `dpkg -l | grep sudo`.
```
# dpkg -l | grep sudo
```

### Step 2: Adding User to *sudo* Group
Add user to *sudo* group via `adduser <username> sudo`.
```
# adduser <username> sudo
```
>Alternatively, add user to *sudo* group via `usermod -aG sudo <username>`.
>```
># usermod -aG sudo <username>
>```
Verify whether user was successfully added to *sudo* group via `getent group sudo`.
```
$ getent group sudo
```
`reboot` for changes to take effect, then log in and verify *sudopowers* via `sudo -v`.
```
# reboot
<--->
Debian GNU/Linux 10 <hostname> tty1

<hostname> login: <username>
Password: <password>
<--->
$ sudo -v
[sudo] password for <username>: <password>
```

### Step 3: Running *root*-Privileged Commands
From here on out, run *root*-privileged commands via prefix `sudo`. For instance:
```
$ sudo apt update
```

### Step 4: Configuring *sudo*
Configure *sudo* via `sudo vi /etc/sudoers.d/<filename>`. `<filename>` shall not end in `~` or contain `.`.
```
$ sudo vi /etc/sudoers.d/<filename>
```
To limit authentication using *sudo* to 3 attempts *(defaults to 3 anyway)* in the event of an incorrect password, add below line to the file.
```
Defaults        passwd_tries=3
```
To add a custom error message in the event of an incorrect password:
```
Defaults        badpass_message="<custom-error-message>"
```
###
To log all *sudo* commands to `/var/log/sudo/<filename>`:
```
$ sudo mkdir /var/log/sudo
<~~~>
Defaults        logfile="/var/log/sudo/<filename>"
<~~~>
```
To archive all *sudo* inputs & outputs to `/var/log/sudo/`:
```
Defaults        log_input,log_output
Defaults        iolog_dir="/var/log/sudo"
```
To require *TTY*:
```
Defaults        requiretty
```
To set *sudo* paths to `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`:
```
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

## SSH

### Step 1: Installing & Configuring SSH
Install *openssh-server* via `sudo apt install openssh-server`.
```
$ sudo apt install openssh-server
```
Verify whether *openssh-server* was successfully installed via `dpkg -l | grep ssh`.
```
$ dpkg -l | grep ssh
```
Configure SSH via `sudo vi /etc/ssh/sshd_config`.
```
$ sudo vi /etc/ssh/sshd_config
```
To set up SSH using Port 4242, replace below line:
```
13 #Port 22
```
with:
```
13 Port 4242
```
To disable SSH login as *root* irregardless of authentication mechanism, replace below line
```
32 #PermitRootLogin prohibit-password
```
with:
```
32 PermitRootLogin no
```
Check SSH status via `sudo service ssh status`.
```
$ sudo service ssh status
```
>Alternatively, check SSH status via `systemctl status ssh`.
>```
>$ systemctl status ssh
>```

### Step 2: Installing & Configuring UFW
Install *ufw* via `sudo apt install ufw`.
```
$ sudo apt install ufw
```
Verify whether *ufw* was successfully installed via `dpkg -l | grep ufw`.
```
$ dpkg -l | grep ufw
```
Enable Firewall via `sudo ufw enable`.
```
$ sudo ufw enable
```
Allow incoming connections using Port 4242 via `sudo ufw allow 4242`.
```
$ sudo ufw allow 4242
```
Check UFW status via `sudo ufw status`.
```
$ sudo ufw status
```

### Step 3: Connecting to Server via SSH
SSH into your virtual machine using Port 4242 via `ssh <username>@<ip-address> -p 4242`.
```
$ ssh <username>@<ip-address> -p 4242
```
Terminate SSH session at any time via `logout`.
```
$ logout
```
>Alternatively, terminate SSH session via `exit`.
>```
>$ exit
>```

## User Management

### Step 1: Setting Up a Strong Password Policy

#### Password Age
Configure password age policy via `sudo vi /etc/login.defs`.
```
$ sudo vi /etc/login.defs
```
To set password to expire every 30 days, replace below line
```
160 PASS_MAX_DAYS   99999
```
with:
```
160 PASS_MAX_DAYS   30
```
To set minimum number of days between password changes to 2 days, replace below line
```
161 PASS_MIN_DAYS   0
```
with:
```
161 PASS_MIN_DAYS   2
```
To send user a warning message 7 days *(defaults to 7 anyway)* before password expiry, keep below line as is.
```
162 PASS_WARN_AGE   7
```

#### Password Strength
Secondly, to set up policies in relation to password strength, install the *libpam-pwquality* package.
```
$ sudo apt install libpam-pwquality
```
Verify whether *libpam-pwquality* was successfully installed via `dpkg -l | grep libpam-pwquality`.
```
$ dpkg -l | grep libpam-pwquality
```
Configure password strength policy via `sudo vi /etc/pam.d/common-password`, specifically the below line:
```
$ sudo vi /etc/pam.d/common-password
<~~~>
25 password        requisite                       pam_pwquality.so retry=3
<~~~>
```
To set password minimum length to 10 characters, add below option to the above line.
```
minlen=10
```
To require password to contain at least an uppercase character and a numeric character:
```
ucredit=-1 dcredit=-1
```
To set a maximum of 3 consecutive identical characters:
```
maxrepeat=3
```
To reject the password if it contains `<username>` in some form:
```
reject_username
```
To set the number of changes required in the new password from the old password to 7:
```
difok=7
```
To implement the same policy on *root*:
```
enforce_for_root
```
Finally, it should look like the below:
```
password        requisite                       pam_pwquality.so retry=3 minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
```

### Step 2: Creating a New User
Create new user via `sudo adduser <username>`.
```
$ sudo adduser <username>
```
Verify whether user was successfully created via `getent passwd <username>`.
```
$ getent passwd <username>
```
Verify newly-created user's password expiry information via `sudo chage -l <username>`.
```
$ sudo chage -l <username>
Last password change					: <last-password-change-date>
Password expires					: <last-password-change-date + PASS_MAX_DAYS>
Password inactive					: never
Account expires						: never
Minimum number of days between password change		: <PASS_MIN_DAYS>
Maximum number of days between password change		: <PASS_MAX_DAYS>
Number of days of warning before password expires	: <PASS_WARN_AGE>
```

### Step 3: Creating a New Group
Create new *user42* group via `sudo addgroup user42`.
```
$ sudo addgroup user42
```
Add user to *user42* group via `sudo adduser <username> user42`.
```
$ sudo adduser <username> user42
```
>Alternatively, add user to *user42* group via `sudo usermod -aG user42 <username>`.
>```
>$ sudo usermod -aG user42 <username>
>```
Verify whether user was successfully added to *user42* group via `getent group user42`.
```
$ getent group user42
```

## *cron*

### Setting Up a *cron* Job
Configure *cron* as *root* via `sudo crontab -u root -e`.
```
$ sudo crontab -u root -e
```
To schedule a shell script to run every 10 minutes, replace below line
```
23 # m h  dom mon dow   command
```
with:
```
23 */10 * * * * sh /path/to/script
```
Check *root*'s scheduled *cron* jobs via `sudo crontab -u root -l`.
```
$ sudo crontab -u root -l
```
## Monitoring

You have to create a simple script called `monitoring.sh` It must be developed in bash.
At server startup, the script will display some information (listed below) on all ter- minals every 10 minutes (take a look at wall). The banner is optional. No error must be visible.
Your script must always be able to display the following information:<br/>
• The architecture of your operating system and its kernel version.<br/>
• The number of physical processors.<br/>
• The number of virtual processors.<br/>
• The current available RAM on your server and its utilization rate as a percentage.<br/> 
• The current available memory on your server and its utilization rate as a percentage.<br/>
• The current utilization rate of your processors as a percentage.<br/>
• The date and time of the last reboot.<br/>
• Whether LVM is active or not.<br/>
• The number of active connections.<br/>
• The number of users using the server.<br/>
• The IPv4 address of your server and its MAC (Media Access Control) address.<br/>
• The number of commands executed with the sudo program.

 You can find the script `monitoring.sh` in this repository.

## Bonus

### #1: Installation
Watch *bonus* installation walkthrough *(no audio)* [here](https://youtu.be/2w-2MX5QrQw).

### #2: Linux Lighttpd MariaDB PHP *(LLMP)* Stack

#### Step 1: Installing Lighttpd
Install *lighttpd* via `sudo apt install lighttpd`.
```
$ sudo apt install lighttpd
```
Verify whether *lighttpd* was successfully installed via `dpkg -l | grep lighttpd`.
```
$ dpkg -l | grep lighttpd
```
Allow incoming connections using Port 80 via `sudo ufw allow 80`.
```
$ sudo ufw allow 80
```

#### Step 2: Installing & Configuring MariaDB
Install *mariadb-server* via `sudo apt install mariadb-server`.
```
$ sudo apt install mariadb-server
```
Verify whether *mariadb-server* was successfully installed via `dpkg -l | grep mariadb-server`.
```
$ dpkg -l | grep mariadb-server
```
Start interactive script to remove insecure default settings via `sudo mysql_secure_installation`.
```
$ sudo mysql_secure_installation
Enter current password for root (enter for none): #Just press Enter (do not confuse database root with system root)
Set root password? [Y/n] n
Remove anonymous users? [Y/n] Y
Disallow root login remotely? [Y/n] Y
Remove test database and access to it? [Y/n] Y
Reload privilege tables now? [Y/n] Y
```
Log in to the MariaDB console via `sudo mariadb`.
```
$ sudo mariadb
MariaDB [(none)]>
```
Create new database via `CREATE DATABASE <database-name>;`.
```
MariaDB [(none)]> CREATE DATABASE <database-name>;
```
Create new database user and grant them full privileges on the newly-created database via `GRANT ALL ON <database-name>.* TO '<username-2>'@'localhost' IDENTIFIED BY '<password-2>' WITH GRANT OPTION;`.
```
MariaDB [(none)]> GRANT ALL ON <database-name>.* TO '<username-2>'@'localhost' IDENTIFIED BY '<password-2>' WITH GRANT OPTION;
```
Flush the privileges via `FLUSH PRIVILEGES;`.
```
MariaDB [(none)]> FLUSH PRIVILEGES;
```
Exit the MariaDB shell via `exit`.
```
MariaDB [(none)]> exit
```
Verify whether database user was successfully created by logging in to the MariaDB console via `mariadb -u <username-2> -p`.
```
$ mariadb -u <username-2> -p
Enter password: <password-2>
MariaDB [(none)]>
```
Confirm whether database user has access to the database via `SHOW DATABASES;`.
```
MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| <database-name>    |
| information_schema |
+--------------------+
```
Exit the MariaDB shell via `exit`.
```
MariaDB [(none)]> exit
```

#### Step 3: Installing PHP
Install *php-cgi* & *php-mysql* via `sudo apt install php-cgi php-mysql`.
```
$ sudo apt install php-cgi php-mysql
```
Verify whether *php-cgi* & *php-mysql* was successfully installed via `dpkg -l | grep php`.
```
$ dpkg -l | grep php
```

#### Step 4: Downloading & Configuring WordPress
Install *wget* via `sudo apt install wget`.
```
$ sudo apt install wget
```
Download WordPress to `/var/www/html` via `sudo wget http://wordpress.org/latest.tar.gz -P /var/www/html`.
```
$ sudo wget http://wordpress.org/latest.tar.gz -P /var/www/html
```
Extract downloaded content via `sudo tar -xzvf /var/www/html/latest.tar.gz`.
```
$ sudo tar -xzvf /var/www/html/latest.tar.gz
```
Remove tarball via `sudo rm /var/www/html/latest.tar.gz`.
```
$ sudo rm /var/www/html/latest.tar.gz
```
Copy content of `/var/www/html/wordpress` to `/var/www/html` via `sudo cp -r /var/www/html/wordpress/* /var/www/html`.
```
$ sudo cp -r /var/www/html/wordpress/* /var/www/html
```
Remove `/var/www/html/wordpress` via `sudo rm -rf /var/www/html/wordpress`
```
$ sudo rm -rf /var/www/html/wordpress
```
Create WordPress configuration file from its sample via `sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php`.
```
$ sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
```
Configure WordPress to reference previously-created MariaDB database & user via `sudo vi /var/www/html/wp-config.php`.
```
$ sudo vi /var/www/html/wp-config.php
```
Replace the below
```
23 define( 'DB_NAME', 'database_name_here' );^M
26 define( 'DB_USER', 'username_here' );^M
29 define( 'DB_PASSWORD', 'password_here' );^M
```
with:
```
23 define( 'DB_NAME', '<database-name>' );^M
26 define( 'DB_USER', '<username-2>' );^M
29 define( 'DB_PASSWORD', '<password-2>' );^M
```

#### Step 5: Configuring Lighttpd
Enable below modules via `sudo lighty-enable-mod fastcgi; sudo lighty-enable-mod fastcgi-php; sudo service lighttpd force-reload`.
```
$ sudo lighty-enable-mod fastcgi
$ sudo lighty-enable-mod fastcgi-php
$ sudo service lighttpd force-reload
```

### #3: File Transfer Protocol *(FTP)*

#### Step 1: Installing & Configuring FTP
Install FTP via `sudo apt install vsftpd`.
```
$ sudo apt install vsftpd
```
Verify whether *vsftpd* was successfully installed via `dpkg -l | grep vsftpd`.
```
$ dpkg -l | grep vsftpd
```
Allow incoming connections using Port 21 via `sudo ufw allow 21`.
```
$ sudo ufw allow 21
```
Configure *vsftpd* via `sudo vi /etc/vsftpd.conf`.
```
$ sudo vi /etc/vsftpd.conf
```
To enable any form of FTP write command, uncomment below line:
```
31 #write_enable=YES
```
To set root folder for FTP-connected user to `/home/<username>/ftp`, add below lines:
```
$ sudo mkdir /home/<username>/ftp
$ sudo mkdir /home/<username>/ftp/files
$ sudo chown nobody:nogroup /home/<username>/ftp
$ sudo chmod a-w /home/<username>/ftp
<~~~>
user_sub_token=$USER
local_root=/home/$USER/ftp
<~~~>
```
To prevent user from accessing files or using commands outside the directory tree, uncomment below line:
```
114 #chroot_local_user=YES
```
To whitelist FTP, add below lines:
```
$ sudo vi /etc/vsftpd.userlist
$ echo <username> | sudo tee -a /etc/vsftpd.userlist
<~~~>
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
<~~~>
```

#### Step 2: Connecting to Server via FTP
FTP into your virtual machine via `ftp <ip-address>`.
```
$ ftp <ip-address>
```
Terminate FTP session at any time via `CTRL + D`.

## <a name="peereval">Submission and peer-evaluation for 1337/42 Students</a>

You only have to turn in a signature.txt file at the root of your Git repository. You must paste in it the signature of your machine’s virtual disk. To get this signature, you first have to open the default installation folder (it is the folder where your VMs are saved):
 
• Windows: `%HOMEDRIVE%%HOMEPATH%\VirtualBox VMs\`
    
• Linux: `~/VirtualBox VMs/`
    
• MacM1: `~/Library/Containers/com.utmapp.UTM/Data/Documents/ `
    
• MacOS: ` ~/VirtualBox VMs/`

Then, retrieve the signature from the ".vdi" file (or ".qcow2 for UTM’users) of your virtual machine in sha1 format. Below are 4 command examples for a centos_serv.vdi file:
    
• Windows: `certUtil -hashfile centos_serv.vdi sha1 `
    
• Linux: `sha1sum centos_serv.vdi`
    
• For Mac M1: `shasum Centos.utm/Images/disk-0.qcow2 `
    
• MacOS: `shasum centos_serv.vdi`
    
This is an example of what kind of output you will get:
    
• `6e657c4619944be17df3c31faa030c25e43e40af`

## <a name="evalknowledge">Evalknowledge.txt</a>
    
Little Q&A from Subject and whattocheck as evaluator. You can find it in this repository.

I think it's done for now. I highly recommend repeating the installation process several times, if possible, in order to remember and understand everything well. Especially if this is your first time working both Linux and a virtual machine.
