# cicd-demo
•	Connect to your EC2 instance and run the command “ssh-keygen -t rsa -b 4096”
•	Run the following command to display the contents on the public key file: “cat ~/.ssh/id_rsa.pub”.
•	Copy the output into your buffer.
•	On the console, navigate to the IAM service. Under security credentials for the user, select upload SSH public key.
•	Paste what you have in the space provided and upload.
•	After the upload, copy the SSH ID generated.
•	Go back to your EC2 instance.
•	In your local ~/.ssh directory, add the following lines to the config file there. If it’s not present, create one. 
•	Give it read write permissions by running chmod 600 ~/.ssh/config
o	Host git-codecommit.*.amazonaws.com
o	User “your SSH ID”
o	IdentityFile ~/.ssh/id_rsa
•	Navigate to the CodeCommit service. 
•	Select the repo you want to create and choose clone with SSH.
•	One the EC2 istance, run git clone “paste the URL”
We now have to set up the Codebuild job:
•	Create an S3 called Application-xashy bucket to be used for storing the CodeBuild artifacts.
•	On the management console, search and navigate to the CodeBuild service.
•	Choose “Create project.”
•	Enter “Application-xashy” in the project name.
•	For Source provider, select “AWS CodeCommit”
•	Repository name is “Application-xashy”.
•	Choose “Use a buildspec file” in the build specifications.
•	Click on create project.
•	Now, select the code build project just created and choose “Start build.”
•	When the build is complete, examine the logs. Also, go to the S3 bucket you created and find the artifacts.
We move to the CodeDeploy Job:
•	Create an IAM role that give EC2 access to S3.
•	Launch an EC2 instance and tag the instance with “Application = xashy”.
•	Attach the IAM role above to the instance.
•	Connect to that instance.
•	Run the following commands:
o	sudo yum update -y
o	sudo yum install -y ruby wget httpd
o	wget https://aws-codedeploy-us-east-2.s3.us-east-2.amazonaws.com/latest/install
o	chmod +x ./install
o	sudo ./install auto
o	sudo service codedeploy-agent status.
•	Go to IAM and select the create a role option.
o	Choose CodeDeploy for the use-case and select “CodeDeploy”.
o	Leave everything set to default. Name the role “Application-xashy” and create it.
•	On the console, navigate to the CodeDeploy service.
•	Click on create application.
•	For application name, enter “Application-xashy”.
•	Choose EC2/On-premises as the compute platform and click create.
•	Select “create deployment group”.
•	Name the deployment group as “Application-xashy”
•	In place of service role, give the IAM role you create above.
•	In the Environment configuration section, select Amazon EC2 instance, click on add tags.
•	Input the tags you set on the EC2 instance.
•	Un-check load balancer.
•	Create the deployment group.
•	Now, choose “Create deployment”.
•	Deployment group is Application-xashy.
•	Revision type is S3.
•	Copy and paste the URL.
•	Leave everything set to default and click on “create deployment”.
We now configure CodePipeline:
•	Navigate to the CodePipeline service and choose create pipeline.
•	For pipeline name, enter Application-xashy
•	Choose new service role
•	Click advanced settings and use the codepipeline default s3 bucket. You also have the option to put in the URI of your S3 artifact. To troubleshoot any errors, use this link:https://andrew.hawker.io/dailies/2020/02/24/aws-codepipelines-source-access-denied/
•	Use the default AWS Managed key for encryption.
•	Click next.
•	Source provider is CodeCommit. Supply the repository and branch name.
•	Allow cloudwatch monitor and start the pipeline.
•	Leave output artifact format as default and click next.
•	Build provider is CodeBuild.
•	Give the build project name and choose single build.
•	Deploy provider is CodeDeploy.
•	Input the application name and deployment group name.
•	Choose next, review your settings, and create.
Now on your EC2 instance, clone your CodeCommit repository, add the files, push them, and watch the pipeline.
