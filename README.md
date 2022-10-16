# Task1


## Question 1: LVM

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


