# Creating an RDS Instance



<img src="imgs\img1.PNG" width="200px" />

<img src="imgs\img2.PNG" width="200px" />

## Templates

- **Production**: if you need high availability and consistent performance
- **Dev/Test**
- **Free tier**

<img src="imgs\img3.PNG" width="300px" />

- Name unique in your AWS account


<img src="imgs\img4.PNG" width="300px" />

- **Storage autoscaling**: add storage on the fly if your reach out of space
<img src="imgs\img5.PNG" width="200px" />

- **Connectivity**:

<img src="imgs\img6.PNG" width="300px" />

- Change the VPC to a new one (**LOOK that we are creating a SG named rds-launch-wizard**):

<img src="imgs\img7.PNG" width="300px" />

- Advanced configurations - name of the database, backup automatic (put 0 days to not enable automatic backups)

<img src="imgs\img9.PNG" width="200px" />

- Maintenance: all the pending modifications or maintenance is applied to the DB by Amazon RDS
<img src="imgs\img10.PNG" width="200px" />

- **RDS are more slow to be available than EC2** (5-10 min)

- While **waiting, setup a new instance (everything as default but the Bootstrap script)**:

	- Install wordpress, PHP-MySQL and changing permissions to the directory to write to it

```bash
#!/bin/bash
yum install httpd php php-mysql -y
cd /var/www/html
wget https://wordpress.org/wordpress-5.1.1.tar.gz
tar -xzf wordpress-5.1.1.tar.gz
cp -r wordpress/* /var/www/html/
rm -rf wordpress
rm -rf wordpress-5.1.1.tar.gz
chmod -R 755 wp-content
chown -R apache:apache wp-content
service httpd start
chkconfig httpd on
```

- Use the WebDMZ SG. Here **we see that we also have the SG from the RDS instance**:

<img src="imgs\img11.PNG" width="400px" />

- We need our **WebDMZ SG to talk to the RDS** cause we want our WebServer to talk with the RDS created using the MySQL port.

- To allow **RDS to accept INBOUND traffic from the WebServer (SG: WebDMZ) we go to Inbound > Edit > Add Rule > MySQL (Source = SG of WebDMZ)**:

<img src="imgs\img12.PNG" width="600px" />

- Once the RDS is available:

<img src="imgs\img13.PNG" width="400px" />

- Click on it:

<img src="imgs\img14.PNG" width="500px" />

- We see the **DNS endpoint** (Endpoint & Port) to connect to our RDS instance (that will be used to install wordpress)

- In Monitoring we see that is tied with CloudWatch:
<img src="imgs\img15.PNG" width="400px" />

- Go to **EC2** and grab the public IP:

<img src="imgs\img16.PNG" width="500px" />

- Navigate to it:

<img src="imgs\img17.PNG" width="500px" />

- You need to provide de DB Host to the **Endpoint** of your RDS instance (section Endpoints & Ports in RDS):

<img src="imgs\img18.PNG" width="500px" />

- It will tell us that we need to add that script in our EC2 instance:

<img src="imgs\img19.PNG" width="500px" />

- We **SSH to our EC2 instance**:

<img src="imgs\img20.PNG" width="500px" />

```bash
sudo su
cd /var/www/html
nano wp-config.php
#paste the config 
```

- Hit next in the WordPress web browser tab:

<img src="imgs\img21.PNG" width="500px" />

- Install WordPress and once installed login with the credentials:

<img src="imgs\img22.PNG" width="500px" />

### Exam tips

-  RDS run on virtual machines but **you don't have access to RDS, you cannot SSH to an RDS**
- You cannot log in to the Operating systems where the RDS is hosted
- Patching (crear parches or updates in the system where the DB is hosted) of the RDS OS and DB is Amazon's responsibility
- RDS is **NOT serverless** (except Aurora serverless). BUt the other ones they **run on virtual machines** but you cannot access such machines, only Amazon


# RDS: BackUps, MultiAZ & Read Replicas (LAB)

## Multi AZ
- Go to RDS > Databases > select your database and see configuration (actions you can take):

<img src="imgs\img23.PNG" width="500px" />

- You can migrate MySQL db to Aurora.

