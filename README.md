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
>Add user: user1, set uid=601 Password: redhat. The user's login shell should be
non-interactive. (no ssh access to server)
2- Add user1 to group TrainingGroup.
3- Add users: user2, user3. The Additional group of the two users: user2, user3 is the admin
group Password: redhat, user 3 with root permissions
