# Load Balancers

- Phyisical or virtual device that allows you to balance the network load across multiple servers. 
- You can use it with applications, it odes not have to necessarily be Internet-facing apps.


## Application Load Balancer

-  Load balancing of HTTP and HTTPs traffic.  They operate at Layer 7 and are **application-aware** (when we change for example the language of a website we go to the server of that language). 
- You can create advanced request routing, sending specified requests to specific web servers. 

## Network Load Balancers

- Load balancing of TCP traffic where extreme performance is required. 
- It operates at the conenction level (layer 4). 
- Handles **millions of requests per second, while maintaining ultra-low latencies!!!**
- EXTREME PERFORMANCE!

## Classic Load Balancer

- Are the legacy Elastic Load Balancers. You can load balance HTTP/HTTPS applications and use layer7-specific features: X_Forwarded and sticky sessions. 
- Use stric Layer 4 load balancing for applications that rely purely on the TCP protocol. 

- If 504 error (the application stops responding). This could be either at the WebServer layer or at the Database Layer, this is not that the CLB is having the issue. You need to trouble-shoot the application. 

### X-Forwarded for Header

- ELB has an internal IP address of 10.0.0.23
- The User comes with his IP, goes to the ELB 
- This is being passed to the EC2 instance, which is logging the load balancer's IP address as our user's IP address. This can be annyoing since you may want to know where the users are coming from... So, how to **know the IP address of the users that are directed with the ELB to the application?**. 
- Using X-Forwared-For Header we can know that.

<img src="imgs\img1.png" width="500px" />

# Load Balancers - Lab 

> EC2 > Launch instance

- We are going to launch 2 instances in 2 different AZ. 
- Create a web server on each instance using that bootstrap:

```bash
#!/bin/bash
yum update -y
yum install httpd -y
service httpd start
chkconfig httpd on
cd /var/www/html
echo "<html><h1>This is WebServer 01</h1></html>" > index.html
```

- Tag this as Web01.
- Put the instance in a specific subnet: eu-west-1a (specific AZ).
- SG: use the WebDMZ

- Do the same with another instance:

```bash
#!/bin/bash
yum update -y
yum install httpd -y
service httpd start
chkconfig httpd on
cd /var/www/html
echo "<html><h1>This is WebServer 02</h1></html>" > index.html
```

- Make sure that this goes into a separate AZ: eu-west-1b
- Use the tag Web02
- Use the WebDMZ SG

<img src="imgs\img3.png" width="500px" />

- Create the Load Balancer.
- **We are using a Classic Load Balancer**. Network are expensive...
- Call it "myClassicELB". 
- You can have Load Balancers inside private subnets and also select which subnets the ELB will be deployed into. You should always have a minimum of 2.

<img src="imgs\img4.png" width="500px" />

- Use the WebDMZ SG
- COnfigure the Health Check:

<img src="imgs\img5.png" width="500px" />

- Add our 2 EC2 instances to the Load Balancer. 

<img src="imgs\img6.png" width="500px" />

- Note that we are **never given a IP address for an ELB, we are always given a DNS**. 
- Copy that DNS, and before make sure that the instances are InService (when recently launched, the Health Check will do some checks to both instances to see if they past the healt check). 

<img src="imgs\img7.png" width="500px" />

- Once they are InService:

<img src="imgs\img8.png" width="500px" />

- Navigate to the DNS of the Elastic Load Balancer.
**See that sometimes we hit Server 01 and other times to 2:

<img src="imgs\img9.png" width="500px" />
<img src="imgs\img10.png" width="500px" />

- Shutdown WebServer 1 (stop the first instance). **This will stop EC2 isntance 1 serving traffic to my Load Balancer. My Load Balancer is going to detect that using the health check, and it is going to mark the instance as unhealthy and take it out of the **load balancing pool**. 

- When refreshing continuously we will realize that we only hit WEb Server 02. 

<img src="imgs\img11.png" width="500px" />

- We restart the EC2 instance stopped.
- Go to **Target Group**.
- A Target Group is basically where you load balancer is going to request to the targets in that target group. 
- The load balancer routes requests to the targets in a target group using the target group settings that you specify. You may have a group of instances for the European customers, a group for the USA customers... you want the balancer to divide traffic but taking into account in which group the EC2 instances are. 

<img src="imgs\img12.png" width="500px" />

- Add our instance to the target group:

<img src="imgs\img13.png" width="500px" />
<img src="imgs\img14.png" width="500px" />

- Now create the Load Balancer. Now we will select a **Application Load Balancer**. 

<img src="imgs\img15.png" width="500px" />
<img src="imgs\img16.png" width="500px" />

