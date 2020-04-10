# Custom VPC - Part 1

- View all VPCs in all regions

<img src="imgs\img0.jpg" width="700px" />

- In my VPCs there is the default VPC:

<img src="imgs\img1.png" width="700px" />

- In subnets we see how each subnet has a different IP, that the VPC has 3 subnets and they are on different AZ (IPs are /20 giving 4091 availabel IP addresses):

<img src="imgs\img2.png" width="700px" />

- Route tables (default route tables are created when creating default VPCs):

<img src="imgs\img3.png" width="700px" />

- Internet gateway allow our VPC to talk to the Internet 
<img src="imgs\img4.png" width="700px" />

- Network ACLs:

<img src="imgs\img5.png" width="700px" />

- Security Groups (SG):

<img src="imgs\img6.png" width="700px" />

- **Create a VPC**:

<img src="imgs\img7.png" width="500px" />

- He wants the biggest address block possible, so he goes for the /16 address... . He also sets the IPv6 CIDR Block (is up to you).  If we want everything to be on **dedicated hardware** we should use the "Dedicated Tenacy" however this could cost us a fortune... The default is **multi-tenant** se we are sharing the underlying hardware with other AWS customers.

<img src="imgs\img8.png" width="500px" />

- We cannot still see the subnets for this new VPC... 

<img src="imgs\img9.png" width="500px" />

- So when we create a VPC it **DOES NOT CREATE any subnet**. But we have Route table:

<img src="imgs\img10.png" width="500px" />

- **It DOES NOT create an Internet Gateway**

- In **Network ACL it DOES create a NACL**:

<img src="imgs\img11.png" width="500px" />

- **It DOES create a SECURITY GROUP**

<img src="imgs\img12.png" width="500px" />

- To use the VPC we need to create subnets:

<img src="imgs\img13.png" width="500px" />

- Select the **availability zone and the VPC for the subnet**:

<img src="imgs\img14.png" width="500px" />

- We create another VPC in another AZ. Put another name. It is advisable that in the subnet name you put the IP ranges and the AZ:

<img src="imgs\img15.png" width="500px" />

- The field of Auto-assign IPv6 address is by default turned OFF. We have to fix it since **OUR MAIN GOAL IS TO HAVE ONE SUBNET AS A A PUBLIC SUBNET AND ANOTHER AS A PRIVATE SUBNET**. We should also note that here we only have available 251 IPv4 addresses (since the system reserves some special addresses of 10.0.XXXX for reserved services - services, network address, IP of the DNS server, network bradcast address and special reserved)

- In order to convert a subnet as PUBLIC we are **going to need TWO instances to launch in it with public IP addresses**. We are going to need to **auto-assign** public IP addresses  

<img src="imgs\img16.png" width="500px" />
<img src="imgs\img17.png" width="500px" />

- We are now able to launch an EC2 instances into this subnet and it will automatically be able to get a public IP address.

<img src="imgs\img18.png" width="500px" />

- What we have now is this:

<img src="imgs\img19.png" width="500px" />

- We need a way to get into this VPC so we are going to need to add an **Internet Gateway** named MyIGW. By default is DETACHED. We have to attach it to a VPC:

<img src="imgs\img20.png" width="500px" />

-  **IMPORTANT!! If I create another IG and try to ATTACH it to the same VPC, WE CAN'T!!! You can ONLY HAVE 1 IG per VPC**

- Now that we have an IG we have to go into the **ROUTE TABLE** and configure our main **routes**. If we go on Routes for Route Tables we will see that what this is doing it's allowing our subnets to **talk to each other**. Any subnet that we have in our VPC using this two routes they can communicate with each other over IPv4 (10.0.0.0/16) and IPv6 (the other register).

<img src="imgs\img21.png" width="500px" />

- In subnet Associations we see subnets that are NOT associated with ANY route table, hence, they are associated with the MAIN route table.

<img src="imgs\img22.png" width="500px" />

- If we were to put a ROUTE OUT to the internet in our MAIN route table, this means that ANY subnet that we were creating, would be by default associated with that MAIN table, hence **every SUBNET will be BY DEFAULT PUBLIC!!!!**. This is a security concern. 

- Route table control de routing for the subnet. A subnet can only be associated with one route table at a time, but multiple subnets can be associated with the same subnet route table. 

-**A good practice, is to KEEP the MAIN Route Table as PRIVATE** and **FOR the PUBLIC SUBNET, add a especial Route table**.  We Create a Route table for the public:

<img src="imgs\img23.png" width="500px" />
<img src="imgs\img24.png" width="500px" />

- We are going to **create a ROUTE OUT to the Internet for the public Route table**:

<img src="imgs\img25.png" width="500px" />
<img src="imgs\img26.png" width="500px" />
<img src="imgs\img27.png" width="500px" />

- We set the destination to 0.0.0.0 and for the target we set our Internet Gateway 

<img src="imgs\img28.png" width="500px" />

- We can do the same for route out for IPv6 addresses. This will have given us a route out for Ipv6 and Ipv4. 
Any subnet associated with this Route Table will be public both for IPv4 and IPv6. 

