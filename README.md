# System provisioning  



## First of all! AWS - Infrastructure provisioning  
- Amazon S3 — Travis CI will transmit our code from github to an S3 bucket for setting up the deployment process. No need of creating the S3 bucket first, travis-ci creating it automatically when specifying in the yml.  
- AWS Elastic Beanstalk — It is basically an incredibly easy tool of AWS to deploy applications on the cloud in a matter of seconds without the need of setting up machines manually.   
  Travis is going to be deploying our server side code on it only.   
  https://aws.amazon.com/elasticbeanstalk/  
- AWS IAM — By the use of IAM, we will get some credentials which will add in our .travis.yml and hence, whenever travis would try to deploy our code on AWS, AWS will know that it’s authorized to do so because of the credentials.    
- AWS Cloudformation - https://aws.amazon.com/cloudformation/ - It is the easier way for provisioning the AWS EB since we are already in the AWS ecosystem and travis-ci has easily integration for it.  
  https://docs.travis-ci.com/user/deployment-v2/providers/cloudformation/  
  
## So, what’s going to be the flow of our CI/CD pipeline  
1). A commit and push happens at GitHub:   
Here you will find all the code and configuration files:    
https://github.com/TalHibner/tikal-provisioning  
  
First, cloning the github repository so you can change the code and then commit & push to trigger travis-ci:  
- open a terminal inside your nodejs project directory.  
- Enter 'git init'  
- Then, Enter 'git remote add origin https://github.com/TalHibner/tikal-provisioning.git'.  
- Enter 'git add' . This will make all your files in the project directory (apart from gitignored files) ready for a commit.  
- Enter 'git commit . -m “first commit”' This commits your files and tells git about the new changes in the files.  
- Finally, enter 'git push origin master' This pushes and uploads your code on GitHub.  
  
2). Travis detects that code change on GitHub, gets triggered and sends the code for testing => Continuous Integration  
You can see it online here:   
https://app.travis-ci.com/github/TalHibner/tikal-provisioning    
(There is a stage of testing of yml linting and later the deployment - the next part)  
3). After the testing is done, Travis CI can automatically deploy files to AWS CloudFormation after a successful build .travis.yml which we are going to setup => Continuous Deployment  
For tests and example you can find some screenshots in the screenshots folder  
For documentation see : https://docs.travis-ci.com/user/deployment-v2/providers/cloudformation/  
   
4). And Boom! We will have our server on cloud up and running! :D  
See it here:  
http://helloworld-env.eba-ikpnwypc.us-east-1.elasticbeanstalk.com/   
We will even get an email alert for ERROR or PASS!  
  
** Note: You can run "Restart build" in the right up corner of the failed job if needed.  
  
### How I implemented it?  
Step 1).  
Setting up an AWS Elastic Beanstalk where our code is going to get deployed.  
Writing here AWS Cloudformation template.yml since it integrates easily with other AWS services and also travis-ci as integration with it.   
Step 2).   
Setting up an IAM user with the privilege of accessing and controlling the AWS Elastic Beanstalk Instance and more.  
Step 3).  
Writing the .travis.yml in which we will specify the name of our AWS Cloudformation template and stack name, the access keys associated with the IAM user we created.  
  
### Setting up the AWS IAM User  
  
- Go to AWS Management Console and search for “IAM”.  
- Click on the “Users” tab/option on the left drawer.    
- Click on the blue button saying “Add user”.  
- Enter any username and check the “Programmatic Access” checkbox.  
- Then, click on the “Next: Permissions” button.  
- Click on the “Attach existing policies directly” tab.  
- Search for “AdministratorAccess-AWSElasticBeanstalk”. The following role provides the permissions to the full access to AWS ElasticBeanstalk to the Travis-ci IAM user.  
- Add also "IAMFullAccess", "AmazonS3FullAccess" and "AWSCloudFormationFullAccess" to the full access to AWS IAM/S3/CloudFormation to the Travis-ci IAM user.  
- No need to add tags here, so, click on “Next: Review”.  
- Review the things you did and click on “Create user” button.  
- Click on the “Download .csv” button. That will download the csv containing the “Access Key ID” and “Secret Access Key” and save that .csv securely because AWS won’t ever provide the next opportunity to get these credentials.  
- These credentials will be used in setting up the deployment module in .travis.yml file.  
PS: these credentials are not meant to be shared at all with anyone if that wasn’t conveyed.  
  
It is very important to not enter access key and secret access id directly as a plain text in the .travis.yml file because it might be visible to other people.   
So, a good strategy I used here is to specify environment variables in travis-ci.com under both repo and then, write $<the variable name> against access_key and secret_access_key_id :  
- Go to each repo  - in the up right click the "More options" -> "Settings"  
  For example https://app.travis-ci.com/github/TalHibner/tikal-sample-app-travis-ci/settings   
- Then under "Environment Variables" enter the vars in NAME and the secret in VALUE and click "Add".  
