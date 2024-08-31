# Creating and Mounting NewFileSystem using Amazon EFS
## About Amazon EFS
Amazon EFS provides scalable file storage for use with Amazon EC2. You can use an EFS file system as a common data source for workloads and applications running on multiple instances. Amazon Elastic File System (Amazon EFS) automatically grows and shrinks as you add and remove files with no need for management or provisioning. 
## Creating an Instance
AMI- Ubuntu(Free Tier)

User Data- multios_websetup.txt
![image](https://github.com/user-attachments/assets/a1b151c1-5e29-4616-ae38-c9aaad611a50)
Connect to this instance using SHH Client.

## Creating Security group for EFS
Type- NFS , Source- Custom(Security Group of our instance)
![image](https://github.com/user-attachments/assets/5856103a-6e00-4741-8628-67cd76a99796)

## Creating file system and access point
For file system, customize settings
![image](https://github.com/user-attachments/assets/b493a067-c940-4f98-892d-f85d24879ea7)
![image](https://github.com/user-attachments/assets/ec2ef91b-77e1-4379-8e7e-abb9b070b3c4)

For access point, choose the file system.
![image](https://github.com/user-attachments/assets/e7fd456f-92c3-4fb0-a496-93049bcff773)

## Mounting file system to a directory in our EC2 Instance
### Installing Amazon EFS client(amazon-efs-utils)
https://docs.aws.amazon.com/efs/latest/ug/using-amazon-efs-utils.html

To build and install a Debian package:
```
sudo apt-get update
sudo apt-get -y install git binutils rustc cargo pkg-config libssl-dev
git clone https://github.com/aws/efs-utils
cd efs-utils
./build-deb.sh
sudo apt-get -y install ./build/amazon-efs-utils*deb
```
This will take time.

### Mounting file system
https://docs.aws.amazon.com/efs/latest/ug/mount-fs-auto-mount-update-fstab.html

We have to manually update the /etc/fstab on an EC2 Linux instance so that the instance uses the EFS mount helper to automatically remount an EFS file system when the instance restarts.
```
vi /etc/fstab
```
To mount a file system using an EFS access point, adding following line to the /etc/fstab file.
```
file-system-id:/ efs-mount-point efs _netdev,noresvport,tls,accesspoint=access-point-id 0 0
```
For me, it is-
```
fs-0264f07c465241bc6:/ /var/www/html/images efs _netdev,noresvport,tls,accesspoint=fsap-03ea99628242e80c0 0 0
```
First, moving any files in /var/www/html/images to a new location and adding them after mounting.
```
mkdir img
mv /var/www/html/images/* /root/img
```
/etc/fstab file-

![image](https://github.com/user-attachments/assets/3b7ed81c-3b05-4170-bfcf-e8a8136a56c2)

After this run command-
```
mount -fav
```
![image](https://github.com/user-attachments/assets/7ccbfa1e-1bc7-425b-9a5a-8c3844e018b0)

We can check is file system mounted or not using command-
```
df -h
```
![image](https://github.com/user-attachments/assets/f6e99d7d-5c7c-4696-b255-66236b360dea)

Our new file system is successfully mounted.

