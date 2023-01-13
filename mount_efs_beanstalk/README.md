
##  Mount the efs storage to the elastic beanstalk running on an aws AMI version 2 ec2 instance using hooks (hooks is the new feature in AMI v2)

## Background:
trying to mount efs volume on aws AMI v1 will work fine, but when you try to mount it to AMI v2, 
you may get a lot of errors below.

-'ERROR: Mount command failed!'

-2022-07-22 16:51:24,628 P5115 [INFO] /tmp/mount-efs.sh: line 3: /opt/elasticbeanstalk/bin/get-config: No such file or directory 

-2022-09-12 14:43:16,304 [ERROR] Error encountered during build of prebuild_0_my_app: Command 01_mount failed Traceback (most recent call last): File /usr/lib/python3.7/site-packages/cfnbootstrap/construction.py, line 579, in run_config

## Solution:
I have found no guide on the internet or clear steps in aws documentation to mount efs drive on AWS AMI v2 EC2 instance.
I have implemented and tested this method and it is working perfectly fine on AWS AMI v2.

## Final goal:
You have a persistent, dedicated, external storage directory mounted inside the root of your application for any use!

## Steps:
1. Create a local directory in your application source code root called 'my_efs'

2. Create an EFS file system from the aws EFS dashboard, select the default VPC, choose the subnets and the security group.

3. To allow connections, edit the security group rules for the file system. The rules must allow inbound connections on port 2049 (Network File System or NFS) from the security group for instances in your Elastic Beanstalk environment.

4. Update the instance security group to allow outbound connections on port 2049 to the Amazon EFS security group.

5. In my setup, I have both the ec2 instance, and the efs on the same region and same availability zone.

6. Edit the 'env_var.config' with your <EFS_FILE_SYSTEM_ID> and <EFS_MOUNT_DIR> values

7. Make sure you have the script mount-efs.sh' is inside '.platform/hooks/postdeploy' at the root of your elasticbeans's project root directory. No need to alter anything in this script.

8. Deploy the application, and you have read/write access to the folder 'my_efs' inside the root of your application, where the content of 'my_efs' dir actually resides in your EFS persistent storage on AWS.

9. The application will access 'my_efs' folder normally as if it is located on the same machine.

10. For elasticbeans application, you can find the app dir in the ec2 in this location '/var/app/current/' and the 'my_efs' folder will be in '/var/app/current/my_efs/'

Thank you,
### by: Mohammad Hamdy Oreaba
https://linkedin.com/in/oreaba