-  Put into the WebDMZ SG
- COnfigure the routing. Use the existing Target group:


<img src="imgs\img17.png" width="500px" />

- Go to Target Groups to see the Targets section and Add registered targets:

<img src="imgs\img18.png" width="500px" />
<img src="imgs\img19.png" width="500px" />

- Wait the Status till it gets HEALTHY. So that the instances will be under the Application Load Balancer. 

<img src="imgs\img20.png" width="500px" />

- Edit Rules and create rules.. This is beyond the scope... 
- **So basically for the test, remember that for more intelligent actions like intelligent routing (with conditions and rules) use Application Load Balancer instead of Classical Load Balancers. 
- Use Network Load Balancers if you need a fixed IP address for your load balancer or need ultrahigh performance
- Use Classic Load Balancers if you need really simple routing and NOT any intelligent routing and keep the cost DOWN.

- Copy the DNS of the balancer and see that it also works. 
<img src="imgs\img21.png" width="500px" />

## Exam tips
<img src="imgs\img2.png" width="500px" />

  
# Advanced Load Balancers Theory

## Sticky Sessions

- Sticky sessions route all the traffic of a SPECIFIC user to a SPECIFI EC2 instance. This ensures that all the requests of that user are handled by the same instance. This is true for **Classic Load Balancers**.

- For **Application Load Balancers** the traffic is sent at the TARGET GROUP LEVEL not at the user level.

- This is useful if the application writes a file in **local disk** (of the instance) so, you need the same instance for the same user.
- **In the exam**, if you have a Classic Load Balancer and ONE of the instance is NOT receiving any traffic from a user, **then you need to disable sticky sessions**.

## Cross Zone Load Balancing

- In a scenario without CZLB, we have a user that uses Route 53 that routes 50/50 traffic to 2 AZ (A, B).
- In each AZ we have a CLB. If in A we have 4 instance and in B we have 1... In the A, the 50% of traffic will be split across 4 instances but in B all the 50% will be sent to 1 instance...
- The reason is that the Elastic Load Balancers cannot sent traffic to another AZ!!!! That happens if we have NO CZLB enabled

<img src="imgs\img22.png" width="500px" />

- By enabling it,

<img src="imgs\img23.png" width="500px" />

- Typical Exam scenario: you have instances in zone A that are splitting the traffic but you have a instance in AZ B that is not receiving any traffic. Then you should enable CZLB. 

<img src="imgs\img24.png" width="500px" />

## Path Patterns

- Creates a listener that has rules that will forward requests based on the URL path. 
- This is know as **path-based routing**.
- In micro-services, you can route traffic to multiple back-end services using path-based routing. 
- For example: route general requeetsts to oen target group and requests to render images to anoterh Target group.

- Typical Exam scenario: you need to route the requests of the path myurl.com to the AZ A but the ones that requests for the /images section route them to the AZ B. That needs the Path patterns

<img src="imgs\img25.png" width="500px" />

# Autoscaling theory

## Components

- Groups: logical component: webserver, application or database group... Is to what we assign the EC2 instances. 
- Configuration Templates: each group always uses a launch template  as a configuration ftemplate for its EC2 instances. These are the instructions of what EC2 instances to launch, what size they are, how much storage... Instance type, key pair, SG, block device mapping...
- Autoscaling options: configure a group to scale based on XXXX. 

## Scaling Options

### Maintain current instance levels at all times
- Maintain a specific number of running instances. I want a minimum of X instances all the time. Periodic health checks are running within the Auto Scaling group. When it finds an unhealthy instance is going to terminate that instance and launch a new one.

### Scale Manually
- Most basic way. You specify the change in the maximum/minimum/capacity of you AutoScaling group.
- You specify manually the number of instances to maintain the updated capacity. 

### Scale based on Scheduling
- Scaling actions are produced as a function of date and time. 
- Useful for you know when you need to increase based on an hour

### Scale based on demand
- Is the most common, uses **scaling policies** that yu define parameters that control the scaling process.
- For example: an app that runs on 2 instances, you want the CPU utilization of the AutoScaling group to stay at around 50% when the load on the app changes. This method is useful for scaling in response to changing conditions, when you DON'T KNOW WHEN those conditions will change.

### Predictive scaling
- Use EC2 autoscaling in combination with AWS Auto Scaling. 
- Helps to mantain optimal availability. Predicts based on your previous performance. 

## Lab

- To create an AutoScaling Group we need first an AutoScaling configuration:

<img src="imgs\img26.png" width="100px" />

<img src="imgs\img27.png" width="500px" />