- Edit Subnet Associations:

<img src="imgs\img29.png" width="500px" />

- So we want to **add this subnet to our public Route table**:

<img src="imgs\img30.png" width="500px" />

- So if we see what subnet is associated with our **Main table** we see this subnet:

<img src="imgs\img31.png" width="500px" />

- This is the subnet that we want to keep as private. And for the public route table one we see the associated subnet that we want to convert as public:

<img src="imgs\img32.png" width="500px" />

- **The next step is to create a EC2 instance in our private subnet and one in our public subnet**.

- We start by launching the EC2 for our **public** subnet:

<img src="imgs\img33.png" width="500px" />

- **Now we have to select SG!!** If we have created a SG in another VPC, that SG will not be visible for this VPC. **Hence, SG DO NOT SPAN OVER VPC!!!**. So we create a new SG. Since we will need to SSH into the instance as well as giving it HTTP access

<img src="imgs\img34.png" width="500px" />

- We launch another instance in the Private Subnet. **Automatically you will see how the field Auto-Assign Public IP gets DISABLED!!!**. So we are not going to get a public IP address for this EC2 instance

<img src="imgs\img35.png" width="500px" />

- In the SG choice for thi private instance I do not select the previously created SG, but I select the default:

<img src="imgs\img36.png" width="500px" />

- So we see that we have an EC2 instance with a public IP address and another one without one:

<img src="imgs\img37.png" width="500px" />

- We SSH the public instance:

<img src="imgs\img38.png" width="500px" />

- That's a summary of what we have just created. Note that here we have only one Network ACL:

<img src="imgs\img39.png" width="500px" />

## Exam tips 
- When creating a VPC by default it enables RT, NACL, SG
- It does not create Subnets nor IG
- US-East-1A in your AWS can be completely different AZ to US-East-1A in another account. This is done by AWS to avoid all users picking AZ as A or B and leaving empty the C. 
- Amazon rserves 5 IP addressess **within your subnets**
- You can only have **1 IG per VPC**
- **SG CAN'T SPAN VPCs**

# Custom VPC - Part 2

- We are going to connect Public Instance in the subnet 1.0 and communicate with the private instance in the subnet 2.0.

- Our private EC2 instance in the private subnet does not have public IP address but it DOES have a PRIVATE IP address:

<img src="imgs\img40.png" width="500px" />

- **If I try to SSH to this private IP from my public EC2 instance is not going to work BECAUSE both INSTANCES remember that have been created with DIFFERENT SECURITY GROUPS!!!!**. And by defaul, SG do not allow access to each other. 

- So we will create a new SG. Since the private EC2 instance is MyDBServer we should probably have a database SG. We will put as Inbound rules All ICMP since I want to be able to ping it. In Source, we can either put the CIDR address range or the SG. We will put the address range. **I want this SG to allow everything in my public subnet to communicate to it**:

<img src="imgs\img41.png" width="500px" />

- Now we add more inboud rules for this SG. We will allow for the Source (our public subnet) HTTP, HTTPs, MySQL and SSH type of connections.

<img src="imgs\img42.png" width="500px" />

- My newly created SG is in my default VPC:

<img src="imgs\img43.png" width="500px" />

- Next thing to do is just go over to EC2 **and CHANGE SG for the PRIVATE EC2 instance and add my newly created SG!!! which will allow such previously mentioned connections from any source in the PUBLIC subnet**. 

<img src="imgs\img44.png" width="500px" />
<img src="imgs\img45.png" width="500px" />

- Take the **private IP address for the private instance**. Now SSH to our public EC2 instance:
- Now we **ping to the private IP address (hence, we are pinning to the private EC2 instance)**:

<img src="imgs\img46.png" width="500px" />

- We can see how we are communicating from my public webserver to my private webserver. 

- Now we have a problem. To SSH from the public EC2 instance to the private one, we need our private key (.pem) to be in the Public Ec2 instance. This is not advisable. Usually what you would do in production is to **use a BASTION HOST**. But now I do it CUTRE, I copy paste the key and create a file with the .pem extension and this key. We  SSH then to the private EC2:

<img src="imgs\img47.png" width="500px" />

- If once I am INSIDE th private EC2 instance, if I try to **do a yum update** for example, is going to do a TIMEOUT fail, as **we are in a PRIVATE SUBNET THAT HAS NO ROUTE OUT to the INTERNET!!!!**. So... how I install software?? **Using NAT Instances**

# NAT Instances & NAT Gateways 

Network Address Translations. It allows Instances in PRIVATE subnets to go OUT on the internet and download software. To do that, they need a way to communicate to our Internet Gateway, **but we don't want to make the subnet public**. 

In 90% of cases we are going to use **NAT Gateways**. NAT instances are individual EC2 instances that do this task, whereas, NAT Gateways are **highly available gateway that allows your private subnets communicate out to the internet without becoming public**, they are not dependent on a single instance as NAT Instances are. 

<img src="imgs\img48.png" width="500px" />

