# Stop-Start-EC2-Instances
Stop and start EC2 Instances via CloudWatch Events cron job.

First of all we want to create a policy for our the two Lambda functions (one to stop and one to start) and we will be creating this via the IAM Policy Generator - https://awspolicygen.s3.amazonaws.com/policygen.html

If however you know the Instance IDs of all the EC2 instances you wish to stop and start then you can enter the ARN for the instances - arn:aws:ec2:<REGION>:<ACCOUNT_ID>:instance/<instance-id> to maintain best security practices of least privileged:
  
![1  Policy Generator](https://user-images.githubusercontent.com/68379635/102479001-3970f000-4056-11eb-9e3c-093ec5ef1de6.PNG)

Once we generate this policy we create the policy and name it 'LambdaStopStartEC2'.

Next we want to create two Roles 'LambdaStopEC2Role' and 'LambdaStartEC2Role' and attach our policy to both of these:

![2  Stop Role](https://user-images.githubusercontent.com/68379635/102480146-c8323c80-4057-11eb-8396-9905ac616238.jpg)

![3  Start Role](https://user-images.githubusercontent.com/68379635/102480980-f5331f00-4058-11eb-9ca2-385ae26e788d.jpg)

Next you want to create an EC2 instance and I'd suggest using a T2.micro if you are still free tier eligible or to keep cost low if not. Here you want to make a note of your Instance ID.

Next we want to create two Lambda functions with a runtime of Python 3.7 and we want to attach the Roles we previously created to both of them:

![4  Lambda stop EC2](https://user-images.githubusercontent.com/68379635/102481796-23fdc500-405a-11eb-97ab-2dc59fd9fb6f.PNG)

![5  Lambda start EC2](https://user-images.githubusercontent.com/68379635/102481824-2fe98700-405a-11eb-9707-1e2fd4a13aa7.PNG)

Once deployed you will deploy the code for each function in the .zip file and enter your region and instance ID/s.

Now you want to go to CloudWatch and under Events we want to go to Rules and we are going to create two rules. We are going to use a Schedule for both and cron expressions. The first rule triggers at 17:00 GMT each day and the target is our StopEC2 Lambda function:

![8  Cron job stop](https://user-images.githubusercontent.com/68379635/102483260-5f998e80-405c-11eb-85fd-5e9d6bc9dbc5.PNG)

The second rule is to trigger our StartEC2 Lambda function at 10:00 GMT each day:

![9  Cron job start](https://user-images.githubusercontent.com/68379635/102483506-c0c16200-405c-11eb-9986-d0db1b93924e.PNG)

Good to add descriptions so other users know what these rules are for:

![10 Cloudwatch Events rules](https://user-images.githubusercontent.com/68379635/102483559-d33b9b80-405c-11eb-91cb-f2c8ee294a35.PNG)

Now each day at 17:00 GMT our instances will stop and each morning at 10:00 GMT they will start and we can see this working correctly in CloudWatch.

Stopped Instance:

![12  Stopped instance Lambda Log](https://user-images.githubusercontent.com/68379635/102483849-404f3100-405d-11eb-9c60-9ce46436cf91.PNG)

Started Instance:

![13  Started instance Lambda Log](https://user-images.githubusercontent.com/68379635/102483908-61b01d00-405d-11eb-89c1-068b300fdf35.PNG)