### Create a Snapshot

- If we take an snapshot and migrate snapshot. If we create the SNAPSHOT and restore from that snapshot, it will create a new entire RDS instance. 
- **Click on Modify**:

	- Turn on Multi-AZ:

<img src="imgs\img24.PNG" width="500px" />

- Then we are advised about the Potential performance impact, so make sure that we hit the **Apply during the next scheduled maintenance window**

<img src="imgs\img25.PNG" width="500px" />

- For the sake of the example we are going to apply immediately and not choose scheduling:

<img src="imgs\img26.PNG" width="500px" />

- Now the Status is Modifying...

<img src="imgs\img27.PNG" width="500px" />

- Once modified, we see that MultiAZ is turned on:

<img src="imgs\img28.PNG" width="500px" />

- We do a reboot we can **reboot with Failover** to force our AZ to change, so you can change from one AZ to another if we hit the rebooting with failover:

<img src="imgs\img29.PNG" width="500px" />
<img src="imgs\img30.PNG" width="500px" />

## Read Replicas

- Read Replicas are not going to work cause I have not my Backup turned on (if I go to Modify I will check that):

<img src="imgs\img31.PNG" width="500px" />

- Hence, we cannot create a read replica yet:

<img src="imgs\img32.PNG" width="500px" />

- Go to Modify and turn on to Backup:

<img src="imgs\img33.PNG" width="500px" />

- Turn to 35 days for example, and apply it immediately:
<img src="imgs\img34.PNG" width="500px" />

- Go to **Actions > Create Read Replica**

- The RR can be in any region:
<img src="imgs\img35.PNG" width="500px" />

- The RR can have Multi AZ:
<img src="imgs\img36.PNG" width="500px" />

- Add a new name for the RR:

<img src="imgs\img37.PNG" width="500px" />

- Hit CREATE and we will see that it will be modifying the Master DB and a Replica has been created:

<img src="imgs\img38.PNG" width="500px" />

- We can **PROMOTE a RR**:

<img src="imgs\img39.PNG" width="500px" />

## Exam Tips

- Two different types of BK: Automated (in a scheduled maintanance window) and DB Snapshots (manually).
- RR can be MultiAZ too
- Used to increase performance
- Must have backups turned on to have a RR
- Can be in different regions
- Can be promoted to master (and break replication, so it will no longer act as a RR)

- MultiAZ are used for Disaster Recovery
- You can **force the failover from one AZ to another by rebooting the RDS**.

- Encryption is supported for MySQL, Oracle, SQL Server, PostgreSQL, MariaDB and AUrora. 
- Is done using the AWS KMS (Key Management Services). 
- Once data is encrypted, the underlyng storage is also encrypted too (as are its automated backup,s replicas and snapshots).


# Aurora Database (convert MySQL to Aurora)

- Create an Aurora READ Replica from our RDS instance.

- Actions > Create an Aurora Read replica

<img src="imgs\img40.PNG" width="500px" />

- We can create our replica in a **different AZ**:

<img src="imgs\img41.PNG" width="500px" />

- Put a DB instance identifier (name of the DB Aurora: acloudguru-aurora)

- Leave everything else as default (no encryption, backups for 1 day)


<img src="imgs\img42.PNG" width="500px" />

- Once  it is finished we have our aurora cluster with 2 nodes:
	- writer node
	- reader node

- They are in **separate AZ** each one with each own DNS endpoint:

<img src="imgs\img43.PNG" width="500px" />

- Hence, **reader and writer are SEPARATE databases** in separate AZ with separate DNS endpoint.

- Now, we can go to the **writer and PROMOTE READ REPLICA**:

<img src="imgs\img44.PNG" width="500px" />

- This can be also done with the reader node. This takes time, but once it is done, what has happened is that **we have migrated our database from a MySQL database to an AURORA database simply by creating a READ REPLICA and PROMOTING that database to its own STANDALONE database**.

- Another alternative to **convert the MySQL to AURORA** would have been to take an snapshot of my read replica (i.e. writer) :

<img src="imgs\img45.PNG" width="500px" />

- And then we can **restore an AURORA DB from that SNAPSHOT**. 
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