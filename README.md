# Ansible AWS & Docker Demo Script

## What this script does
- Setup a VPC with two public subnets
- Setup two Security Groups one for Web and one for Load Balancer
- Create an EC2 Instance
- Create an Application Load Balancer
- Configure Docker and run Docker in the created instance

## Requirements
Things required to run the script:
- Ansible 2.8
- Python
- Boto 3
- Pip
- AWS Access and Secret Key

## Install
### Ansible 2.8

We will have get Ansible from ansible personal repository
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367
sudo apt-add-repository "deb http://ppa.launchpad.net/ansible/ansible/ubuntu bionic main"
sudo apt install ansible
```

### Pip

```
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
sudo python get-pip.py
```

### Boto3

```
sudo pip install boto3
```

# Running Script

## Setting up AWS VPC, and EC2

If you want to run this script you will be needing to add your AWS Access and Secret Key to `setup-aws.yml`

Should look something like this
```
---
- hosts: localhost
  vars:
    access_key: # Put Access Key Here
    secret_key: # Put Secret Key Here
  roles:
    - ec2
```

#### Running file to manage AWS
Since we aren't connecting to a remote server this ansible file will run locally so all you need to do is run
```
ansible-playbook setup-aws.yml
```
Optionally you can add `-vvvv` to see logs as it runs

After running successfully, this should create three files for you. First it will create `hosts.yml` in root directory of this project which has the Public IP of the instance created. Second a file called `dns.txt` this has the DNS name for the the Application Load Balancer that is created with this project. Finally it will create a folder in root directory of you machine called `.pems`  and there is created a private key  `demo-key.pem`, you will need to change permission for this file to be 400.

#### Running files to setup Docker
You can simply run this command to configure and setup Docker in the EC2 instance
```
ansible-playbook configure-ec2.yml -i hosts.yml --private-key ~/.pems/demo-key.pem
```

Using the DNS name in `dns.txt` you can access the Hello World that is running in the machine


## Default Variables
You can overwrite these default variable by defining in `setup-aws.yml`
```
aws_region: ap-southeast-1
vpc_name: "My VPC"
vpc_cidr_block: "10.0.0.0/16"
vpc_public_subnet_1_cidr: "10.0.0.0/24"
vpc_public_subnet_2_cidr: "10.0.1.0/24"
instance_type: 't3.micro'
# https://cloud-images.ubuntu.com/locator/ec2/
ami_id: 'ami-068f0e11411e30b36'
instance_count: 1
target_group_name: ansibledemo

```
