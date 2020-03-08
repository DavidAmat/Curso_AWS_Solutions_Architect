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

- 

<img src="imgs\img9.png" width="500px" />
<img src="imgs\img10.png" width="500px" />
<img src="imgs\img11.png" width="500px" />
<img src="imgs\img12.png" width="500px" />
<img src="imgs\img13.png" width="500px" />
<img src="imgs\img14.png" width="500px" />
<img src="imgs\img15.png" width="500px" />
<img src="imgs\img16.png" width="500px" />
<img src="imgs\img17.png" width="500px" />
<img src="imgs\img18.png" width="500px" />
<img src="imgs\img19.png" width="500px" />
<img src="imgs\img20.png" width="500px" />
<img src="imgs\img21.png" width="500px" />
<img src="imgs\img22.png" width="500px" />
<img src="imgs\img23.png" width="500px" />
<img src="imgs\img24.png" width="500px" />
<img src="imgs\img25.png" width="500px" />
<img src="imgs\img26.png" width="500px" />
<img src="imgs\img27.png" width="500px" />
<img src="imgs\img28.png" width="500px" />
<img src="imgs\img29.png" width="500px" />
<img src="imgs\img30.png" width="500px" />
<img src="imgs\img31.png" width="500px" />
<img src="imgs\img32.png" width="500px" />
<img src="imgs\img33.png" width="500px" />
<img src="imgs\img34.png" width="500px" />
<img src="imgs\img35.png" width="500px" />
<img src="imgs\img36.png" width="500px" />
<img src="imgs\img37.png" width="500px" />
<img src="imgs\img38.png" width="500px" />
<img src="imgs\img39.png" width="500px" />
<img src="imgs\img40.png" width="500px" />
<img src="imgs\img41.png" width="500px" />
<img src="imgs\img42.png" width="500px" />
<img src="imgs\img43.png" width="500px" />
<img src="imgs\img44.png" width="500px" />
<img src="imgs\img45.png" width="500px" />
<img src="imgs\img46.png" width="500px" />
<img src="imgs\img47.png" width="500px" />
<img src="imgs\img48.png" width="500px" />
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