## NAT Instance

- Launch an instance in my Public Subnet

- Select Community AMI for NAT: select the first
<img src="imgs\img49.png" width="500px" />

- Launch that **NAT INSTANCE for the PUBLIC SUBNET**.

<img src="imgs\img50.png" width="500px" />

- That will give me a public IP address for that instance. Select the security group:
<img src="imgs\img51.png" width="500px" />

- **The NAT instance is acting as a bridge between our private subnets, through the public subnets and finally reaching the internet gateway**. According to the documentation, we must disable source and destination checks on the NAT instance.

<img src="imgs\img52.png" width="500px" />

- We need to disable SOURCE and DESTINATION CHECKS:

<img src="imgs\img53.png" width="500px" />

- Now I need to create a **route** to allow my **private EC2 instance (in the privaate subnet) allow to talk to the public EC2 instance (NAT Instance)**.

- We go to VPC > Route Tables :

<img src="imgs\img54.png" width="500px" />

- So if we want to **go out of the internet (0.0.0.0/0) (destination) we have to use as target our NAT Instance**

<img src="imgs\img55.png" width="500px" />

- See in routes that for that route table (the MAIN) we have created an elastic network interface (eni). 

<img src="imgs\img56.png" width="500px" />

- Take the PUBLIC IP ADDRESS of the **NAT Instance** and **SSH**:

- We do a SSH to the private EC2 instance as we did before:

<img src="imgs\img57.png" width="500px" />

- Run a "yum update -y" and **we will see that it starts doing things**. So, it is using a route out of the Internet by using my NAT Instance .

- Once problem is that a **NAT Instance** is just a single machine, so it can get quickly overwhelmed. If we had thousands of EC2 instances in private subnets all trying to download things at once...  This NAT Instance is a massive bottleneck. If we terminate the NAT instance, all the EC2 instances in private subnets connected to the internet through the NAT instance will not be longer getting connection to the Internet. 

- **If I terminate the NAT instance** and go to the Route table:

<img src="imgs\img58.png" width="500px" />

- We see a blackhole status... Cause the instance does not exist. So we go to **Edit routes** and remove this route.

## NAT Gateway

- Go to VPC > NAT Gateway > Create a NAT GAteway. This will be a highly available and scalable solution compared to NAT Instance.

<img src="imgs\img599.png" width="500px" />

- Select the subnet: **CHOOSE THE PUBLIC SUBNET**


<img src="imgs\img59.png" width="500px" />

- We let the system create a Elastic IP Address and then we will attach it to my NAT gateway. 

<img src="imgs\img60.png" width="500px" />

- We need to **edit the route tables to point to this NAT gateway**. Edit the MAIN route table and edit route to add a route out of the internet.

<img src="imgs\img61.png" width="500px" />

<img src="imgs\img62.png" width="500px" />

- NAT Gateways can take some time to get up...

- I go to my private EC2 instance, which I was in throug and SSH from the public EC2 instance. I try to make a command that uses the internet and VOILA, it has connection through the NAT Gateway. 

## Exam tips

- NAT instances are out of date but are in the exam
- When creating NAT instance we need to disable the source and destination check on the instance
- NAT instance must be ina public subnet and there must be a route OUT of the private subnet to that NAT Instance in order for this to work.
- The amount of traffic that the NAT Instance supports depends on the instance size... So if you are bottlenecking, increase the instance SIZE
- You can create high availability using **Autoscaling Groups**, multiple subnets in differents AZ, an script to automate failover...
- Our NAT Instance always lays behind a Security Group

- NAT Gateways are redundant inside the AZ, they can survive the failure of EC2 instances that power NAT gateways. 
- **You can only have 1 NAT Gateway inside 1 AZ**.
- Start with a throughput of 5Gbps and scale currently to 45Gbps. (not quizzed on that)
- No need to patch the OS for your NAT gateways
- No need to associate it with security groups
- Automatically be assigned a public ip address
- Update the route tables for your NAT gateways
- No need to disable Source and Destination checks for NAT Gateways
- **If you have resources in multiple AZ**, and they share ONE NAT gateway... in the event that the NAT gateway's AZ is down, the resources in the other AZ **lose internet access**. To create an **AZ-independent architecture**, create a NAT gateway **in each AZ and configure your routing to ensure that resources use the NAT gateway for the SAME AZ that they are in**. 


# NACL - Network Access Control Lists

- Services > Network > VPC > Security > Network ACLs

<img src="imgs\img63.png" width="500px" />

- We see two ACLs. Both are default. One sits inside our custom VPC (the one created before) default VPC. And we see that it detects how many subnets does it have associated. 

<img src="imgs\img64.png" width="500px" />

- We see that in Inboud Rules there are Rule numbers:
	- 100: for Ipv4
	- 101: for Ipv6
	- If you add another route, add from 101...

- Outbound Rules:

<img src="imgs\img65.png" width="500px" />

- Create a new VPC and select the VPC that is gonna go in. 

<img src="imgs\img66.png" width="500px" />

