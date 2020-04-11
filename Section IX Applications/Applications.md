
# SQS

- Web service to give access to a queue of messages that can be used to store messages while waiting for someone to process them.
- Distributed queue system
- Queue is a temporary repository
- If a instance is consuming a message from SQS and suddenly we lose that instance, this message will become available again in the queue. So we don't lose any information. 
- You can **DECOUPLE!!!!** componenets of an application so they **run independently**.
- Any component of a distributed app can store messages in a fail-safe queue.
- Messages can contain up to 2GB, actually the info is stored in S3 (info as text in any format). Any component can retrieve the message programmatically using the SQS API.
- The queue acts as a buffer betwen the component that produces data and the one that is processing.
- This is helpful when the producer produces at a different rate than the processors are processing. 

## Types of Queues

- Standard queue:
    - Nearly unlimited number of transactions per second
    - **Guarantees that the message is DELIVERED AT LEAST ONCE**
    - It may happen that one copy of a message might be delivered **out of order**.
    - Also, there can be multiple copies of the same message being delivered **twice**
- FIFO:
    - First in first out delivery
    - Exactly ONE processing: the order in which messages are sent and received is **preserved** and message is delivered ONCE and remains available until **a consumer processes AND DELETES IT!!!!**. Duplicates are NOT introduced into the queue. 
    - FIFO queues are limited to 300 transactions per second (TPS) and suppor message groups that allow multiple ordered messages in the same single queue.

## Pull based

- SQS is pull based, you need an EC2 instance that is PULING the messages OUT of the queue.

## Retention

- Messages are kept in the queue from 1 min to 14 days. The default retention period is 4 days

## Visibility timeout

- Visibility timeout is the amount of time that the message is INVISIBLE in the SQS queue **AFTER a reader PICKS up that message**. If the job is processed before the visibility timeoute expires, the message will then be **deleted from the queue**. However, if the job is not processed within the **visibility timeout**, then the message **will become available again and another reader will process it**. 
- **This can result in the SAME message being DELIVERED TWICE**!
- EXAM QUESTION: you are in a FIFO and the message is delivered twice!!! what happens? Visibility timeout is not long enough. 
- Visibility timeout maximum is 12 hours, **your job should finish in 12hours**.

## Amazon SQS long polling

- Way to retrieve messages from your Amazon SQS queues. While the regular short polling returns immediately (even if the message queue being polled is EMPTY!!!!), long polling DOES NOT return a response until a message **arrives in the message queue or the long poll times out**. 

- EXAM QUESTION: you have a queue that is **most of the times EMPTY**, and there is a process that is pulling from the queue asking for more job to do and always hits a empty queue. How to reduce your bill? **Use long polling**. It won't turn a response until a message arrives in the queue or the long poll times out. 

# SWF

- Simple Worflow Service: web service that makes easy to **coordinate work** across distributed application components.
- Enables apps for a range of use cases: media processing, web app back-ends, business process workflows, analytics pipelines...

- Tasks reresent invocations of various processing steps in an app which can be erformed by code, web service calls, **human actions**, ...
- SWS: coordinating applications and human beings (manual process).
- **This is used in Amazon Warehouses!!!!**. 

- EXAM QUESTION: human element... SWS!

- **SWF vs SQS**:
    - SQS has retention period of 14 days but in SWF executsions can last up to 1 year
    - SWF presents a **task**-ordientated API, SQS offers a message-oriented API (Exam QUESTION: task, think about SWF)
    - SWF ensures taht a task is assigned only once and NEVER duplicated. in SQS you can have duplicated messages
    - SWF keeps track of all the tasks and events in an app while in SQS you need to implement your own app-tracking.

- **Workflow Starter**: app that can start a workflow. (maybe a website, mobile app)
- **Deciders**: control the flow of activity tasks in a worflow execution. If something finishes, a decider decides what to do next
- **Activity Workers**: carry out the activity tasks

# SNS: Simple Notification Service

