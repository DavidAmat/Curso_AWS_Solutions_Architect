# EC2 Part 1


## Launch an instance

- Launch an instance (new):

- **We have to choose an Amazon Machine Image (AMI)**

<img src="imgs\img1.PNG" width="800px" />

- An AMI is a template that contains the software configuration (OS, application server and applications) required to launch your instance. You can select the AMIs provided by AWS, the user community AWS, AWS Marketplace or select on of your own AMIs.
- We select the **First one (Linux)**


### Instance types
- We then see the **instance types**:


<img src="imgs\img2.PNG" width="800px" />

- We will use t2.micro since it goes along with the **Free tier**.

### Instance details
- We configure Instance Details:

<img src="imgs\img3.PNG" width="800px" />

- We will not select the request of spot instances (we will go On-Demand)
- We will launch the instances into our **default VPC** 
- **Subnet**: is a way for choosing which AZ you want to go into
- **Placement group** is for high performance computing 
- **Capacity reservation**: reserve capacity for EC2 in an AZ
- IAM roles (cover that later)
- **Shutdown behaviour**: what happens if we shut down this instance? Do we stop it or terminate it?
- **Enable termination protection**: prevent from accidental termination of the instance 
- **Monitoring**: enabling CloudWatch (later in the course). CloudWatch monitors the EC2 instances every 5 min. If you want to be it every X min or seconds you have to enable the **detailed monitoring** check (but this is not covered in the Free Tier)
- **Tenacy**: 
	- Shared: Run a shared hardware instance
	- Dedicated: run a dedicated instance
	- Dedicated host: launch this instance on a dedicated host
- **Elastic Interface**: we haven't covered it yet 
- **Advanced Details**: here we can pass **bootstrap scripts**. 

<img src="imgs\img4.PNG" width="800px" />


### Add Storage

- In Volum Type you can select the EBS volume type:
	- General Purpose SSD
	- Provisioned IOPS SSD
	- Magnetic (Standard)
- If we add a new volume we get more options in Volum Types... This is because in the **root device** we can only **launch on SSD or magnetic standard**, but we have additional volumens (Cold HDD, THroughput Optimized HDD)... 
- When we say root device volume or EBS we are talking about a **virtual disk in the cloud**.  This is where our OS will be installed. 

<img src="imgs\img5.PNG" width="800px" />

### Add Tags

- We add Tags, which are key.value pairs.
- Tags will be applied to **all instances and volumes**.
- They are a way of keeping track of our EC2 and AWS infraestructure.

<img src="imgs\img6.PNG" width="800px" />

### Configure Security Group

- Security Group is just a **virtual firewall**. 
- It allows  communication **over particular ports**
- It enables traffic on the different ports to reach your instance
- Allowing different types of communication to EC2 instance through your security group
- When we create a Security Group, all the webservers are going to go into that.