-  **By default, a new ACLs DENIES everything by default**:

<img src="imgs\img67.png" width="500px" />

- We will test this. We will go to EC2, take the Public IP address of the public EC2 (web server). We SSH to the public ip address. Now I will try to see if the services httpd is running. Since it is not, I install httpd and create a web page:

<img src="imgs\img68.png" width="500px" />

- I go in my browser and see that the webpage is working:

<img src="imgs\img69.png" width="500px" />

- The reason why this is working is because that EC2 instance (public) was associated with the DEFAULT network ACL:

<img src="imgs\img70.png" width="500px" />

- **What we will do is to MOVE our subnet (public) over the network ACL by default so it will only be in the MyWebNACL**. We edit the subnet associations for the newly created ACL:

<img src="imgs\img71.png" width="500px" />

- Associate the subnet with this ACL.  

<img src="imgs\img72.png" width="500px" />
<img src="imgs\img73.png" width="500px" />

- In my defaul subnet we see that the subnet public is no longer associated to that ACL:

<img src="imgs\img74.png" width="500px" />

- If I refresh my webpage is going to fail:

<img src="imgs\img75.png" width="500px" />

- We want to allow rules to be able to see HTTP connections in that subnet.

<img src="imgs\img76.png" width="500px" />

- Add a Rule:
	- 100: allow port 80 for all addresses (0.0.0.0/0)
	- 200: allow HTTPs (Port 443) all addresses
	- 300: allow SSH (port 22) all addresses

- **Rules are evaluated in order by their Rule number (first evaluate 100, after, 200, after, 300)...** and then it denies everything else.

<img src="imgs\img77.png" width="500px" />

- These rules are allowing us to connect to Port 80, 443, 22 but this is configured for **inbound**. We do the same to allow **outbound traffic**:

<img src="imgs\img78.png" width="500px" />

- And here is where **we add our EPHEMERAL PORTS!!!!** 

<Start of section>
### Ephemeral ports

On servers, ephemeral ports may be used as the port assignment on the server end of a communication. This is done to continue communications with a client that inittially connected to one of the server's well-known listening ports (like port 80 or port 22). The allocations are temporary and only valid for the duration of the communication session (Wikipedia). After the communication senssion ,the ports become available for reuse. 

<img src="imgs\img79.png" width="500px" />

- In the official documentation we see that the NAT gateway uses ports 1024-65535. That's why we have chosen this port range. 

<End of section>

- Now we will Edit Inbound Rules and **add a DENY RULE**:

<img src="imgs\img80.png" width="500px" />

- **I will DENY my own IP address**:

- But if I refresh the page, I am able to see the page stil... Why is that? Well, **route 100 here is trumping rule 400 so rule 100 is allowing everything on port 80, is NOT denying my IP address**. So take care of the CHRONOLOGICAL ORDER!!! **IF YOU HAVE A DENY, YOU MUST HAVE THE DENY BEFORE THE ALLOW!!!!**. 

<img src="imgs\img81.png" width="500px" />

- We change it to rule 99 to be before the ALLOW. Now when connecting to my webserver we cannot reach the site. 

## Exam Tips

- **When creating a VPC, a NACL was created by default**, allowing by default ALL inbound and outbound traffic. Every time we add a subnet to OUR VPC is going to be associated with a default NACL. You can then associate the subnet with a new NACL, but a **subnet itself can ONLY be associated with ONE network ACL at any given time**. 

- You can create custom NACL but by default it will **deny all inbound and outbound traffic until you add rules**.

- **NACL can have multiple subnets** on them. I could have MANY subnets associated in a single NACL. 

- Any change in the NACL, **those changes take effect immediately**. 

- Rules are evaluated in numerical order (rule number). DENY rules of specific IP address should go first.

- **NACL are always evaluated BEFORE security groups SG. So if we deny a specific port on your network ACL, it is never even going to reach your SG**. 

- Each subnet in your VPC must be associated with a NACL. If you don't associate a subnet with a ACL, the subnet is going to automatically associate with the default NACL. 

- You can **block specific IP addresses (we cannot did that in SG)**. 

- NACL have separate inbound and outbound rules, and each rule can either allow or deny traffic.

- NACL are **STATELESS**; we have to add both inbound and outbound rules. Responses to allowed inbound traffic are subject to the rules for outbound traffic and viceversa. In SG, since they were STATEFUL, we didn't need to do that. 

# Custom VPCs and Elastic Load Balancers

- EC2 > Load Balancers > Create a Load Balancers
- 3 types of ELB:

<img src="imgs\img82.png" width="500px" />
<img src="imgs\img83.png" width="500px" />

- If I choose an AZ to enable my Load balancer (which route traffic to the targets) in these AZ only, we will see that a warning appears since there is NO IG attached to the selected subnet. This is because this was the **private subnet**. We will **choose the public subnet**. 

<img src="imgs\img84.png" width="500px" />

- But if we want to continue.... BAM, ERROR! At least **2 subnets must be specified**:

<img src="imgs\img85.png" width="500px" />