- Web service that makes it easy to set up, operate and send notifications from the cloud.
- Highly scalable, flexible, cost effective to publish messages from an app and deliver them to subscribers or other apps.
- **Push notifications!!** to your mobile devices
- Besides pushing cloud notifications, SNS can also deliver notifications by SMS text message, or email to Amazon SQS queues, or any HTTP endpoint. 
- SNS allows to group recipients **using TOPICS**. Topics is an **access point** for allowing recipients to subscribe for identical copies of the same notification. One topic can support deliveries to many endpoint types (group iOS, Android and SMS recipients). When you publish once to a topic, SNS delivers appropriately formatted copies of your message to each subscriber.
- Billing alerts, AutoScaling alerts, could be different topics. 
- All mesages are stored reduntantly across MULTIPLE AZ.

## SNS Benefits

- Push based delivery (no polling)
- Simle API and easy integration
- Pay as you go
- Multiple transport protocols (flexible)
- Web based AWS console offers the simplicity of a point and click interface (simple to use). 

## SNS vs SQS

- Both messaging systems
- SNS push
- SQS is pull based


# Elastic Transcoder

- Media transcoder in the cloud
- Convert media files to different formats
- Proves presets for many output formats
- Pay based on the minutes that you transcode and resolution
- You have you S3 bucket, store the media, this trigger a Lambda function, it will send the video to the Elastic Transcoder
- Elastic transcoder then Stores the videos to S3 bucket.

# API Gateway

- Service that makes it easy for developers to publish, maintain, monitor and secure **APIs** at any scale.
- It is acting as a door way to your AWS environment
- API that acts as a front door for applications to access data, or apps running on EC2, code running on AWS Lambda, or any web app.
- It's somehow like a load balancer but you don't need EC2 instances behind it , usually you use API Gateway to communicate to AWS Lambda Functions. 

- Users do a call to API Gateway.
- Depending on the call, that could be passed to Lambda, EC2 or writing something to Dynamo DB.

## What can they do

- Expose HTTPS endpoints to define a RESTful API
- It can **SERVERLESSly** connect to services like Lambda or Dynamo DB.
- Send each API endpoint to a different target
- Low cost
- Scale effortlessly. You don't have to worry about AutoScaling Groups. 
- Track and control usage by API key
- Throttle API Gateway to prevent attacks. 
- Connecto to CloudWatch to log all requests for monitoring
- Maintain multiple versions of your API


## Configuration

- Define a API (container)
- Define nested resources (URL paths)
- For each resource:
    - Select HTTP methods (GET, POST)
    - Set security
    - Choose target (EC2, Dynamo, Lambda)
- Set requests and respponse transformations

## How to Deploy it

- Deploy it to a stage:
    - Use API Gateway domain by default.
    - Can use a custom domain
    - Suports AWS Certificate Manager; free SSL/TLS cert

## Caching

- API Caching: cache the endpoint response.
- Reduce the number of calls made to your endpoint and also improve the latency of the requests to your API.
- When you enabling cahcing for a stage, API Gateway caches responses from your endpoint for a specified time-to-live TTL period in seconds.
- API Gateway then responds to the requests by looking up the endpoint response from the cache instead of making a requessts to your endpoint.

<img src="imgs\img0.PNG" width="500px" />

- User 1 makes a requests, API gateway process it and returns the response from the Lambda function and then a second user does the SAME requests. Then API Gateway looks at its Cache so that it DOES NOT have to communicate with the Lambda function as it has stored this requests in the cache. 

## Same Origin Policy

- In computing, the same origin policy is important for security.
- Under that policy, a web browser permits scripts contained in a 1st web page to ACCESS DATA in a 2nd web page, but only if both pages have THE SAME ORIGIN! (the same domain name). So basically, one page can talk to another if both are under the same domain name. 
- This is done to prevent **Cross Site Scripting (XSS) attacks**.
- This is ignored by tools like PostMan and Curl.

- When we are using AWS, S3 has its own domain name, CloudFront another domain name... So this can be a hindrance to make those services to communicate. The way to turn this off is by using **CORS**.

## CORS

- CORS is one way the server at the other end (not the cliend code in the browser) can relax the same-origin policy.
- Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources (i.e fonts) on a web page to be requested from another **domain outside the domain from which the first resource was served**. 
- The CORS is enforced by the client, is enforced by YOUR BROWSER!!

## CORS works:
- Browser makes an HTTP OPTIONS call for a URL (OPTIONS is like a GET or POST method in HTTP).
- The server returns a response that says: these other domains are approved to GET this URL
- You might get an ERROR: Origin policy cannot be read at the remote resource? **You need to enable CORS on API Gateway!!!** (this is a EXAMN QUESTION)



# Kinesis

