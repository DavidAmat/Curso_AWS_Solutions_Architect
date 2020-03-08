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

<img src="imgs\img6.PNG" width="500px" />
<img src="imgs\img7.PNG" width="500px" />
<img src="imgs\img8.PNG" width="500px" />
<img src="imgs\img9.PNG" width="500px" />
<img src="imgs\img10.PNG" width="500px" />
<img src="imgs\img11.PNG" width="500px" />
<img src="imgs\img12.PNG" width="500px" />
<img src="imgs\img13.PNG" width="500px" />
<img src="imgs\img14.PNG" width="500px" />
<img src="imgs\img15.PNG" width="500px" />
<img src="imgs\img16.PNG" width="500px" />
<img src="imgs\img17.PNG" width="500px" />
<img src="imgs\img18.PNG" width="500px" />
<img src="imgs\img19.PNG" width="500px" />
<img src="imgs\img20.PNG" width="500px" />
<img src="imgs\img21.PNG" width="500px" />
<img src="imgs\img22.PNG" width="500px" />
<img src="imgs\img23.PNG" width="500px" />
<img src="imgs\img24.PNG" width="500px" />
<img src="imgs\img25.PNG" width="500px" />
<img src="imgs\img26.PNG" width="500px" />
<img src="imgs\img27.PNG" width="500px" />
<img src="imgs\img28.PNG" width="500px" />
<img src="imgs\img29.PNG" width="500px" />
<img src="imgs\img30.PNG" width="500px" />
<img src="imgs\img31.PNG" width="500px" />
<img src="imgs\img32.PNG" width="500px" />
<img src="imgs\img33.PNG" width="500px" />
<img src="imgs\img34.PNG" width="500px" />
<img src="imgs\img35.PNG" width="500px" />
<img src="imgs\img36.PNG" width="500px" />
<img src="imgs\img37.PNG" width="500px" />
<img src="imgs\img38.PNG" width="500px" />
<img src="imgs\img39.PNG" width="500px" />
<img src="imgs\img40.PNG" width="500px" />
<img src="imgs\img41.PNG" width="500px" />
<img src="imgs\img42.PNG" width="500px" />
<img src="imgs\img43.PNG" width="500px" />
<img src="imgs\img44.PNG" width="500px" />
<img src="imgs\img45.PNG" width="500px" />
<img src="imgs\img46.PNG" width="500px" />
<img src="imgs\img47.PNG" width="500px" />
<img src="imgs\img48.PNG" width="500px" />
<img src="imgs\img49.PNG" width="500px" />
<img src="imgs\img50.PNG" width="500px" />
<img src="imgs\img51.PNG" width="500px" />
<img src="imgs\img52.PNG" width="500px" />
<img src="imgs\img53.PNG" width="500px" />
<img src="imgs\img54.PNG" width="500px" />
<img src="imgs\img55.PNG" width="500px" />
<img src="imgs\img56.PNG" width="500px" />
<img src="imgs\img57.PNG" width="500px" />
<img src="imgs\img58.PNG" width="500px" />
<img src="imgs\img59.PNG" width="500px" />
<img src="imgs\img60.PNG" width="500px" />
<img src="imgs\img61.PNG" width="500px" />
<img src="imgs\img62.PNG" width="500px" />
<img src="imgs\img63.PNG" width="500px" />
<img src="imgs\img64.PNG" width="500px" />
<img src="imgs\img65.PNG" width="500px" />
<img src="imgs\img66.PNG" width="500px" />
<img src="imgs\img67.PNG" width="500px" />
<img src="imgs\img68.PNG" width="500px" />
<img src="imgs\img69.PNG" width="500px" />
<img src="imgs\img70.PNG" width="500px" />
<img src="imgs\img71.PNG" width="500px" />
<img src="imgs\img72.PNG" width="500px" />
<img src="imgs\img73.PNG" width="500px" />
<img src="imgs\img74.PNG" width="500px" />
<img src="imgs\img75.PNG" width="500px" />
<img src="imgs\img76.PNG" width="500px" />
<img src="imgs\img77.PNG" width="500px" />
<img src="imgs\img78.PNG" width="500px" />
<img src="imgs\img79.PNG" width="500px" />
<img src="imgs\img80.PNG" width="500px" />
<img src="imgs\img81.PNG" width="500px" />
<img src="imgs\img82.PNG" width="500px" />
<img src="imgs\img83.PNG" width="500px" />
<img src="imgs\img84.PNG" width="500px" />
<img src="imgs\img85.PNG" width="500px" />
<img src="imgs\img86.PNG" width="500px" />
<img src="imgs\img87.PNG" width="500px" />
<img src="imgs\img88.PNG" width="500px" />
<img src="imgs\img89.PNG" width="500px" />
<img src="imgs\img90.PNG" width="500px" />
<img src="imgs\img91.PNG" width="500px" />
<img src="imgs\img92.PNG" width="500px" />
<img src="imgs\img93.PNG" width="500px" />
<img src="imgs\img94.PNG" width="500px" />
<img src="imgs\img95.PNG" width="500px" />
<img src="imgs\img96.PNG" width="500px" />
<img src="imgs\img97.PNG" width="500px" />
<img src="imgs\img98.PNG" width="500px" />
<img src="imgs\img99.PNG" width="500px" />


´´´python
for ii in range(0,100):
	print(f'<img src="imgs\img{ii}.PNG" width="500px" />')
´´´ 

