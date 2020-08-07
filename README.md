Project Overview
In this project, I have:

Deployed and assessed a simple web application environment’s security posture
Tested the security of the environment by simulating attack scenarios and exploiting cloud configuration vulnerabilities
Implemented monitoring to identify insecure configurations and malicious activity
Applied methods learned in the course to harden and secure the environment
Designed a DevSecOps pipeline

We will utilize the AWS CLI in this guide, however you are welcome to use the AWS console to deploy the CloudFormation templates.

From the root directory of the repository - execute the below command to deploy the templates.
Deploy the S3 buckets
aws cloudformation create-stack --region us-east-1 --stack-name c3-s3 --template-body file://starter/c3-s3.yml
Expected example output:

{
    "StackId": "arn:aws:cloudformation:us-east-1:4363053XXXXXX:stack/c3-s3/70dfd370-2118-11ea-aea4-12d607a4fd1c"
}
Deploy the VPC and Subnets
aws cloudformation create-stack --region us-east-1 --stack-name c3-vpc --template-body file://starter/c3-vpc.yml
Expected example output:

{
    "StackId": "arn:aws:cloudformation:us-east-1:4363053XXXXXX:stack/c3-vpc/70dfd370-2118-11ea-aea4-12d607a4fd1c"
}
Deploy the Application Stack
You will need to specify a pre-existing key-pair name.

aws cloudformation create-stack --region us-east-1 --stack-name c3-app --template-body file://starter/c3-app.yml --parameters ParameterKey=KeyPair,ParameterValue=<add your key pair name here> --capabilities CAPABILITY_IAM
Expected example output:

{
    "StackId": "arn:aws:cloudformation:us-east-1:4363053XXXXXX:stack/c3-app/70dfd370-2118-11ea-aea4-12d607a4fd1c"
}
Expected example AWS Console status: https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks

Upload the free recipes to the free recipe S3 bucket from step 2. Do this by typing this command into the console (you will replace `<BucketNameRecipesFree>` with your bucket name):
 
Example:  
```
aws s3 cp free_recipe.txt s3://<BucketNameRecipesFree>/ --region us-east-1
```
 
Upload the secret recipes to the secret recipe S3 bucket from step 2. Do this by typing this command into the console (you will replace `<BucketNameRecipesSecret>` with your bucket name):
 
Example:  
```
aws s3 cp secret_recipe.txt s3://<BucketNameRecipesSecret>/ --region us-east-1
```
 
Test the application
Invoke the web service using the application load balancer URL:
```
http://<ApplicationURL>/free_recipe
```
You should receive a recipe for banana bread.
The AMIs specified in the cloud formation template exist in the us-east-1 (N. Virginia) region. You will need to set this as your default region when deploying resources for this project.

Brute force attack to exploit SSH ports facing the internet and an insecure configuration on the server
ssh -i <your private key file> ubuntu@<AttackInstanceIP>

1. Log in to the attack simulation server using your SSH key pair.
2. Run the below commands to start a brute force attack against the application server. You will need the application server hostname for this.
date
hydra -l ubuntu -P rockyou.txt ssh://<YourApplicationServerDnsNameHere>

# view the files in the secret recipes bucket
aws s3 ls  s3://<BucketNameRecipesSecret>/ --region us-east-1
 
# download the files
aws s3 cp s3://<BucketNameRecipesSecret>/secret_recipe.txt  .  --region us-east-1

# view contents of the file
cat secret_recipe.txt

To disable SSH password login on the application server instance.
# open the file /etc/ssh/sshd_config
sudo vi /etc/ssh/sshd_config

# Find this line:
PasswordAuthentication yes

# change it to:
PasswordAuthentication no

# save and exit

#restart SSH server
sudo service ssh restart
Test that this made a difference.

Project Files
E1T4: Text file identifying 2 poor security practices with justification for the AWS-WebServiceDiagram-v1-insecure.png architectural model.
E2T2: Text file providing recommendations on how to remediate the vulnerabilities.
E3T1: Text file listing GuardDuty findings that were detected related to the brute force attack ande explaination about how GuardDuty detected the attack.
E4T1: Text file listing 2-3 changes that can be made to environment to prevent an ssh brute force attack from the internet and also 2-3 points about how to secure the sensitive data in S3.
E4T4:Analysis
What additional architectural change can be made to reduce the internet-facing attack surface of the web application instance.
Assuming the IAM permissions for the S3 bucket are still insecure, would creating VPC private endpoints for S3 prevent the unauthorized access to the secrets bucket.
Will applying default encryption setting to the s3 buckets encrypt the data that already exists?
The changes you made above were done through the console or CLI; describe the outcome if the original cloud formation templates are applied to this environment?
E5T2:Tools and Documentation
Identify tools that will allow you to do the following: a. Scan infrastructure as code templates. b. Scan AMI’s or containers for OS vulnerabilities. c. Scan an AWS environment for cloud configuration vulnerabilities.
For each tool - identify an example compliance violation or vulnerability which it might expose.
starter: This folder contains all the files that were provided to implement security features on it.
screenshots: This folder contains all the screenshots captured during this project.