- Choose the instance type and configuration (bootstrap script):

<img src="imgs\img28.png" width="500px" />

-  Select the WebDMZ SG
- Create the Launch Configuration. **Doing this does not DO ANYTHING, it is just CONFIGURATION!!!!**. 
- Create an AutoScaling group using this launch configuration:

<img src="imgs\img29.png" width="500px" />

- We will start running 3. It is intelligent not to put all of them in the same subnet. 

<img src="imgs\img30.png" width="500px" />

- Start configuring policies to scale out: as soon as it gets to 80% CPU, add an instance from 3 up to 6 instances:

<img src="imgs\img31.png" width="500px" />

- We can add an SNS notification when the autoscaling group adds an instance but we are not going to do that.

- I check that my instances are up and running (started with 3):

<img src="imgs\img32.png" width="500px" />

- Let's simulate a failover. Let's delete 12 of these instances in AZ A and C.
- We can go to the AutoScaling > Activity History and see that it has triggered the launch of an ew instance ater the termination of the instance.

<img src="imgs\img33.png" width="500px" />

- After some time, we will get our 3rd instance also launching. **Note that IT HAS PUT THOSE NEW INSTANCES BACK in the SAME AZ in which the previous ones where!!**. 

<img src="imgs\img34.png" width="500px" />

- **When you delete an AUTOSCALING GROUP, ALL THE INSTANCES running on top of that group WILL BE AUTOMATICALLY DELETED as well**.

# HA Architecture

- Plan for failure.
- Use Multiple AZ and Multiple Regions where ever you can.
- Know the difference between Multi-AZ (disaster recovery) and Read Replicas  (performance) for RDS. 
- A well known architecture to be HA in both AZ and Region level is:

<img src="imgs\img35.png" width="500px" />

- Having a user that requests this website and Route53 routes traffic to one region or another (if one region fails, directs to another). Thanks to the Health check, Route53 knows if the region is healthy or not.
-  Inside the region you have 2 AZ, each with each own subnets, public subnets and private ones, we have our web servers in two different public subnets. Databases are in two different private subnets and NAT Gateways in the public subnets and the Autoscaling group. **The DBs are doing some kind of replication such as RDS Multi AZ deployment or 2 EC2 instances that you setup to do synchronous replication**. 

<img src="imgs\img36.png" width="500px" />

- Exam Scenario: You have a website that requires a min. of 6 instances and it must be HA. You must be able to tolerate the failure of 1 AZ. What is the ideal architecutre of this env while also being the most cost effective? 
- Answer: 3 AZ with 3 instances in each AZ, so that if 1 fails, there are 2 AZ with a total of 6 instances.

<img src="imgs\img37.png" width="500px" />

- Difference between scaling up (increase the resources inside the EC2 instances, upgrade the t2.micro to t2.large)  and scaling out (using AUTOSCALING GROUPS, adding additional EC2 instances).

- Know the different S3 storage classes:
    - Standard S3 and Standard S3 Infrequently Access are HA.
    - Reduced Redundancy Storage and S3 Single AZ are NOT HA.


## Lab: Building a Fault Tolerant Wordpress Site

- Got our user, they will browse to the Internet, hit Route 53 domain name (this is optional, you could use the application Load Balancer DNS).
- Users are connected to the ELB in the cloud, we are going to have 2 EC2 instances behind an AutoScaling Group (ASG) that are going to be on separate AZ. They are going to have their respective RDS instances (with a Multi-AZ setup). Will have 2 S3 buckets, one with Media Assets for our WordPress site and the other will have the code for Wordpress. 
- We are going to serve out all our pictures from our WordPress through CloudFront.
- We will simulate the failure of 1 EC2 instance as well as an RDS instance to see how this affects. 

### 1. Launch S3 buckets

- Create one for the code
- One for the pictures (media)

<img src="imgs\img38.png" width="500px" />

### 2. CloudFront
- Create a CloudFront dstribution and Create a Distribution using a Web Distribution:

    - Select the S3 of the media bucket
    - Everything else as default.

<img src="imgs\img39.png" width="500px" />


### 3. VPC - Security Groups

- **Security groups**: create a WebDMZ

<img src="imgs\img40.png" width="500px" />

- Configure a SG for the RDS instances:

<img src="imgs\img41.png" width="500px" />

- Make sure the port for the mysql is open **for the SECURITY GORUP of WebDMZ (sg-0644...2b8). This is crucial to allow the EC2 instance to talk to the RDS instances that will be using that SG:

### 4. Launch RDS instances

- RDS > Create Database > MySQL
-

