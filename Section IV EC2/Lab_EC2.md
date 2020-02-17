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