## What is


- Platform on AWS to send the streaming data to
- Makes easy to load and analyze streaming data and also providing the ability to build your own custom apps for your business needs.

## Kinesis Streams

<img src="imgs\img1.PNG" width="500px" />

- We have a device generating data: producers
- Stream data to Kinesis Stream
- It can store data from 24hours - 7 days (**persistent STORAGE**)
- Data is contained in **Shards**. Each shard has its own data (shard for geospacial data, one for stock prices, one for Iot)
- The data consumers (EC2 instances) can analyze that data in that Shards.
- EC2 instances can then store in DynamoDB, S3, EMR, Redshift... 

- **Shards**: 5 transactions per second reads, read rate of 2MB/s and up to 1,000 records per second for writes. Maximum total data write rate of 1MB/s. (NOT GOING TO BE QUIZZED)
- Data capacity of your stream is a function of the number of shards that you specify for the stream. The total capacity of the stream is the sum of the capacities of its shards.  (NOT GOING TO BE QUIZZED)

## Kinesis Firehose

- Data producers send to Firehose
- Inside Firhose there is NO persistent storage, **data has to be analyzed as it comes in**. 
- It's optional to have a Lambda function, that as soon as data comes in Lambda function triggers a set of code for that data and then outputs it to S3 to pass it to Redshift (cannot go directly to Redshift) or go to a **ElasticSearch Cluster**. 

<img src="imgs\img2.PNG" width="500px" />

## Kineses Analytics

- Analyze data on the fly with either Firhose or Streams. Stores this data either on S3, RedShift or ElasticSearch Cluster. 
- Analyze data INSIDE Kinesis (not by a Lambda function) in both Firehose and Streams.

# Web Identity Federation

- WIF lets to give your users access to AWS resources after they have autheticated with a web-based identity provider like Amazon, Facebook or Google.
- After a successful authentication, the user receives an auth code from the Web ID provider, which they can trade for temporary AWS security credentials. 
- Amazon Cognito provides Web Identity Federation with:
    - Sign-up and sign-in to your apps
    - Access for guests users
    - Acts as an identity broker between your application and Web ID providers so you don't need to write any additional code
    - Synchronizes user data for multiple devices
    - Recommended for all mobile apps for AWS services

- Clients authenticates with Facebook, Facebook will give the auth token, client will send this token to Cognito, it will respond and will grant the client access to your AWS environment. So the user can access AWS environment.

<img src="imgs\img3.PNG" width="500px" />

- Cognite brokers between the app of Facebook to provide temporary credentials which map to an IAM role allowing access to the required resources.
- No need fo the app to embed or store AWS credentials locally on the device and it gives users a seamless experiencess acrross mobile devices. 

## User Pools

- Are user directories, used to manage sign-up and sign-in functionality for mobile and web apps.
- Users can sign in directly ot the User POol or using Facebook or Google.
- Cognito acts as an Identity Broker between the identity provider and AWS.
- Successful auth generates a JSON web token (JWTs). 

## Identity Pools

- Enable provide temporary AWS credentials to access AWS services like S3 or Dynamo DB
- User Pools are bout your actual users (email, password) whereas Identity Pools is the actual granting of access to AWS resources.

<img src="imgs\img4.PNG" width="500px" />

- A user wants to connect to our website, she is going to log in using Facebook credentials. Once Facebook authenticates its account is going to pass the message to Cognito that is going to convert it to a JWT Token. She then sends the JWT Token to an identity pool and this pool will grant her the AWS Credentials in the form of an IAM role. She now can access AWS resources.

- If a user updates his credentials (change password), Cognito is going to send out a SNS silent push notification to all the devices of that user, so that all the devices will be synchronized with the new data.

<img src="imgs\img5.PNG" width="500px" />

## Exam Tips

- Federation allow users to auth with Google, Faceboo
- The users auth first with the Web ID Provider and receives an auth token, this token is exchanged for temporary AWS credentials allowing them to assume an IAM role
- Cognito is an Identity Broker which handles interaction between your apps and the Web ID provider.
- User pool is user based. It handles everything about user registration, authentication, account recovery. 
- Identity pools: grants the IAM role to access to your AWS resources. 

<img src="imgs\img6.PNG" width="500px" />

-

<img src="imgs\img7.PNG" width="500px" />

-