- **A load balancer NEEDS at least 2 PUBLIC subnets**.

# VPC Flow Logs

- VPC Flow Logs allos to capture information about the **IP traffic going to and from network interfaces in your VPC**. 

- Stored using **Amazon CloudWatch Logs**.

- After you create a flow log, you can view and retrieve its data in Amazon CloudWatch Logs.

- Created at 3 levels :
	- VPC level
	- Subnet level
	- Network Interface Level (ENI - Elastic Network Interface) 

- Go to VPC > Your VPCs > Select our Custom VPC

<img src="imgs\img86.png" width="500px" />

- We have different filters. This is the type of traffic to be logged. So we can filter by only accepted traffic, denied traffic or ALL. The destination can either be a CloudWatch Logs or a S3 bucket. 

- For the destination we will need to go to CloudWatch and create a log group for this:

- Logs > Get Started

<img src="imgs\img87.png" width="500px" />
<img src="imgs\img88.png" width="500px" />

- Go back to VPC > Your VPCs > Create flow log

<img src="imgs\img89.png" width="500px" />

- We have not created an IAM role so we click on Set Up Permissions:

<img src="imgs\img90.png" width="500px" />

- Going back to VPC, we select that IAM role:

<img src="imgs\img91.png" width="500px" />

- We see that is being created in the CloudWatch > Logs:

<img src="imgs\img92.png" width="500px" />

- We see that if we try to access the denied EC2 instance we had before and look at the logs, there will be rejections:

<img src="imgs\img93.png" width="500px" />
<img src="imgs\img94.png" width="500px" />

## Exam Tips

- You cannot enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your accound

- You cannot tag a flow log

- After you create a flow log, you CANNOT change its configuration; for example, you CANNOT associate a different IAM role with the flow log. 

- Not all IP traffic is monitored. Traffic generated by instances when they contact the Amazon DNS server! If you use your OWN DNS server, then all traffic to that DNS server is logged. 

- Traffic generated by a Windows instance for Amazon Windows license activation will NOT be monitored

- Traffic to and from 169.254.169.254 NOT monitored (for instance metadata).

- DHCP traffic is NOT monitored

- Traffic to the reserved IP address for the default VPC router is NOT monitored

# NATs vs. Bastion Hosts

- What is a Bastion Host?

<img src="imgs\img95.png" width="500px" />

- When EC2 instances in the private subnet want to connect to the Internet, they do so by the either NAT Instance or the NAT Gateway.   

- But if we need to SSH (for Linux) or RDP (Windows). The SSH is going through the Internet Gateway, through our Router, Route Tables, NACLs, through our SG and reach our **Bastion server**. Our **Bastion server basically just forwards the connection (either thorugh SSH or RDP) to our private instances**.  

<img src="imgs\img96.png" width="500px" />

- In this scenario we need to **HARDEN** our bastion host, since this is what receives the connection from the outside and maybe is trying to be HACKED. We don't need to care about hardening our instances in our private subnet as long as our Bastion host is hardened.

- So a Bastion is basically that, a way to SSH or RDP into our private instance in your private subnet. 

- You can get **Bastion AMI from the AMI Community**. 


## Exam Tips

- A NAT Gateway or NAT Instance is used to provide internet traffic to EC2 instances in private subnets.
- A Bastion is used to securely administer EC2 instances (called Jump boxes in Australia). .
- **You CANNOT use a NAT Gateway as a Bastion Host**. 


# Direct Connect

- Direct Connect is a cloud service that makes it **easy to establish a DEDICATED network connection from your premises to AWS**. 

- You can establish a private **connectivity between AWS and your datacenter, office,...**, to reduce network cost, increase bandwidth throughput and provide a more consistent network experience than internet based connections. 

- It is done on dedicated lines to connect directly to AWS. 

<img src="imgs\img97.png" width="500px" />

- We have a AWS region and inside we have AWS public services like S3... And we have our VPCs (could be private).
- We have our Direct Connect (DX) Location. These are spread ALL over the world
- Then we have our Customer Environment (this could be our own datacenter) (WAN/MAN/LAN). 

- Inside the Direct Connect Location we have a **AWS Cage** which is dedicated to AWS. Inside that cage we have Direct Connect Routers. Inside that location, where our customer will also have to have their own cage (or you could use a AWS Partner Cage), inside that cage they will be going to have  their own customer routers or partner routers. 

- SO what happens is, we have our router in Customer (WAN/MAN/LAN), in my datacenter, and we are going to have **a dedicated link** (in green) from your datacenter and AWS has its **own backbone network as well** (in gray). From their backbone they are connecting their direct connect routers through a direct connect connection and then we have what's called a cross connect. And this is essentially a network cable that is connecting our Direct Connect to our customer or our partner routers. And then we have our own dedicated link, so this is our last mile extension so this could be a NPLs circuit or dedicated line, etc...

- We run a connection from own datacentr through our Customer or Partner Routers. That router cross connects to the Direct Connect Routers and then that has a dedicated connection to the AWS public service and a dedicated connection to our VPC. 

