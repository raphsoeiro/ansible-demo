This demo is intended to show users on how to quickly deploy and configure an EC2 instance using Ansible.

Here are the steps:

1 -> Create 2 IAM Roles: (for step one I recommend reading in the raw format)
  - Ansible role: 
       a. attach the AmazonEC2FullAccess managed policy;
       b. Create an inline policy (IAMLaunchRole) and allow the following actions:
        	{
   				 "Version": "2012-10-17",
  				 "Statement": [
       				 {
            			 "Sid": "Stmt1427757090000",
            			 "Effect": "Allow",
          			     "Action": [
               				 "iam:AddRoleToInstanceProfile",
             			     "iam:AttachRolePolicy",
				             "iam:GetRole",
				             "iam:ListRolePolicies",
				             "iam:ListRoles",
				             "iam:PassRole"
            			 ],
            			 "Resource": [
                			  "*"
            			 ]
        			 }
    			 ]
			}			
  
  - Bootstrapping role:
  		a. attach the S3ReadOnlyAccess managed policy.

2 -> Launch an Instance (t2.micro is fine) using the Demo AMI in one of the regions. (refer to the AMIs file)
  - Enable Auto-assign Public IP
  - Attach the Ansible role to the instance
  - Don't change storage
  - Tag if you want
  - Create a SG that allows SSH into the instance

3 -> Create new SG allowing SSH from the Ansible SG and HTTP from anywhere. This SG will be attached to the instances that Ansible will create and bootstrap.

4 -> Create a new key par (ansible) to be used with the instances being launche by Ansible (you can use an existing one as well). Upload the pem file to the Ansible server. Also chmod 400 pemfile

5 -> Log into the instance. in the home directory, you should see 2 files:
  - deploy.sh 
     - used to launch new instances and add their IPs to Ansible's inventory file. Please note that you will have to edit the script whenever you see USER ACTION
  - web.yml
  	 - Ansible's playbook file with our instructions. In this case install Apache, PHP, anc copy an php file to the server
  - in the /etc/ansible directory there should be a hosts file. If you see anything under [webservers], please delete the lines, before continuing

6 -> You will need to gather the following information: Amazon Linux ami-id of the region you are deploying /  key name / Bootstrapping sg-ig / subnet-id / region

7 -> Edit the deploy.sh, Lines 7 and 14, with the information gathered above.

8 -> run deploy.sh 'n' where n is the number of instances you want to launch

9 -> If eveything works as it's supposed to, you should see entries in the /etc/ansible/hosts file with the folling format:
[webservers]
instanceIP path_to_pem_file

10 -> check if the instances are running:  ansible webservers -m ping

11 -> Run the bootstrapping: ansible-playbook web.yml

12 -> Get the public IP address of the instance and open using a browser. 