<img src="imgs\img42.png" width="500px" />
<img src="imgs\img43.png" width="500px" />
<img src="imgs\img44.png" width="500px" />

- Create a stand-by instance for MultiAZ deployment

<img src="imgs\img45.png" width="500px" />

- Use the default VPC:

<img src="imgs\img46.png" width="500px" />

- Use the SG that you have created for RDS:

<img src="imgs\img47.png" width="500px" />

- IMPORTANT!! Name the Database!!!

<img src="imgs\img48.png" width="500px" />

- Disable Enchanced monitoring. Create the DB!

### 5. IAM

- Create Role > EC2 > Full Access to S3
- This role will allow our EC2 instances to speak to the S3 bucket:
- Named it: S3ForWP

<img src="imgs\img49.png" width="500px" />

### 6. EC2 instances

- Provision our EC2 instances.
- Assign the previously created Role for S3ForWP:

<img src="imgs\img50.png" width="500px" />

- Bootstrap script to install mysql and wordpress:

```bash
#!/bin/bash
yum update -y
yum install httpd php php-mysql -y
cd /var/www/html
echo "healthy" > healthy.html # THIS WILL BE CHECKED in the 7.3 SECTION!!!!
wget https://wordpress.org/wordpress-5.1.1.tar.gz
tar -xzf wordpress-5.1.1.tar.gz
cp -r wordpress/* /var/www/html/
rm -rf wordpress
rm -rf wordpress-5.1.1.tar.gz
chmod -R 755 wp-content
chown -R apache:apache wp-content

# HT ACESS!!! We will explain that LATER!!
wget https://s3.amazonaws.com/bucketforwordpresslab-donotdelete/htaccess.txt


mv htaccess.txt .htaccess
# rename it so that it is a hidden file. This HTACCESS will allow us
# to do the URL re-write, that is, to allow us to serve content out of cloudfront 
# rather than doing it out of S3 buckets


chkconfig httpd on
service httpd start
```
- Give a tag:

<img src="imgs\img51.png" width="500px" />

- Configure SG:

<img src="imgs\img52.png" width="500px" />

- Launch the instance. 

## 7. Configure

- Install Wordpress on EC2 instance
- Create a post and inside that post and add 2 new images
- Add some resiliency to see wheter those images are save on our EC2 instance and look at a way of backing that up to S3 (first do it manually but then use Chrone to do it automatically).
- Put the EC2 instance behind an ALB (Application Load Balancer) and connect our ALB up to Route53.

### 7.1 Install Wordpress

- Go to EC2 and SSH into it. 

```bash
cd /var/www/html
ls #check that files are present

#make sure the htaccess file is there
cat .htaccess

#check that the httpd is running
service httpd status
```

- Go to WebBrowser and navigate to the IP of that EC2 isntance
- **Configure the Database** (remember the database name in the RDS instance). For that we need to go to RDS > Instances and get the Endpoint of the RDS instance

<img src="imgs\img53.png" width="500px" />

- Create the configuration using these details:

<img src="imgs\img54.png" width="500px" />

- It will give this code that will be need to be copied in the clipboard:

<img src="imgs\img55.png" width="500px" />

- And paste it our EC2 instance:

```bash
nano wp-config.php
#PASTE IT HERE!
```

- Hit run the installation!

<img src="imgs\img56.png" width="500px" />

- Install WordPress after configuring it:

<img src="imgs\img57.png" width="500px" />

- We Login In Wordpress:

<img src="imgs\img58.png" width="500px" />

### 7.2 Create a first post

- Create post

<img src="imgs\img59.png" width="500px" />

- Upload an image and hit Publish

<img src="imgs\img60.png" width="500px" />

- View Post you will se the post.

- Go to the EC2 instance now (SSH):

```bash
cd wp-content
cd uploads
cd 2019
cd 02 #february
ls
# img1.jpg img2.jpg
```

- **Let's ADD MORE RESILIENCE**. 
-  Go to html directory:
```bash
cd wp-content
cd uploads
cd 2019
cd 02 #february
ls
# img1.jpg img2.jpg
```
### 7.3 Back Up to S3

- What we want is that each time someone uploads a file in WordPress this file is stored in S3 for redundancy

- We are going to FORCE CloudFront to serve those files using the CloudFront distribution rather than using the images INSIDE the EC2 instance because then the side will load a bit faster. 

- Make sure that the S3 role is working:

```bash
aws s3 ls
```

- Upload manually those files in these directories directly to the S3 bucket:

<img src="imgs\img61.png" width="500px" />

- Add redundancy, since we can lose this EC2 instance. So we are going to do a full copy of our website in our S3 bucket. 
- So in our **second S3 (the one done for code)** we are going to put this code (copy all the folders in the html folder and subfolders -- recursive!!):

