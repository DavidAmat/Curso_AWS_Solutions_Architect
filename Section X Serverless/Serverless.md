# Lambda Concepts

- Lambda is the ultiamte abstraction level

- Is a computer service where you can upload your **code** and crete a function.
- AWS Lambda takes care of rovisioning and managing the servers that you use to run the code.
- You don't have to worry about OS, patching, scaling...

## Applications

- As an event-driven compute service where in response of events lambda is triggered.
- Events can be changes to data in S3 or DynamoDB table
- Run code in response to HTTP requests using Amazon API Gateway or API calls made using AWS SDK. API Gateway proxies the http requests to Lambda, Lambda will run the code in response to that requests and send it back to API Gateway, which will send it back to the user. If there are 2 users that run a requests, **it will hit BOTH the same API Gateway** but both will trigger the **same Lambda Function, but this lambda function will run separately for the 2 users**:

<img src="imgs\img0.PNG" width="500px" />

- Lambda functions can trigger other lambda functions, that maybe what they do is to save something in a S3 bucket and maybe by cross-region replication you can store that info somewhere else in the world.

- Lambda is different from autoscaling in the sense that if 1 Million users make a requests, this **will trigger 1 Million lambda functions**, one by requests. 

## Traditional Architecture vs Serverless Architecture (lots of Exam Questions)

### Traditinal Architecture

- User sends requests, it hits Route53, goes to your ELB, which sends the requests to your Web servers (EC2 instances) and maybe such servers communicate with the backend RDS and sends back response to the user. You are still relying here on operating systems (on EC2 machines). 
- The cool thing about serverless architecture is that the requests is send to API Gateway, it then sends to Lambda, it can write to Dynamo DB and send the responsed back to the user. So if we have 1Million users hitting API Gateway, it will scale automatically, don't need to worry about autoscaling. 

<img src="imgs\img1.PNG" width="500px" />

- EXAM: how well you understand serverless architecture. API Gateway for the requests, Lambda functions to scale out automatically and a database that is serverless (Dynamo DB or Aurora Serverless). RDS is NOT SERVERLESS!! If they are doing maintainance of the OS, this service is down!! Aurora Serverless is the only RDS service that is serverless.

## Languages Support

<img src="imgs\img2.PNG" width="500px" />

## Pricing

- Priced on the number of requests. First 1Million requests are free.
- Then $0.2 per 1 Million requests. It is VERY CHEAP
- Duration: how long it takes the function to run and the memory allocated to your function. You are charged $0.00001667 GB-seconds used. 

## Lambda is Cool

- No worry about servers
- Continuously scales
- Super cheap
- Lambda scales OUT (not up)
- AWS X-Ray allows to debug what is happening if you have a set of complex lambda functions calling multiple lambda functions... So X-ray helps to debug your serverless application.
- Lambda can do **things globally!!**, you can back up a S3 bucket to other S3 bucket. 
- Know the triggers of Lambda!!! for the exam (RDS cannot trigger a lambda function)

# Build a Serverless Webpage using API Gateway and Lambda

- Setup:

<img src="imgs\img3.PNG" width="500px" />

- The user wants to visit that website. The user does not know the IP address so it will go through Route53, user will send the quiery across route53 and Route53 will respond with the bucket address for our website. The user will then go to the S3 bucket, and go to the index.html and when they view that page (static web page), but inside that static webpage, there will be a button there, when pushing the button they will get dynamic content, they will send a requests to API Gateway. Api Gateway will proxy that requests to the Lambda function, which will take the data and return the result and API Gateway will return the result to the user. 

## Configuring Lambda

- Lambda > Functions > From Scratch

<img src="imgs\img4.PNG" width="500px" />

- We will assign a role to that Lambda function, what it is able to coomunicate with. We select the simple microservice permissions:

<img src="imgs\img5.PNG" width="500px" />

- We have our designer and many settings:

<img src="imgs\img6.PNG" width="500px" />

- What will trigger our Lambda Function in our case will be the API Gateway. Note that RDS is not in the list of Triggers.
- If we scroll down:

<img src="imgs\img7.PNG" width="500px" />

- Here we have our IDE. We put our code:

