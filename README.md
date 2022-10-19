# Task1


## Question 1: LVM
>Create a volume group, and set 16M as extends. And divided a volume group containing 50 extends on
volume group lv, make it as ext4 file system, and mounted automatically under /mnt/data. Please
note that this should be implemented on the second disk
#### scan everything in scsi:
```
echo "- - -" > /sys/class/scsi_host/host0/scan
echo "- - -" > /sys/class/scsi_host/host1/scan
echo "- - -" > /sys/class/scsi_host/host2/scan
```
#### create a partition:
```
fdisk /dev/sdb
```
choose 'n' for create ,then 'p' for primary partition ,choose '1' for the partition number
press 't' to select the first partition , '8e' the code for LV , then 'p' , the 'w' to save
![create partition](/create-partition.png)
#### create a physical volume:
```
pvcreate /dev/sdb1
```
#### create a vloume group :
```
vgcreate -s 16M vg03 /dev/sdb1
```
#### create Logical volume :
```
lvcreate -L 800M -n lv vg03
```
![vg and lv](https://github.com/haithamhamad/Task1/blob/ac5a12d2939502da4bd55524d32b5fe33ed51aea/create%20vg%20and%20LV.png)


#### make a file system and mount it:
```
mkfs -t ext4 /dev/vg03/lv
mkdir /mnt/data
mount /dev/vg03/lv /mnt/data
```
![mount](https://github.com/haithamhamad/Task1/blob/8211452cb273cf38066759d8c178af476a30a702/mount%20the%20fs.png)

#### to mount automaticaly
```
vi /ets/fstab
```
> UUID="589e3804-df77-4da3-8c55-1e1eb8f818e9" /mnt/data  ext4 defaults  0 0

### final result
![res](https://github.com/haithamhamad/Task1/blob/8211452cb273cf38066759d8c178af476a30a702/result%20lvm.png)

## Question 2: users, groups and permission
>1. Add user: user1, set uid=601 Password: redhat. The user's login shell should be non-interactive. (no ssh access to server)
>2. Add user1 to group TrainingGroup.
>3. Add users: user2, user3. The Additional group of the two users: user2, user3 is the admin group Password: redhat, user 3 with root permissions

#### add user1:
```
useradd -u 601 -s /etc/bashrc -p redhat user1
```
#### deny SSH access for user1:
```
vi /etc/ssh/ssh_config
```
>DenyUsers user1
#### add groups :
```
groupadd TrainingGroup
groupadd Admin
```
#### add user2 and 3:
```
useradd -p redhat -G Admin,TrainingGroup user2
useradd -p redhat -G Admin,TrainingGroup user3
```
![add res](https://github.com/haithamhamad/Task1/blob/f56a62b65687d5669c9dec03970a683aa30e7b1d/add%20result.png)
#### Give user3 root permissions:
```
visudo
```
>user3 ALL=(ALL) ALL

![u3 permission](https://github.com/haithamhamad/Task1/blob/86c76d321cd39fe5652bc8cf94be719118544d8c/user3%20permission.png)

## Question 3: SSH
>Generate SSH key and connect to different VM without password.
#### genrate public and private key:
```
 ssh-keygen -t rsa
```
>saved to /home/osboxes/.ssh/id_rsa

#### send the public key to the other machine:
```
ssh-copy-id -i id_rsa.pub (root@whatever is the other machine)
```
## Question 4: permissions
>Copy /etc/fstab to /var/tmp name admin, the user1 could read, write and modify it, while user2 can’t do
any permission
#### copy the file:
```
cp /etc/fstab /var/tmp/admin
```
#### change owner to user1:
```
chown user1 /var/tmp/admin
```
#### change permissions:
```
chmod 700 /var/tmp/admin
```
## Question 5: SELinux
>SELinux must be running in the Enforcing mode (permanent even after reboot)
```
vi /etc/selinux/config
```
>SELINUX=enforcing

## Question 6:  bash script and processes
>Write a shell script that will keep running for 10 mins in the background and check the process that it's
created and try to kill using commands.
```
#!/bin/bash

PID=$!

sleep 600 && kill -9 $PID 2>/dev/null

```
## Question 7: Yum Repo
>Install tmux on your machine
```
yum install tmux
```
>Install apache server on your machine(httpd) and Install mysql
```
yum install httpd
systemctl start httpd
systemctl enable httpd
systemctl status httpd
sudo wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
sudo md5sum mysql80-community-release-el7-3.noarch.rpm
sudo yum install mysql-server
```
>Create a local yum repository on your local machine(available publicly) with the zabbix rpms
```
yum install createrepo yum-utils
yum-config-manager --add-repo https://repo.zabbix.com/zabbix/4.4/rhel/7/x86_64/
mkdir /var/www/html/
yum reposync -p=/var/www/html --repoid=repo.zabbix.com_zabbix_4.4_rhel_7_x86_64_ --download-metadata
createrepo /var/www/html/repo.zabbix.com_zabbix_4.4_rhel_7_x86_64_
```
```
nano /etc/yum.repos.d/local.repo
```
```
[local-extras]
name=Yum Local Extras
baseurl=http://192.168.122.1:80/repo.zabbix.com_zabbix_4.4_rhel_7_x86_64_
enabled=1
gpgcheck=0
```
>disable all repos and only enable the local one
```
yum-config-manager --disable \*
yum-config-manager --enable local-extras
```
>Install zabbix rpms from the new repo
```
yum insatll zabbix-server
yum insatll zabbix-agent
yum insatll zabbix-web
```
>to install PHP
```
yum install --downloadonly --downloaddir=/var/www/html/repo.zabbix.com_zabbix_4.4_rhel_7_x86_64_ epel-release –y
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm	
reposync -p=/var/www/html/repo.zabbix.com_zabbix_4.4_rhel_7_x86_64_ --repoid=remi-safe --download-metadata
createrepo /var/www/html/repo.zabbix.com_zabbix_4.4_rhel_7_x86_64
yum insatll php
```











## Question 8: Network management
>1- Open port 443 , 80
>2- Make the changes permanent
>3- Block ssh connection for your colleague ip to the VM.

#### open ports :
```
firewall-cmd --add-port={443/tcp,80/tcp} --permanent
```
#### deny an IP:
```
vi /etc/hosts.deny
```
>sshd : IP(ex : 192.168.2.5)

## Question 9: cronjob
>Create a cronjob that will run at 1:30 AM every day and collect the users logged in and save them in a file
Format : timestamp – users
```
crontab -e
```
```
30 1 * * * /usr/bin/logged.bash
```

```
#!/bin/bash


date | tr -d '\n' >> /home/userslogged
echo "  - -  " | tr -d '\n' >> /home/userslogged
users >> /home/userslogged

echo "-------------------" >> /home/userslogged
```
## Question 10: Mariadb
>install mariadb
```
 yum install wget
 wget https://downloads.mariadb.com/MariaDB/mariadb_repo_setup
 chmod +x mariadb_repo_setup
 sudo ./mariadb_repo_setup
 
 yum install MariaDB-server
```
>open ports in iptables from mariadb.
```
iptables -A INPUT -p tcp -m tcp --dport 3306 -j ACCEPT
```
>create database , user
```
mysql -u root
> CREATE USER 'user'@localhost IDENTIFIED BY '123456Hh@';
>GRANT ALL PRIVILEGES ON *.* TO 'user'@localhost IDENTIFIED BY '123456Hh@';
>FLUSH PRIVILEGES;
>CREATE DATABASE studentdb;
```
>connect to the database created in step 3 using the new user (with password)
```
mysql -u user -p studentdb
```















