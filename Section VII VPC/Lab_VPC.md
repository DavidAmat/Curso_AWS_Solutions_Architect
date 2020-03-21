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


<img src="imgs\img49.png" width="500px" />
<img src="imgs\img50.png" width="500px" />
<img src="imgs\img51.png" width="500px" />
<img src="imgs\img52.png" width="500px" />
<img src="imgs\img53.png" width="500px" />
<img src="imgs\img54.png" width="500px" />
<img src="imgs\img55.png" width="500px" />
<img src="imgs\img56.png" width="500px" />
<img src="imgs\img57.png" width="500px" />
<img src="imgs\img58.png" width="500px" />
<img src="imgs\img59.png" width="500px" />
<img src="imgs\img60.png" width="500px" />
<img src="imgs\img61.png" width="500px" />


```bash

```

<img src="imgs\img62.png" width="500px" />
<img src="imgs\img63.png" width="500px" />
<img src="imgs\img64.png" width="500px" />
<img src="imgs\img65.png" width="500px" />
<img src="imgs\img66.png" width="500px" />
<img src="imgs\img67.png" width="500px" />
<img src="imgs\img68.png" width="500px" />
<img src="imgs\img69.png" width="500px" />
<img src="imgs\img70.png" width="500px" />
<img src="imgs\img71.png" width="500px" />
<img src="imgs\img72.png" width="500px" />
<img src="imgs\img73.png" width="500px" />
<img src="imgs\img74.png" width="500px" />
<img src="imgs\img75.png" width="500px" />
<img src="imgs\img76.png" width="500px" />
<img src="imgs\img77.png" width="500px" />
<img src="imgs\img78.png" width="500px" />
<img src="imgs\img79.png" width="500px" />
<img src="imgs\img80.png" width="500px" />
<img src="imgs\img81.png" width="500px" />
<img src="imgs\img82.png" width="500px" />
<img src="imgs\img83.png" width="500px" />
<img src="imgs\img84.png" width="500px" />
<img src="imgs\img85.png" width="500px" />
<img src="imgs\img86.png" width="500px" />
<img src="imgs\img87.png" width="500px" />
<img src="imgs\img88.png" width="500px" />
<img src="imgs\img89.png" width="500px" />
<img src="imgs\img90.png" width="500px" />
<img src="imgs\img91.png" width="500px" />
<img src="imgs\img92.png" width="500px" />
<img src="imgs\img93.png" width="500px" />
<img src="imgs\img94.png" width="500px" />
<img src="imgs\img95.png" width="500px" />
<img src="imgs\img96.png" width="500px" />
<img src="imgs\img97.png" width="500px" />
<img src="imgs\img98.png" width="500px" />
<img src="imgs\img99.png" width="500px" />


´´´python
for ii in range(0,100):
	print(f'<img src="imgs\img{ii}.png" width="500px" />')
´´´ 

