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