## Exam Tips

- Direct Connect directly connects your datacenter to AWS
- Useful for High Throughput workloads (high network traffic)
- If we need a stable and reliable secure connection.
- If we have a scenario where we have a VPN connection that keeps dropping out because of the amount of throughput and what kinds of things can be done to solve, that will be answered by using DIRECT CONNECT.  
## Set Up Direct Connect

**IMPORTANT! This steps should be memorized!!!**

1) Create a Virtual Interface in the Direct Connect Console. This is a **Public Virtual Interface**
<img src="imgs\img98.png" width="500px" />
<img src="imgs\img99.png" width="500px" />
<img src="imgs\img100.png" width="500px" />
2) Go to VPC console and to VPN connections and create a **Customer Gateway**.
<img src="imgs\img101.png" width="500px" />
3) Create a **Virtual Private Gateway**.
<img src="imgs\img102.png" width="500px" />
4) Attach the **Virtual Private Gateway** to the desired **VPC**
<img src="imgs\img103.png" width="500px" />
5) Select VPN Connections and **create new VPN Connection**
6) Select the **Virtual Private Gateway** and the **Customer Gateway** (step 2).
<img src="imgs\img104.png" width="500px" />
7) Once the VPN is available, setup the VPN on the customer gateway or firewall. 

# Global Accelerator

- Service to create accelerators to improve availability and performance of your applications for local and global users.
- Directs traffic to the **optimal endpoints** over the AWS global network. This improves availability and performance of you internet apps that are used by a global audience. 

- Global Accelerator gives 2 **static IP addresses that you associate with your accelerator**.


<img src="imgs\img105.png" width="500px" />

- Users traffic is sent to AWS Global Accelerator
- It's going by an Edge Location, so the user traffic enters the AWS global network at the closest Edge Location
- Global Accelerator directs traffic to the **closest endpoint** based on proximity to the client and endpoint weights.
- The user's traffic traverses the AWS global network to the optimal endpoint group in an AWS Region. Each endpoint group includes one or more application endpoints. 
- The Endpoints can be application Load Balancers, EC2 instances, ... 

<img src="imgs\img106.png" width="500px" />

- Without AWS Global accelerator we are relying on a lot of providers, it takes many networks to reach the application. Each hop introduces delay... Whereas in the Global Accelerator, you are using AWS backbone Network thanks to the Edge Location.

## Components

### Static IP addresses
- Global Acc provides you with 2 statip IP addresses that you associate with your accelerator. Or bring you own IP.

###  Accelerator
- Directs traffic to optimal endpoints over the AWS global network to improve availab and performance of you app.
- Each accelerator contains one or many listeners.

###  DNS Name
- When you create a Global Acclerator it will assign each accelerator a default DNS name (XXXXXX.awsglobalaccelerator.com). This DNS points to the **static IP addresses that Global Accelerator assigns to you**.

###  Network Zone
- It services the static IP addresses for your accelerator from a unique IP subnet. It is similar to an AZ, it is a isolated unit with its own set of physical infraestructure.

- Global Accelerator allocates 2 IPv4 addresses for the accelerator. If 1 IP address from one network zone becomes NOT available due to IP address blocking or network disruptions, client applications canretry on the healthy static IP address from **another isolated network zone**. 

### Listener
- It processes inbound connections from clients TO Global Acceletor, based on the port and protocol that you configure.
- GlobAcc supports TCP and UDP protocols.

- Each listener has one or more endpoint groups associated with, so traffic is forwarded to endpoints in one of the groups. 
- You associate endpoint groups with listeners by specifying the regions that you want to distribute traffic to.
- Traffic is distributed to optimal endpoints within the endpoint groups associated with a listener. 

### Endpoint Group
- Each endpoint group is associated with a specific AWS Region
- Endpoint groups include on or more endpoints in the Region
- You can increase or reduce % of traffic that woud be otherwise directed to an endpoint group by adjusting a setting called **traffic dial**. 
- **Traffic dial** allows you to do performance testing or blue/green deployment testing for new releases across different AWS Regions. 

### Endpoint
- Can be Network Load Balancers, Application Load Balancers, EC2, Elastic IP addresses...
- An Application Load Balancer endpoint can be an internet-facing or internal. Traffic is routed to endpoints based on configuration options that you choose, such as **endpoint weights**. This numbers are the proportion of traffic to route to each one. This can be done to do performance testing within a Region. 


## Global Accelerator Lab

- Create a Endpont: in this case a EC2 instance: t2.micro, all by default.
- This EC2 is where the Global accelerator is going to send all the traffic to.

> Networking > Global Accelerator > Crete accelerator (IPv4)

- Configure the listeners: we want to listen to port 80 and port 443 (http and https). 
- Protocol: TCP
- Client affinity: by default GlobAcc distributes traffic equally between the endpoints in the endpoint groups for the listener, bu if you have stateful applications you can direct all requests from a user at a specific IP address to the same endpoint resource to maintain client affinity. Leave as None

<img src="imgs\img107.png" width="500px" />

