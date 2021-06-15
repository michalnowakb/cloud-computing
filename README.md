### What is Cloud Computing?
- Simply put, cloud computing is the delivery of computing services including servers, storage, databases, networking, software, analytics and intelligence over the Internet (or “the cloud”).
- Benefits:
	- Flexibility
	- Efficiency
	- Strategic value

# Who is using it in the industry?
- Netflix, Amazon Prime, Spotify


### What is AWS?
- Cloud Computing Platform from Amazon (Amazon Web Services) which uses 3 main parts:
	- SaaS (Software as a Service)
	- IaaS (Infrastructure as a Service)
	- PaaS (Platform as a Service)

- Benefits:
	- Easy to Use
	- Flexible
	- Secure






### How to create an EC2 instance and deploy the node app

## EC2

- choose `EC2` inside AWS
- search for Ubuntu Server 16.04 LTS (HVM), SSD Volume Type and select it (must be 64-bit (x86))
- select the t2.micro under the Type column
- click Next: Configure Instance Details
- Network: leave it as default
- Subnet: select the DevOpsStudents subnet
- Auto-assign Public IP: `Enable`
- click Next: `Add Storage`
- Choose how much storage you need and the Volume Type
- click Next: `Add Tags`
- Add whatever tags you'd like
- click Next: `Configure Security Group`
- Choose Create a new security group
- for SSH under the Source column select `My IP`
- Add a new rule of type HTTP and under the Source column set it to Anywhere
- Add a new rule of custom TCP with port `3000` and Source column set to `Anywhere`
- click Review and Launch

## Deploy the node App

- Open AWS and a local terminal
- Select the instance you just created
- Under the SSH client column you will find informations on how to connect via SSH
- Do chmod 400 devop_bootcamp.pem (this is the key file)
- Now ssh into your VM using the ssh command provided in AWS: ssh -i "devop_bootcamp.pem" user@awsVirtualMachineLink.com
- After you managed to ssh into your VM do the following:
	- `sudo apt-get update -y`
	- `sudo apt-get upgrade -y`
	- `sudo apt-get install nginx`
	- `sudo apt-get install python-software-properties`
	- `curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -`
	- `sudo apt-get install nodejs -y`
	- `sudo npm install pm2 -g`

- Now you need to exit from your VM 
- do the following:
	- `scp -i ~/.ssh/devop_bootcamp.pem -r /home/user/app user@vmLink.com:/home/ubuntu/`
- now log back in
- go inside `/home/ubuntu/app`
- run node `app.js`
- access yourIP: 3000 in the browser 

### How to create an EC2 database instance and deploy the mongoDB

- choose `EC2` inside AWS
- search for Ubuntu Server 16.04 LTS (HVM), SSD Volume Type and select it (must be 64-bit (x86))
- select the t2.micro under the Type column
- click Next: Configure Instance Details
- Network: leave it as default
- Subnet: select the DevOpsStudents subnet
- Auto-assign Public IP: `Enable`
- click Next: `Add Storage`
- Choose how much storage you need and the Volume Type
- click Next: `Add Tags`
- Add whatever tags you'd like example: `Name | devops_bootcamp_db`
- click Next: `Configure Security Group`
- Choose Create a new security group
- for SSH under the Source column select `My IP`
- Add a new rule of custom TCP with port `27017` and Source column set to `Custom` and add an IP referring to your app instance
- click Review and Launch

## Installation MongoDB on your db instance
- Open AWS and a local terminal
- Select the instance you just created
- Under the SSH client column you will find informations on how to connect via SSH
- copy a file with script to install mongoDB using `scp -i ~/.ssh/devop_bootcamp.pem -r /c/Users/eXo/Desktop/DevOps/Vagrant/mongoDB ubuntu@ec2-34-245-106-120.eu-west-1.compute.amazonaws.com:/home/ubuntu/`
- go to mongoDB folder directory
- allow script to be executed using `sudo chmod +x provision_db.sh`
- run script `./provision_db.sh`

OR

- `run every command directly from your instance ubuntu terminal`
sudo apt-get update -y
- `sudo apt-get upgrade -y`
- `wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -`
- `sudo apt-get install gnupg`
- `wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -`
- `echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list`
- `sudo apt-get update -y`
- `sudo apt-get install -y mongodb-org`
- `sudo rm /etc/mongod.conf`
- `sudo ln -s /home/ubuntu/mongoDB/mongod.conf /etc/mongod.conf`
- `sudo systemctl restart mongod`
- `sudo systemctl enable mongod`
- `sudo systemctl status mongod`


## Connecting app instance with db instance
- go to your app instance via SSH from AWS
- edit `.bashrc` file using `sudo nano .bashrc`
- go to very bottom of the file and create new environment variable `export DB_HOST="mongodb://db_instance_ip:27017/posts"` 
- then use `source .bashrc` command
- type `sudo nano /etc/profile` and at very bottom add `DB_HOST="mongodb://db_instance_ip:27017/posts"`
- now you can go to app directory using `cd app`
- go to seeds folder and run `node seed.js`\
- go back to app folder and run `npm start`