<img src="imgs\img8.PNG" width="500px" />

-

<img src="imgs\img9.PNG" width="500px" />

-

<img src="imgs\img10.PNG" width="500px" />

-

<img src="imgs\img11.PNG" width="500px" />

-

<img src="imgs\img12.PNG" width="500px" />

-

<img src="imgs\img13.PNG" width="500px" />

-

<img src="imgs\img14.PNG" width="500px" />

-

<img src="imgs\img15.PNG" width="500px" />

-

<img src="imgs\img16.PNG" width="500px" />

-

<img src="imgs\img17.PNG" width="500px" />

-

<img src="imgs\img18.PNG" width="500px" />

-

<img src="imgs\img19.PNG" width="500px" />

-

<img src="imgs\img20.PNG" width="500px" />

-

<img src="imgs\img21.PNG" width="500px" />

-

<img src="imgs\img22.PNG" width="500px" />

-

<img src="imgs\img23.PNG" width="500px" />

-

<img src="imgs\img24.PNG" width="500px" />

-

<img src="imgs\img25.PNG" width="500px" />

-

<img src="imgs\img26.PNG" width="500px" />

-

<img src="imgs\img27.PNG" width="500px" />

-

<img src="imgs\img28.PNG" width="500px" />

-

<img src="imgs\img29.PNG" width="500px" />

-

<img src="imgs\img30.PNG" width="500px" />

-

<img src="imgs\img31.PNG" width="500px" />

-

<img src="imgs\img32.PNG" width="500px" />

-

<img src="imgs\img33.PNG" width="500px" />

-

<img src="imgs\img34.PNG" width="500px" />

-

<img src="imgs\img35.PNG" width="500px" />

-

<img src="imgs\img36.PNG" width="500px" />

-

<img src="imgs\img37.PNG" width="500px" />

-

<img src="imgs\img38.PNG" width="500px" />

-

<img src="imgs\img39.PNG" width="500px" />

-

<img src="imgs\img40.PNG" width="500px" />

-

<img src="imgs\img41.PNG" width="500px" />

-

<img src="imgs\img42.PNG" width="500px" />

-

<img src="imgs\img43.PNG" width="500px" />

-

<img src="imgs\img44.PNG" width="500px" />

-

<img src="imgs\img45.PNG" width="500px" />

-

<img src="imgs\img46.PNG" width="500px" />

-

<img src="imgs\img47.PNG" width="500px" />

-

<img src="imgs\img48.PNG" width="500px" />

-

<img src="imgs\img49.PNG" width="500px" />

-

<img src="imgs\img50.PNG" width="500px" />

-

<img src="imgs\img51.PNG" width="500px" />

-

<img src="imgs\img52.PNG" width="500px" />

-

<img src="imgs\img53.PNG" width="500px" />

-

<img src="imgs\img54.PNG" width="500px" />

-

<img src="imgs\img55.PNG" width="500px" />

-

<img src="imgs\img56.PNG" width="500px" />

-

<img src="imgs\img57.PNG" width="500px" />

-

<img src="imgs\img58.PNG" width="500px" />

-

<img src="imgs\img59.PNG" width="500px" />

-

<img src="imgs\img60.PNG" width="500px" />

-

<img src="imgs\img61.PNG" width="500px" />

-

<img src="imgs\img62.PNG" width="500px" />

-

<img src="imgs\img63.PNG" width="500px" />

-

<img src="imgs\img64.PNG" width="500px" />

-

<img src="imgs\img65.PNG" width="500px" />

-

<img src="imgs\img66.PNG" width="500px" />

-

<img src="imgs\img67.PNG" width="500px" />

-

<img src="imgs\img68.PNG" width="500px" />

-

<img src="imgs\img69.PNG" width="500px" />

-

<img src="imgs\img70.PNG" width="500px" />

-

<img src="imgs\img71.PNG" width="500px" />

-

<img src="imgs\img72.PNG" width="500px" />

-

<img src="imgs\img73.PNG" width="500px" />

-

<img src="imgs\img74.PNG" width="500px" />

-

<img src="imgs\img75.PNG" width="500px" />

-

<img src="imgs\img76.PNG" width="500px" />

-

<img src="imgs\img77.PNG" width="500px" />

-

<img src="imgs\img78.PNG" width="500px" />

-

<img src="imgs\img79.PNG" width="500px" />

-