- Endpoint groups: when we deploy the EC2 instance we did on us-east-1, so select that region. 

<img src="imgs\img108.png" width="500px" />

<img src="imgs\img109.png" width="500px" />

<img src="imgs\img110.png" width="500px" />

<img src="imgs\img111.png" width="500px" />


- We are assigned 2 IP addresses ans well as a DNS name.

<img src="imgs\img112.png" width="500px" />

## Exam tips

- AWS GlobAcc is a service in which you create accelerators to improve availability and performance of you applications for local and global users.
- You are assigned 2 static IP addresses.
- You can control traffic using traffic dials. This is done within the endpoint group. And you can control traffic weights to individual endpoints. 


# VPC Endpoints

- Enables to traverse your traffic without having to leave the Amazon Network.
- It enables to privately connect your VPC to AWS services and VPC endpoint services powered by PrivateLink. It does not require an Internet Gateway, NAT device, VPN connection, AWS Direct Connect connections...
- Instances in your VPC do NOT require a **public IP address to communicate with resources in the service**. Traffic between your VPC and the ther service does NOT leave the Amazon AWS network. 
- Endpoints are virtual devices and horizontally scaled and highly available VPC components that allow communication between instances in your VPC and service without **imposing availability risks or bandwidth constraints on your network traffic**. 

## Two types

- VPC Endpoints
- Gateway Endpoints 

**An interface endpoint is an elastic network interface with a private IP address that serves as an entry point fro traffic destined to a service.** 

- Gateway Endpoints look like a NAT Gateway and **are supported for S3 and DynamoDB**. 
- Right now, for our private EC2 instance in our VPC environment, will need to use the NAT Gateway in the Public Instance to communicate to the S3 bucket.

<img src="imgs\img113.png" width="500px" />

- Instead, our private instance will send files to the VPC Gateway and that gateway is going to send the file to the S3 bucket

<img src="imgs\img114.png" width="500px" />

## Lab

> Security Identity and Complicance > IAM > Roles > Create Role > EC2

<img src="imgs\img115.png" width="500px" />
<img src="imgs\img116.png" width="500px" />

- Once created the role that helps me to communicate to S3 bucket, we are going to go to EC2.
- We will add this role to our instance that is in the private subnet.

<img src="imgs\img117.png" width="500px" />

<img src="imgs\img118.png" width="500px" />

- He realizes that he has to change somethin in the Network ACLS:
- Subnet associations for the default VPC:

<img src="imgs\img119.png" width="500px" />

- Edit > Add the subnet 10.0.1.0.
<img src="imgs\img120.png" width="500px" />

- SSH into the EC2 instance. Remember that to SSH to the private instance we need to SSH the public one and from the public one SSH the private one (IP private). Remember that the public EC2 instance needs to have the .pem key inside to ssh into the private instance.
<img src="imgs\img121.png" width="500px" />

- List the fils in AWS: 
```bash
aws s3 ls
echo "Hola" > test.txt
aws S3 cp test.txt s3://music-emotions
```

- Now go to Route tables, and grab the main route table.  Here we see that we have the route out to the NAT gateway:

<img src="imgs\img122.png" width="500px" />

- Delete that route:

<img src="imgs\img123.png" width="500px" />

- Now we have NO route out to our NAT gateway. 

<img src="imgs\img124.png" width="500px" />

- Back in the private EC2 instance, if we do
```bash
aws s3 ls
```
It will get stucked since **we have removed the way OUT of the internet**. 

- So **now is the MOMENT IN WHICH WE ADD our VPC ENDPOINT**. 

<img src="imgs\img125.png" width="500px" />

- Configure the VPC and the Route table. We will do it to my main route table:

<img src="imgs\img126.png" width="500px" />

<img src="imgs\img127.png" width="500px" />

<img src="imgs\img128.png" width="500px" />

- Go to Route Tables and after some minutes you will see in the destination the endpoint available. 

<img src="imgs\img129.png" width="500px" />

- Go to EC2 instance and if the s3 ls command hangs, try specifying the region:
```bash
aws s3 ls --region us-east-2
```

<img src="imgs\img130.png" width="500px" />

# Summary

- Remember that SG are stateful, so we only need to open up a port and we didn't need to worry about the outbound traffic. With NACLs we have to do both inbound and outbound.
- You cannot do transitive peering with VPC. They need to be peered in a 1-to-1 basis.
- With a VPC creation, it creates a default RT,NACL and SG. 
- It does NOT create any subnets nor IG.
- The AZ are randomised between AWS account.
- 5 IP addresses are reserved within your subnets
- You can only have 1 IG per VPC. 
- SG can NOT span VPCs.
---------------------------------------------------
- NAT Instance, you always disable source/destination check on the instance
- NAT instance must be ina public subnet
- There mut be a route OUT of the private subnet to the NAT instance
- The amount of traffic that NAT instances can support depends on the instance size. If there is bottlenecking, increase the instance size. 
- You can create HA using AutoScaling groups, multiple subnets in different AZ and a script to automate failover. 
- NAT instances are always behind a SG. 
---------------------------------------------------
- NAT Gateway:
 
