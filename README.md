# MOUNTING AN S3 BUCKET TO A EC2 INSTANCE STEP BY STEP

## NOTA BENE:

    . we'll use amazon linux instance

## Step 1: run an instance

    1. run an ec2 instance
    2. connect to it via ssh

## Step 2: install package that we'll need

    1. aws cli is preinstalled on amazon linux instance so we dont need to install it and git also
    2. update packages
```bash
sudo yum update -y
```
    3. install all packages:
```bash
 sudo yum install automake fuse fuse-devel gcc-c++ git libcurl-devel libxml2-devel make openssl-devel -y
 ```
## Step 3: install s3fs
    1. clone the the repo
```bash
git clone https://github.com/s3fs-fuse/s3fs-fuse.git
```
    2. excute this
```bash
cd s3fs-fuse/ \
./autogen.sh \
./configure --prefix=/usr --with-openssl \
make \ 
sudo make install
```
    3. verify installation: 
```bash
which s3fs
```
 ## Step 4: configre aws cli (optional)
```bash
aws configure
```
    and put your access credentials in there with the account region
 ## Step 5: create a directory which will use to sync to the s3 bucket
    1. create the directory
```bash
mkdir -p ${HOME}/bucket
```
    2. give the directory the right permission
```bash
chmod 755 ${HOME}/bucket
```
    3. create files in the directory for test (optional)
```bash
touch file.txt
```
 ## step 6: sync the directory to the s3 bucket (optional)
 ```bash
 aws s3 sync /home/ec2-user/bucket s3://figs-bucket
 ``` 
 ## Step 7: create a password file that s3fs will use later to mount the s3 bucket
    1. create the file
 ```bash
 echo ACCESS-KEY:SECRET-ACCESS-KEY > ${HOME}/.passwd-s3fs
 ```
    2. give it the right permission
```bash
chmod 600 ${HOME}/.passwd-s3fs
```
 ## step 8: mount the s3 bucket: 
    1. excute this code:
 ```bash
 sudo s3fs figs-bucket $HOME/bucket -o passwd_file=$HOME/.passwd-s3fs,nonempty,rw,allow_other,mp_umask=002,uid=$UID,gid=$UID -o url=https://eu-north-1.amazonaws.com,endpoint=eu-north-1,use_path_request_style,dbglevel=info,curldbg
 ```
    2. mount: 
```bash
mount | grep s3fs
```