```python
def lambda_handler(event, context):
    print("In lambda handler")

    resp = {
        "statusCode": 200,
        "headers": {
            "Access-Control-Allow-Origin": "*",
        },
        "body": "Ryan Kroonenburg"
    }

    return resp
```
- This function will return this particulare response (200 is the OK status in HTTP), with that body. Returning my name basically.

- You can pass ENV variables that are accessible from your function code:

<img src="imgs\img8.PNG" width="500px" />
<img src="imgs\img9.PNG" width="500px" />

- Now let's configure a Trigger. Click on API GAteway trigger:

<img src="imgs\img10.PNG" width="800px" />

- Choose AWS IAM. And ADD the trigger and Save.
- This has basically created an API ENDPOINT. If we click the API endpoint URL, it will put a Missing Authentication Token. 

<img src="imgs\img11.PNG" width="800px" />

- Click on MyFirstLambdaFunction-API. That will open up API Gateway:

<img src="imgs\img12.PNG" width="800px" />

- The client is sending a requests (ANY requests) and this requests is proxied to the Lambda Function. The Lambda Function responds and proxies it back to our client. 

<img src="imgs\img13.PNG" width="500px" />

- Now, we don't want ANY requests, we only want GET requests. So let's delete that method: Actions > Delete. And create a new one Actions > Create:

<img src="imgs\img14.PNG" width="500px" />

- Enable Use Default Timeout as well as Lambda Proxy Integration. 

<img src="imgs\img15.PNG" width="500px" />

- When we hit save this message appears, click OK.
- Now we need to deploy our API:

<img src="imgs\img16.PNG" width="500px" />
<img src="imgs\img17.PNG" width="500px" />

- If we clock on the GET, make sure we click on the INVOKE URL:

<img src="imgs\img18.PNG" width="500px" />

- This wil open a new window in the browser with the body of the code:

<img src="imgs\img19.PNG" width="200px" />

- Now copy that INVOKE URL link to the clipboard. And open the index.html file, and Paste the INVOKE URL to the code where it does the CALL to the API (PASTE YOUR API GATEWAY part)
:

```html
<html>
<script>

function myFunction() {
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function() {
        if (this.readyState == 4 && this.status == 200) {
        document.getElementById("my-demo").innerHTML = this.responseText;
        }
    };
    xhttp.open("GET", "YOUR-API-GATEWAY-LINK-HERE", true);
    xhttp.send();

}

</script>
<body><div align="center"><br><br><br><br>
<h1>Hello <span id="my-demo">Cloud Gurus!</span></h1>
<button onclick="myFunction()">Click me</button><br>
<img src="https://s3.amazonaws.com/acloudguru-opsworkslab-donotdelete/ACG_Austin.JPG"></div>
</body>
</html>
```

- Basically when the client clicks the button Click me! it will call the myFunction, which will do an HTTP requests (GET) to the API Gateway (that is why we need the URL of the Gatway). 

## Configuring Domain Name - Route53

- Route53 and make sure one domain is there:

<img src="imgs\img20.PNG" width="500px" />

## S3

- Create a bucket:

<img src="imgs\img21.PNG" width="500px" />

- By default, bucket and objets are not public. Since this is going to host a website (static), we will make it public. 

<img src="imgs\img22.PNG" width="500px" />

- Edit public access:

<img src="imgs\img23.PNG" width="500px" />

- Uncheck everything.

<img src="imgs\img24.PNG" width="500px" />

- Let's go to inside the bucket to configure it as a Website Static hosting:

<img src="imgs\img25.PNG" width="500px" />

- Select the name of index and error files:

<img src="imgs\img26.PNG" width="500px" />

- Now go to upload and upload the index.html and error.html. 

<img src="imgs\img27.PNG" width="500px" />

- Make them public:

<img src="imgs\img28.PNG" width="500px" />

- Go to the index.html file and take the URL:

<img src="imgs\img29.PNG" width="500px" />

- Click the Click me button:

<img src="imgs\img30.PNG" width="500px" />

- And voila!

<img src="imgs\img31.PNG" width="500px" />

- If we want to nail it, go to Route53, click on the domain name and Create a Record Set:

<img src="imgs\img32.PNG" width="500px" />

- Confifgure the S3 bucket.


## Build an Alexa Skill

- Put the MP3 files to an S3 bucket, and use Alexa , building a lambda function using the serverless application repositoyr. We are going to point that lambda function to the S3 bucket and we will be able to build an Alexa skill.

- Create a S3 bucket: acloudgurupollyassets2019, make the S3 public and go to bucket policy to make every file public:

<img src="imgs\img33.PNG" width="500px" />

- Machine Learning > Amazon Polly

<img src="imgs\img34.PNG" width="500px" />

- We will add a text and Synthesize to S3:

<img src="imgs\img35.PNG" width="500px" />

- Select the bucket name:

<img src="imgs\img36.PNG" width="500px" />

- This has created a task:

<img src="imgs\img37.PNG" width="500px" />

- Once the task is completed:

<img src="imgs\img38.PNG" width="500px" />

- Go to S3 and see the mp3 file:

<img src="imgs\img39.PNG" width="500px" />

- Let's create an Alexa Skill that is going to start playing those mp3 files. 

## Create the Lambda function

- Compute > Lambda 
- Cretate it in a region in which Alexa Triggers are enabled.

<img src="imgs\img40.PNG" width="500px" />

- Click on the AWS Serverless Application Repo and select the first of alexa.
- Hit Deploy.

<img src="imgs\img41.PNG" width="500px" />

- Once it is up our Lambda we will see that it has the Alexa Skills Kit:

<img src="imgs\img42.PNG" width="500px" />

- This region supports Alexa Skills as Trigger. If we go down:

<img src="imgs\img43.PNG" width="500px" />

- We can customize the message that is going to read out. It will randomly pick any of the lines in data:

<img src="imgs\img44.PNG" width="500px" />

- We will add my name in the data as an element of the list.
- Now, copy the ARN of the lambda function:

<img src="imgs\img45.PNG" width="500px" />

- We will need that to create the skill.
- Sign up for a developer account, if we have a Alexa device, use the same email register in the alexa device.

<img src="imgs\img46.PNG" width="500px" />

- Create A skill, match the Alexa device language with the default language:

<img src="imgs\img47.PNG" width="500px" />

- Select the Custom and Self Hosted.
- Choose a Template: we already used the serverless application repository for a Fact Skill:

<img src="imgs\img48.PNG" width="500px" />

- This is teh Alexa console:

<img src="imgs\img49.PNG" width="500px" />

- Give a name to the invocation:

<img src="imgs\img50.PNG" width="500px" />

- Put the Lambda ARN in the Endpoint. 

<img src="imgs\img51.PNG" width="500px" />

- This is basically pointing our skill to the Lambda Function. 

<img src="imgs\img52.PNG" width="500px" />

- Go to sample utterances and change sample utterances to a cloud fact and add it to the utterances. Utterances is just a way of saying something  in a multiple ways. 

- **Build the model!!**.

- If we go to Test and choose Development, we will use the Alexa Simulator to enter a command: open cloud fact. And we will the the Skill input and output. If we see the response:

<img src="imgs\img53.PNG" width="900px" />
<img src="imgs\img54.PNG" width="500px" />

- We will see that it has picked one from the data:

<img src="imgs\img55.PNG" width="800px" />

- If I modify the Lambda Function to only output my name in "data":

<img src="imgs\img56.PNG" width="500px" />

-  It will read out my name.

- So the last thing is to point our fact to our S3 bucket, to our Mp3 Polly bucket. 
- Go to S3  and grab the URL of the bucket:

<img src="imgs\img57.PNG" width="500px" />

- Go to the lambda function and make the change in data to point to that direction.  To avoid it reading it, we should put it as an audio source. See the documentation of the developer custom skills:

<img src="imgs\img58.PNG" width="500px" />

- Go back to the Lambda funcion and put the audio src:

<img src="imgs\img59.PNG" width="500px" />

- Save it:

<img src="imgs\img60.PNG" width="500px" />

- Test it: 

<img src="imgs\img61.PNG" width="500px" />

- Now, when running it it will run the audio source. 