<img src="imgs\img131.png" width="500px" />

- Remember that in a NAT Gateway we are NOT behind a SG. 


- Redundant inside the AZ
- Starts at 5 GBps and scales currently to 45GBps
- Not associated with SG
- Automatically assigned a PUBLIC IP Address
- Remember to update your route tables
- No need to disable Source/Destination Checks
- If you have resources in multiple AZ and they share 1 NAT gateway, if the NAT gateway's AZ is DOWN, resource in the other AZ lose internet access. To create an AZ independent architecture create a NAT gateway in each AZ and cofigure your routing to ensure that resources use the NAT gateway in the **same AZ**. 

---------------------------------------------------

- Network ACL
- Your VPC automatically comes with a default network ACL and by default allows ALL out/in trafic
- You can create custom network ACL but by default each custom network DENIES ALL in/out traffic until you add rules
- Each subnet in your VPC must be associated with a NETWORK ACL. If you don't explicitly associate a subnet with a NACL, the subnet is automaticall associated with the default NACL. 
- Block IP addresses on specific ports (this cannot be done using SG). 
- You can associate network ACL with multiple subnets, however, ha subnet can be associated with ONLY one NACL. When you associate a NACL with a subnet, the previous association is removed.
- NACLs contain a numbered list of rules. **This rules are evaluated in ORDER, starting with the lowest numbered rule**. Remember, TO DENY something, you MUST PUT IF BEFORE (number of evaluation smaller) than the ALLOW rule. 
- NACL have separate IN/OUTbound rules, and each rule can either allow or deny traffic. 
- NACL are STATELESS, responses to allowed inbound traffic are **subject to rules for outbound traffic**. This does not occur in SG, in which when your instance is listening on port 80 and you allow inbound connections to that port on your SG, you can access it even though you have not explicitly allowed the OUTBOUND communication of the EC2 instance to the client. 

---------------------------------------------------

- ELB: you need a minimum of 2 public subnets to deploy an internet facing loadblanacer

---------------------------------------------------

- FlowLogs: you cannot enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your account.
- Tag VPC flow logs
- After you create a flog low, you CANNOT change the configuration: you can't associate a different IAM role. 
- Not all the IP traffic is monitored: traffic generated by instances when they contact the AWS DNS. If you use your own DNS server, then ALL traffic to that DNS server is logged.
- Traffic generated by a Windows instance for Amazon Windws license activation is not going to be monitored.
- Traffic to and from 169.254.169.254 is not going to be monitored.
- DHCP traffic not monitored
- Traffic to the reserved IP address for the default VPC router will not be monitored.

---------------------------------------------------

- Bastions: 

<img src="imgs\img132.png" width="500px" />

- NAT Gateway is used to provide internet acces to EC2 isntances in private subnets.
- Bastion is used to securely administer EC2 instances. 
- You cannot use a NAT Gateway as a bastion host.

---------------------------------------------------

- Direct connect:

- Connects the Datacenter to AWS and it's useful for highthroughput workloads or when you need stable and reliable secure connection.
- Remember the steps of configuring Direct Connect. 

---------------------------------------------------

- Global Acc: service that imprve availability and performance of you apps for local and global users. 
- Your user is going to the AWS Edge Location and it's traversing the AWS own internal backbone network to get to your AWS service (endpoint). 
- Are assigned 2 IP static addresses.
- Can tcontrol traffic using the traffic dials. This is done within the ENDPOINT GROUP.
- Control weighting to individual endpoints using weights. 

---------------------------------------------------

- VPC Endpoints.
- Privately connect your VPC to supported AWS services. 
- Instances in your VPC do NOT require public IP addresses to communicate with resources in the service (remember that we used the private EC2 instance to get the contents of S3 bucket and upload a file). 
- Interface Endpoints and Gateway endpoints (S3 and Dynamo DB).

<img src="imgs\img133.png" width="500px" />

-
<img src="imgs\img134.png" width="500px" />

-
<img src="imgs\img135.png" width="500px" />

-
<img src="imgs\img136.png" width="500px" />

-
<img src="imgs\img137.png" width="500px" />

-
<img src="imgs\img138.png" width="500px" />

-
<img src="imgs\img139.png" width="500px" />

-
<img src="imgs\img140.png" width="500px" />

-
<img src="imgs\img141.png" width="500px" />

-
<img src="imgs\img142.png" width="500px" />

-
<img src="imgs\img143.png" width="500px" />

-
<img src="imgs\img144.png" width="500px" />

-
<img src="imgs\img145.png" width="500px" />

-
<img src="imgs\img146.png" width="500px" />

-
<img src="imgs\img147.png" width="500px" />

-
<img src="imgs\img148.png" width="500px" />

-
<img src="imgs\img149.png" width="500px" />

-


´´´python
for ii in range(0,100):
	print(f'<img src="imgs\img{ii}.png" width="500px" />')
	print('')
	print('- ')
	print('')
´´´ 