<img src="imgs\img62.png" width="500px" />

- We see that there is a file called healty.html that it basically says healthy. This file was **created in the bootstrap script of the instance**.
- We can use the Elastic Load Balancers to do the Health Check of that file as well.

- Another file that it is worth noting is the  .htaccess:
- Here what we have is a **URL re-write rule**. This basically allows the website to, instead of serving the images out of EC2, it is going to serve the images out of CloudFront!!!!

- This is telling us: rewrite the url wp-content/uploads/.*  to this new URL (this is the new URL of the CloudFront distribution). 
- We have to get the **URL of the CloudFront distribution!!!!** 

<img src="imgs\img63.png" width="500px" />

- Replace the URL that was by default with the URL from CloudFront distrib:

<img src="imgs\img64.png" width="500px" />
<img src="imgs\img65.png" width="500px" />

- Save the file and we want now to make sure the S3 bucket is up to date `aws s3 sync /var/www/html s3://acloudguruwp-code-rjk19`:

<img src="imgs\img66.png" width="500px" />

- This will synch the .htaccess file that we have just changed. 
- This is not yet going to work, since we need to tell apache that we are going to **allow URL re-writes**!. 

- Modify Apache configuration (make a copy just in case):
<img src="imgs\img67.png" width="500px" />

- Allow URL re-writes rules to rewrite our URL to be using the cloudfront distribution instead of using our Public IP address:

<img src="imgs\img68.png" width="500px" />

- Restart the service. 

- **Use a Bucket Policy to make the S3 bucket public!!!**.

<img src="imgs\img69.png" width="500px" />

- Make sure you use the ARN of the S3 bucket you want to make public (the S3 of the images). 

- If we go to the Wordpress blog and copy the URL of the image we will see that it is using the CloudFront to serve that image, not using the EC2!!

<img src="imgs\img70.png" width="500px" />

### 7.4 Move EC2 behind a Application Load Balancer

- EC2 > Load Balancer > Create Load Balancer

<img src="imgs\img71.png" width="500px" />

- Across all 3 AZ:

<img src="imgs\img72.png" width="500px" />

- Use my SG WebDMZ
- Create a new target group:

<img src="imgs\img73.png" width="500px" />
<img src="imgs\img74.png" width="500px" />

- Resgister Targets behind the ALB (this is the EC2 instance)

<img src="imgs\img75.png" width="500px" />

- Create the ALB

- **Go to Route53** and we are going to **point a domain over our Application Load Balancer**.
- In the Alias Target, we select the ALB:

<img src="imgs\img76.png" width="800px" />
<img src="imgs\img77.png" width="500px" />

- Go to EC2 and place our EC2 instance inside our Target Group:

<img src="imgs\img78.png" width="500px" />

- Add to the target group my EC2 instance:

<img src="imgs\img79.png" width="500px" />

- Wait until the status is healthy. 
- Now if we navigate, we will see that using that domain we reach the page:

<img src="imgs\img80.png" width="500px" />

## 8. Adding resilience and autoscaling

-  We have on the left our EC2 instance. This will be our writer node. Every time our marketing team wants to go and write a new article it will navigate to this address 34... This EC2 instance will be configured to push any changes to S3.
- We are going to have a fleet (flota) of EC2 instances in the http://hellocloudgurus2019.com, their job is to constantly pull the S3 bucket looking for changes. So when people visit that domain, route53 will redirect them to the http:... (fleet of instances) not to the EC2 instance. So our writer node will not see the users. 

<img src="imgs\img81.png" width="500px" />

-

<img src="imgs\img82.png" width="500px" />

-

<img src="imgs\img83.png" width="500px" />

-

<img src="imgs\img84.png" width="500px" />

-

<img src="imgs\img85.png" width="500px" />

-

<img src="imgs\img86.png" width="500px" />

-

<img src="imgs\img87.png" width="500px" />

-

<img src="imgs\img88.png" width="500px" />

-

<img src="imgs\img89.png" width="500px" />

-

<img src="imgs\img90.png" width="500px" />

-

<img src="imgs\img91.png" width="500px" />

-

<img src="imgs\img92.png" width="500px" />

-

<img src="imgs\img93.png" width="500px" />

-

<img src="imgs\img94.png" width="500px" />

-

<img src="imgs\img95.png" width="500px" />

-

<img src="imgs\img96.png" width="500px" />

-

<img src="imgs\img97.png" width="500px" />

-

<img src="imgs\img98.png" width="500px" />

-

<img src="imgs\img99.png" width="500px" />

-