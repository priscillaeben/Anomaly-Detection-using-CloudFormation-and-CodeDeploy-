Anomaly Detection using CloudFormation and CodeDeploy 

Task - 1 (Easy) -- M03Project2\Cloud Formation Templates\M3P02_Task1.json
****************************************************************************
a. Provisioned the services (EC2, kinesis, dynamodb, SNS) using cloud formation

AWS CLI Command used :
***********************
aws cloudformation create-stack --stack-name m03p2-stack --template-body file://M3P02_Task1.json --capabilities CAPABILITY_NAMED_IAM

b. Setup the ingestion python script in the EC2 instance manually.

For manual injestion, I did SCP the file to EC2 using the command,

scp -i "priscikey.pem" raw_data.py ec2-user@ec2-3-238-6-234.compute-1.amazonaws.com:/home/ec2-user

Created a virtual environment for python in EC2 and executed the raw_data.py file as follows,

python3 -m venv my_app/env
source ~/my_app/env/bin/activate

c. Created the lambda handler function from lambda handler service manually through AWS console. Copied the lambda handler code and made changes in SNS arn and Table name.

d. The entire setup for anomaly detection worked fine i.e notification got generated and detected data got pushed in DynamoDB

Task - 2 (Moderate) -- M03Project2\Cloud Formation Templates\M3P02_Task2.json
******************************************************************************

a. Provisioned S3 service using Cloud formation and stored the original application of Task-1 and the appspec.yml.
b. Deployed the contents of S3 using CodeDeploy into the EC2 instance at the same location where it was running earlier.

Commands Used :
***************
Application creation for deployment using following code :
***********************************************************
aws deploy create-application --application-name temperature

Pushed the code to S3 using the following command :
***************************************************
aws deploy push --application-name temperature --s3-location s3://s3m3p2f5/s3_m3p2.zip --ignore-hidden-files

Created deployment group using the following command :
******************************************************
aws deploy create-deployment-group --application-name temperature --deployment-group-name temperature_DepGroup --deployment-config-name CodeDeployDefault.OneAtATime --ec2-tag-filters Key=Name,Value=EC2Instance_EC2M3PROJECT2,Type=KEY_AND_VALUE --service-role-arn arn:aws:iam::756651670497:role/CodeDeployServiceRole

Created association using the following command :
*************************************************
aws ssm create-association --name AWS-ConfigureAWSPackage --targets Key=tag:Name,Values=CodeDeployDemo --parameters action=Install,name=AWSCodeDeployAgent --schedule-expression "cron(0 2 ? * SUN *)"

Deployed the code from S3 bucket to EC2 instance using code deploy using following command :
********************************************************************************************
aws deploy create-deployment --application-name temperature --deployment-config-name CodeDeployDefault.OneAtATime --deployment-group-name temperature_DepGroup --s3-location bucket=s3m3p2f5,bundleType=zip,key=s3_m3p2.zip

c. Lambda handler deployed manually from AWS console
d. The entire setup for anomaly detection worked fine i.e notification generated and detected data pushed in DynamoDB

Task - 3 (Hard) -- M03Project2\Cloud Formation Templates\M3P02_Task3.json
***************************************************************************
a. Provisioned the lambda handler service using cloud formation
b. Removed the older lambda handler function from Task-2, pushed the lambda code from s3 bucket to lambda by referring it in cloud formation template.
c. The entire setup for anomaly detection worked fine. notification generated and detected data pushed in DynamoDB