- We can **Add rules**:
	- We can add HTTP type, on the side address range 0.0.0.0/0
	- This address essentially mean that you are **opening the port to any IP address**
	- It is recommended to set the security group rules to allow access from known IP addresses only.
	- You can lock it down to your IP address (but it you IP changes, you won't access this EC2 instance)

<img src="imgs\img7.PNG" width="800px" />

- When we hit Create, we will be asked a **key pair**:
- A key pair consists of a **public key** (that AWS stores) and a **private key** (that YOU store).
- **Together, they allow you to connect to your instance securely**
- For Windows AMIs, the private key is required to obtain the password to log into the instance
- For Linux AMIs, the private key alloys you to **SSH** into your instance

#### Public Key and Private Key
- **Public Key**: Can be though as a padlock (candado), which you can put everywhere you want
- **Private Key**: is the key that unlocks the padlock.


We proceed and create a new key pair: **MyUSE1KP** and Download files to "D:\Projectes\Curso_AWS_Architect\access"

- We Launch Instance and see that is Initializing > View Instances

- The **Public DNS** is a web address that we can use to go and visit our EC2 instance 
- In the **Decription** section we can see our Public IP Address:


## SSH into the created instance

- Get the IPv4 Public IP of the instance and SSH into it:

- You can use the **Connect** button to connect

- EC2 Instance Connect (your user name is always gonna be ec2-user):

- This opens a new tab and establish a session directly with your EC2 instance.

- **I can do Linux commands to elevate my privileges to root**:
```bash
sudo su
```


### Terminal configuration

- We log in via SSH in Putty
- https://www.youtube.com/watch?v=bi7ow5NGC-U
- We have to convert the PEM file to a format readable by putty (puttygen allows to do so)
- We ssh with our credentials in ec2-3-88-4-2.compute-1.amazonaws.com:

- The file D:\Google Drive\1.Estudis\3.CursosOnline\Curso_AWS_Solutions_Architect\credentials has all the required files to do that connection via PuTTY.


### Install Apache

```bash
ym install httpd -y
```

- This command basically install Apache: turns your EC2 instances into a Web Server
- You can go to the directory:

```bash
cd /var/www/html
```


- Anything you **put there will be basically a website or be accessible over port 80.**
- If we create an index.html file we will see result:

<img src="imgs\img8.PNG" width="600px" />

### Start WebServer
```bash
service httpd start
```
Start the HTTPD service if your EC2 accidentally reboots (you don't have to manually turn it on)
```bash
chkconfig on
```

Go to a Web Browser and type your IP address (in credentials.txt we have the Public IP of your instance):
```bash
#Public IP
3.93.11.239
```


# EC2 Part 2

## Status Check

Status checks detect problems that may affect the instance from running your apps.

There are several checks that monitor the software and network configuration for the instances

### System Status Check

- Verifies if instance is **reachable** (if we are able to network packets to your instance)
- If the check fails, maybe the **infraestructure hosting your instance** have had a problem (power, network, software)
- You may need to restart or replace the instance or wait for the system to resolve it
- **Does NOT validate if you OS and app is ACCEPTING traffing**

### Instance Status Checks

- Verifies that your instance's operating system is accepting traffic
- If the check fails, you may need to reboot your instance or make changes on your OS config

## Reserved Instances

We can go to Reserved Instances and look for prices and duration terms of your purchas Instance Type.

<img src="imgs\img9.PNG" width="600px" />

## Volume Type

- When creating a instance, in the Add Storage step, we were asked to choose the volume type (general purpose SSD, ...)
- IOPS: 100 / 3000 from 100 IOPS to 3000 IOPs (baseline of 3 IOPS per Gb) (how fast your hard disk drive is) 
- **We can set the Delete on Termination**
- **Encryption**: you can encrypt your root device volume (!!! POPULAR EXAM TOPIC!!!)
- If you choose Volume Type (EBS) the hard disk options changes (for things like DataWarehousing we may choose a HDD). 
- Choosing an EBS mya lead to the Htorughput colum (MB/s) being displayed (from 20 to 123 MB/s)
- **Delete on Termination is NOT ticked for the EBS automatically**: this means that if you delete the Root, it would NOT delete the attached Volumes to that EC2 instance. 

<img src="imgs\img10.PNG" width="600px" />

## Exam Tips

- Termination protection is turned off by default, you should turn it on
- On EBS-backed instance, the **default action is for the root EBS volume to be deleted if the instance is terminated**: this means that if we delete the instance, our Root Volume will be deleted too. Any **additional volumes** by default won't be deleted!!!
- EBS Root Volumes of your AMI's **CAN BE encrypted**.  You can also used 3rd party tool (bit locker) to encrypt the root volume, or do it when creating AMI's (following lab) in the AWS Console or using the API



# Security Groups

- The reason why we can access the WebServer via our Web Browser using uour Public IP address is because  **we enabled Port 80 on our SECURITY GROUP**.
- In the Instances > Description we see a section for **Security Groups** (the ones attached to your instance)
- We can see the **imbound roles**

<img src="imgs\img11.PNG" width="600px" />

- Go to **Network & Security** > Security Groups
	- Create, Edit, Delete SG
	- In the Description we have the name and in which VPC it is in
	- In **Inbound** we have our Inbound roles:

<img src="imgs\img12.PNG" width="600px" />

## Inbound rules
 - We have opened the port 80 and we have done in the Source and IPv4 (0.0.0.0/0) route and IPv6 (::/0) and done an SSH on port 22
 - If we **Edit** and REMOVE the HTTP rows that **CHANGES IN THE SECURITY GROUP affects IMMEDIATELY** (so we won't be able to access our Web Server) (!!!POPULAR EXAM QUESTION)
 - We can **Add Rule**  with the HTTP roles agains and try to browse again and see that we can access the Web Server in your browser.
- When you Edit an inbound rule you have to provide a CIDR Address range, IP or Security Group

## Outbound rules

<img src="imgs\img13.PNG" width="600px" />

- If we delete ALL rows of our Outbounds **nothing happens**
- This is because **Security Groups are STATEFUL **, when you create an inbound rule (i.e HTTP) an OUTBOUND rule is CREATED AUTOMATICALLY**
- In the VPC section, you will learn about NAC (Network Access Controls), and those are **STATELESS**: when you create an inbound rule, you have to create an OUTBOUND rule. 

- When we whit **Edit** in the Inbound rules, I was able to ALLOW ports, not **BLOCK**
- !!! You **CAN'T BLACKLIST a particular PORT or aparticular IP adrdresss with SECURITY GROUPS**, to do so, you have to go to **NAC** in VPC 
- When you create a **Security Group**, **everything is BLOCKED by default** you have to go in and allow something. 

<img src="imgs\img14.PNG" width="600px" />

## SG to Instance

- You can attach MORE than 1 SG to your INSTANCE
- **Actions** > **Networking** > **Change Security Group**

<img src="imgs\img15.PNG" width="600px" />

- In the **view inbound rules** we can see which ports are opened by each SG:

<img src="imgs\img16.PNG" width="600px" />

- To Remove a Security Group from your instance (not removing the SG as itself)  you also go to  **Actions** > **Networking** > **Change Security Group**

## Exam Tips

- All inbound traffic is blocked by default (we have to enabled it with SG)
- All Outbound traffic is allowed (we deleted the outbound rule but because SG are Statefull they allow in and out)
- Changes to SG take effect immediately
- You can have any number of EC2 instances within a SG
- You can have multiple SG attached to an EC2 instance

- SG are Stateful (outbound is enable automatically for that port if inbound rule is set)
- Inbound rule allow traffic in
- You cannot block specific IP addresses using SG, you have to use NAC lists (NACL).
- You can set ALLOW rules but NOT DENY rules in a SG (you should use NACL). 


# EBS Volumes and Snapshots


## Encrypted Root Device Volumes & Snapshot


- The Root Device Volume is the EBS Volume which has the OS installed on it
- You can encrypt root device volumes **when creating an instance**, in the past you could NOT.

### How to encrypt a RUNNING INSTANCE?

- **Create a new instance without encryption**:


<img src="imgs\img17.PNG" width="600px" />
<img src="imgs\img18.PNG" width="600px" />

- Go to Volumes and Create Snapshot from the existing root device volume:

<img src="imgs\img19.PNG" width="600px" />
<img src="imgs\img20.PNG" width="600px" />

- Create an Encrypted Copy of our Snapshot:

<img src="imgs\img21.PNG" width="600px" />
<img src="imgs\img22.PNG" width="600px" />

- Create an image from the encrypted snapshot:

<img src="imgs\img23.PNG" width="600px" />
<img src="imgs\img24.PNG" width="600px" />

- This creates an AMI, so we use that AMI to launch a new encrypted EC2 instance

<img src="imgs\img25.PNG" width="600px" />
<img src="imgs\img26.PNG" width="600px" />

- You will see in the Add Storage section that the volume is encrypted (you cannot select the Not Encryted version!!):
<img src="imgs\img27.PNG" width="600px" />
<img src="imgs\img28.PNG" width="600px" />

### Exam tips

- Snapshots of encrypted volumes are encrypted automatically
-  Volumes restored from encrypted snapshots are encrypted automatically
- You can share snashots, only if UNENCRYPTED
- These snapshots can be shared with other AWS accounts or made public (unencrypted)
- You can NOW encrypt root device volumes upon creation of the EC2 instance 
- If you want to encrypt existing instance
	- create a snapshot of the unencrypted Root Device Volume:
	- Create a copy of the snapshot and select the encrypt option
	- Create an AMI from the encrypted Snapshot
	- Use that AMI to launch new encrypted instances


# CloudWatch

- Go to EC2 and launch a **new instance and ENABLE CloudWatch detailed monitoring**.
- This allows to monitor our instance at **1 minute intervals**

<img src="imgs\img29.PNG" width="600px"/>

- If we go to our instance Status Checks, we will see that both tests (System Status and Instance Status) are passed (green):

<img src="imgs\img30.PNG" width="600px"/>

- On **Monitoring we see HOST level metrics: CPU, Disk, Network, Status Checks...**.

<img src="imgs\img31.PNG" width="600px"/>


## Set up an alert

Go to **Services > Management & Governance > CloudWatch**

- Go to Alarms and Create an alarm:

<img src="imgs\img32.PNG" width="600px"/>

- Select a metric:

<img src="imgs\img33.PNG" width="600px"/>
<img src="imgs\img34.PNG" width="600px"/>
<img src="imgs\img35.PNG" width="600px"/>

- We do it on a per-instance metric and **find CPU utilitzation for you instance**:

<img src="imgs\img36.PNG" width="600px"/>

- Click select metric:

<img src="imgs\img37.PNG" width="600px"/>

- Put a name of your alarm and whenever CPU is > 90 for **1 minute** , **send me an alarm**:

<img src="imgs\img38.PNG" width="600px"/>

- **Actions**:
	- Whenever the alarm State is ALARM
	- Send notification to a email list (give a name to send notification to):

	<img src="imgs\img39.PNG" width="600px"/>

For the created alarm:

<img src="imgs\img40.PNG" width="600px"/>

**Now I do an SSH to my EC2 instance**:

<img src="imgs\img41.PNG" width="600px"/>

- Put the instance into an infinite loop:

<img src="imgs\img42.PNG" width="600px"/>

- That's gonna maxout your CPU... In a minute you will get an email that your EC2 instance is in a state of an ALARM so we receive the email: 

<img src="imgs\img43.PNG" width="600px"/>

- In the monitoring section I can see that the CPU utilization has reach 100%... 
<img src="imgs\img44.PNG" width="600px"/>

- Go back to CloudWatch and see that in Alarms, an alarm has been triggered:

<img src="imgs\img45.PNG" width="600px"/>

- We can create a dashboard (dashboards can be global taking into account monitoring performances of **other regions**): 

<img src="imgs\img46.PNG" width="600px"/>

- In the Logs, we can do performance logging essentially and send all logs to cloudwatch

<img src="imgs\img47.PNG" width="600px"/>

- CloudWatch Events: near real time stream of system events that describe changes in AWS resources... 

<img src="imgs\img48.PNG" width="600px"/>

## Exam Tips

- **Standard Monitoring** = 5 minutes
- **Detailed Monitoring** = 1 minute

- Create dashboards to see what happens to your AWS environment
- Create alarms to notify particular thresholds are hit in terms of performance 
- Create events: helps to respond to state changes in your AWS resources
- Create logs: CW allow to aggregate, monitor and store logs
- CloudWatch monitors performance while CloudTrial monitors API calls in the AWS platform.
	- Who setup an S3 bucket ? Who provisioned the EC2 instance ?  -> Cloud Trail
	- Performance -> CloudWatch

# AWS Command Line (CLI) 

Once you have you instance EC2, to launch the CLI, do an ssh:

```bash
ssh ec2-user@<ip_address> -i XXXXX.pem
sudo su
```

To activate the CLI, you have to **configure you credentials** to programmatic access:


```bash
aws configure 
```

- This asks for Access Key ID and Secret Access Key, Default region and default out (empty)

<img src="imgs\img49.PNG" width="600px"/>

If we run:

```bash
aws s3 ls
```

- I will be able to access S3 and list all my buckets. 
- **Create a new BUCKET**:
```bash
aws s3 mb s3://<name_bucket> #(unique)
```
With the **CLI** you can **provision EC2 instances, almost anything**.

 - Then you can explore the ~ directory (there is a hidden directory .aws):
 ```bash
cd ~
cd .aws
```

- Here, there is you config and credentials:

<img src="imgs\img50.PNG" width="600px"/>
<img src="imgs\img51.PNG" width="600px"/>

- This a security **risk**, they can use that access key to access on other EC2 instances... 
- That is why AWS recommends **using ROLES** (see **NEXT SECTION ABOUT ROLES**)

## Exam tips

- We can interact with AWS from anywhere just by using the CLI
- You need to setup access in IAM 
	- Create a user with programmatic access:
		- This enables an access key ID and secreta ccess key for the AWS API, CLI and SDK 

	<img src="imgs\img52.PNG" width="600px"/>

	- Put the user in a group, apply **Administrator access policy** to that group so the user will inherit Administrator access. 
	- THen you get your Access Key ID and Secret Access Key, which will be needed to launch the aws configure command 
	- **If you loose your credentials, go to IAM > Users**, click into your user, and go to **Security Credentials** and **Make Inactive** and then "Create access key" to create a new one

	<img src="imgs\img53.PNG" width="600px"/>

	<img src="imgs\img54.PNG" width="600px"/>

- Commands are not in the exam of the CLI

# IAM Roles

Roles enables us to **interact with the AWS platform** without having to pass our **access keys IDs**. 
Roles are used for EC2 and every other AWS service.

- **Go to IAM > Roles** and you will see a role that we created when enabling cross-region replication for our buckets.
- We create a new one on EC2.

<img src="imgs\img55.PNG" width="600px"/>

- The I am asked to attach a policy: give Administrator access for this role:

<img src="imgs\img56.PNG" width="600px"/>

- Once created, we to to **EC2 > Instances**. And ssh to your instance:

<img src="imgs\img57.PNG" width="600px"/>

- Remember that we complained about the fact that inside the CLI we have a folder .aws that stores the credentials, hence, to avoid this, we delete that folder. 

- **Once deleted, if I try to do an aws s3 ls I won't be able to see the buckets cause I deleted the credentials**:

<img src="imgs\img58.PNG" width="600px"/>

- We can **attach a role to the EC2 isntance**:

<img src="imgs\img59.PNG" width="600px"/>
<img src="imgs\img60.PNG" width="600px"/>

- We can review the Administrator Access policy in the Roles by looking at the info for the IAM Role and looking at the policy attached:

<img src="imgs\img62.PNG" width="600px"/>
<img src="imgs\img61.PNG" width="600px"/>

- Now, if I go to my CLI, you will see that you can ls your S3, since we have full administrator access in the AWS environment:

<img src="imgs\img63.PNG" width="600px"/>

- But the .aws it does not exists (no credentials being saved on the instances .aws folder). **This is done to prevent hackers to take your credentials**

## Exam tips

- **Roles are FAR MORE SECURE than storing your access key and secret access key on EC2 instance (.aws folder)**.
- Roles are easier to manage (if you have 1,000 instances, and you update the key... you would have to change manually your 1,000 instance credentials) whereas if you have a role you can go in an change the role and that effect will take place immediately.
- Roles can be assigned to an EC2 instance after it is created using the CONSOLE or CLI.
- **Roles are UNIVERSAL, you can use them in any region** (you create them in IAM)


# Bootstrap Scripts - Bash (Automate AWS Deployments)

Way to **run things at the CLI** when your AWS EC2 instance **first boots**, install software, run commands...

- Create an instance with IAM Role to Administrator Access:

<img src="imgs\img64.PNG" width="600px"/>

- Then I will create an S3 Bucket using my Bootstrap Script. 
- A **Bootstrap Script is provided in the Advanced Details when creating an Instance**:

<img src="imgs\img65.PNG" width="600px"/>
<img src="imgs\img66.PNG" width="200px"/>
<img src="imgs\img67.PNG" width="400px"/>

- We go to our www html to create a web page with echo and redirect to index.html.
- We create a random S3 bucket
- Back up my web page to my S3 bucket:

```bash
aws s3 cp index.html s3://<bucket_name>
```

- We create the instance! You should be able to navigate to the IP Public IP Address:

<img src="imgs\img68.PNG" width="600px"/>

<img src="imgs\img69.PNG" width="300px"/>

- And see that we have created an S3 bucket:

<img src="imgs\img70.PNG" width="300px"/>


# EC2 Instance Metadata (curl)

- SSH insto your EC2 instance, and sudo su.

- Do a curl command to **user/data** or **meta-data** to see our bootstrap script:

```bash
curl http://169.254.169.2/latest/meta-data/<option>
# <option> = local-ipv4
```
<img src="imgs\img71.PNG" width="300px"/>

- We can output the output to a bootstrap.txt file, we will see the script we made in the previous section. 

- **Curl command can help us to get the data from our EC2 instance** (user / metadata) and hit the local-ipv4 option:

<img src="imgs\img72.PNG" width="300px"/>

- **What I can do is create a BootStrap script that runs this curl, takes the public IP, writes it to a file and then it copies automatically to S3, and that could trigger a Lambda Function and store that IP address in a Database**. 

## Exam tips

- Metada is used to get information about an instance (such as Public IP)
- You can curl to latest: **meta-data** or **user-data** (BootStrap script that you run when first provisioned the instance)

# EFS

- We provision an EFS: **Storage > EFS > Create file system**:

<img src="imgs\img73.PNG" width="600px"/>

- There are 3 AZ it will be spread across. The instances connect to a file system using a newtork interface called a **mount target**. Each mount target has an IP address, which we assign automatically.

- EFS FS is accessed by EC2 instances running inside one of your VPCs.

- We also have **Lifecycle policies** for EFS, they are move to **EFS Infrequent Access** class which is cheaper:

<img src="imgs\img74.PNG" width="600px"/>

- Enable encryption: use the KMS master key.

- **Configure client access**:

	- A file system policy is an IAM resource policy that applies to all NFS clients that connect to that file system. You can edit permissions to grant permissions to different IAM roles or different AWS accounts.

<img src="imgs\img75.PNG" width="800px"/>

- **On parallel, we launch EC2 instances that will share the EFS volume**:

	- We create 2 instances and provide a Bootstrap script to convert them to web servers:

	<img src="imgs\img76.PNG" width="800px"/>

	- We create a Bootstrap script:

```bash
#!/bin/bash
yum update -y
yum install httpd -y
service httpd on
yum install -y amazon-efs-utils
```

<img src="imgs\img77.PNG" width="800px"/>

- In the Security group, use the existing Security Group of WebMZ to allow inbound traffic for the HTTP protocols and all output traffic.

- Launch the 2 instances!! While waiting, if we go to **Security Groups** we will see the default SG. We provisioned our EFS system on the **default SG** and our web server is going to communicate with our EFS system using the NFS protocol.  

<img src="imgs\img78.PNG" width="800px"/>

- That is why we need to **allow inbound traffic por the NFS**:

<img src="imgs\img79.PNG" width="800px"/>
<img src="imgs\img80.PNG" width="800px"/>

- We allow NFS protocol into our default SG from our WebServers.
- We then get the public IPv4 IP addresses of the EC2 instances:
	- 34.226.138.168
	- 54.86.129.154

- We SSH both instances:
	- ec2-user@34.226.138.168 -i MyUSE1KP
	- ec2-user@54.86.129.154 -i MyUSE1KP

- If the directory /var/www/html exists it means that we have correctly installed apache.
- Elevate your privileges:

```bash
sudo su
```

<img src="imgs\img81.PNG" width="800px"/>

- We go back to the AWS console and go to EFS and **get the file system ID**:

	- fs-d2ae2b52

- Go at each instance and run:

```bash
sudo mount -t efs -o tls fs-d2ae2b52:/ /var/www/html
```

<img src="imgs\img82.PNG" width="800px"/>

- Then you can verify that by creating one file in one instance and look at it in the other instance.

- To terminate EFS, first delete the EC2 instances.


## Exam Tips

- EFS you only pay for the storage you use (pay as you go) (no pre provisioning required)
- You can scale up to petabytes
- Can support thousands of concurrent NFS connections 
- Data is stored across multiple AZ within a region
- Consistency is READ after write (you create the file in a EC2 and in another EC2 you can see that file)