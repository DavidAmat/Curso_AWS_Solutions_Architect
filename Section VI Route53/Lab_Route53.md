# Register a Domain Name

- Click on Route 53
<img src="imgs\img0.PNG" width="200px" />

- Click on Domain Registration (not free):

<img src="imgs\img1.PNG" width="500px" />

- Search for the availability of the domain:
<img src="imgs\img2.PNG" width="500px" />

- Fill details of your personal contact and pay for it and voila:

<img src="imgs\img3.PNG" width="500px" />

- It can take up to 3 days to complete... it take usually 1-2 hours.

<img src="imgs\img4.PNG" width="500px" />

- Once it is configured and available we can see the Hosted zone, where we will configure Route53:

<img src="imgs\img5.PNG" width="500px" />

- Now I **will provision an EC2 instance** ( I choose a region very close to me cause I will be looking at latency and networking stats).

- He will pick 3 regions: Ireland (very close), Sydeny (furthest), Ohio (in-between distance). 

- He will use for the 3 instances this bootstrap script (to create a web server with a web page index.html):

```bash
#!/bin/bash
yum update -y
yum install httpd -y
chkconfig httpd on
service httpd start
cd /var/www/html
echo "<html><h1>Hello Cloud Gurus! This is the X Web Server</h1></html>" > index.html
# where X will change: for the Ireland, Ohio, Sydney region, to differentiate them and see stats
```

- For the other regions (Ohio and Sydney), while creating the instance we will have to create a SG with port HTTP (80) open.

## Exam tips

- We can buy domain names directly with AWS
- It can take up to 3 days depending on the circumstances

```bash

```


´´´python
for ii in range(0,100):
	print(f'<img src="imgs\img{ii}.PNG" width="500px" />')
´´´ 

