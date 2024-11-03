---
title: Install Python packages in AWS Lambda using Elastic File System
date: 2023-10-01 00:00:00 +0530
comments: true
categories: [aws, lambda]
tags: [aws]
math: true
---

This post is about installing packages in AWS Lambda using an Elastic File System. 

# Install Python packages in AWS Lambda using Elastic File System

### Definitions

1. Lambda - Service that enables you to use compute resources without having to launch or manage the underlying infrastructure - leveraged in serverless architectures.
2. EFS - Fully managed NFS file system designed for Linux workloads.
3. VPC - Logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define (the lambda, EFS and the EC2 instance you use to install the packages must lie in the same VPC)
4. Subnets - A range of IP addresses in your VPC. You can attach AWS resources, such as EFS filesystems, Lambda and EC2 instances to subnets.
    * EC2 instances are associated with a single subnet
    * EFS filesystems and Lambdas can work on a VPC across multiple subnets for higher availability
5. Security Groups - Firewall-like controls for instances within a VPC that controls access for inbound and outbound traffic.
6. EC2 - Service that provides secure and resizable virtual servers on AWS.

### Steps:
What we are going to do is mount an EFS to a lambda function. Both these services can be in the same private subnet. 
Seperately, we are going to mount the same filesystem on an EC2 instance on a public subnet so that we can install our required packages. This requires public access as we need to SSH into our EC2 instance, and also so that we can download and install python packages.
We also need to ensure that the Python version we use to install the packages is the same as the version running on Lambda. 


### Creating an IAM role
1. `IAM` > `Role` > `Create Role` to create a new role.
2. In `Trusted entity type` select `AWS service`.
3. In `Use case` select `Lambda`.
4. Next, you have to add permissions by choosing one or more policies to attach to your role. Attach the following : 
    * `AmazonElasticFileSystemClientFullAccess`
    * `AWSLambdaExecute`
    * `AWSLambdaVPCAccessExecutionRole`
5. Then you can name the role, review and create it.

### Creating a VPC with one private and one public subnet

1. `VPC` > `Your VPCs` > `Create VPC` to launch the VPC wizard.
2. In `Resources to create`, click on `VPC and more`. This will allow us to create multiple resources we need in one step, such as subnets, security groups, route tables and internet gateways.
3. You can give the VPC a name, for auto generating all the other resource names.
4. Create a VPC with 2 or more AZ's for higher availability. For this example, 1 will suffice.
    * this automatically creates one public and one private subnet (if you are using 2 AZ's, this will create two public and private subnets, and so on)
    * this also creates a default security group for the VPC, which applies on all the subnets in it.

### Creating a Security Group

1. `VPC` > `Security Groups` to view the security groups present.
2. You need two security groups, one for resources in your public subnets, and another for resources in your private subnet. Let us call them `sg-private` and `sg-public`.
3. A security group may already have been made for your new VPC. You can either :
    * edit the one already there, and create a new security group
    * make two new security groups 
4. Go to `VPC` > `Security Groups` > `Create security group` to create a new security group. Ensure you select the same VPC you created above. Create them with the following configurations.
3. `sg-public` will be the security group for the EC2 instance you will use to install the packages on the public subnet. So:
    1. for inbound rules:
        * `sgr-xx...xx`, `IPv4`, `SSH`, `TCP`, `22`, `0.0.0.0/0` (to allow us to SSH into the EC2 instance)
    2. for outbound rules:
        * `sgr-0d859b39b65d36b24`, `IPv4`, `All traffic`, `All`, `All`, `0.0.0.0/0` (to allow us to download packages, as well as mount the EFS)
4. `sg-private` will be the security group for the Lambda and EFS resources you will use on the private subnet. So:
    1. for inbound rules:
        * `sgr-xx...xx`, `–`, `NFS`, `TCP`, `2049`, `sg-private` (to allow the Lambda in the same security group to mount the EFS)
        * `sgr-xx...xx`, `–`, `NFS`, `TCP`, `2049`, `sg-public` (to allow the EC2 in the other security group to mount the EFS)
    2. for outbound rules:
        * `sgr-xx...xx`, `–`, `NFS`, `TCP`, `2049`, `sg-private` (to allow the Lambda in the same security group to mount the EFS)

### Create an Elastic File System 

1. `Amazon EFS` > `File systems` to click on `Create file system`.
2. Click on the `Customize` option in the popup that shows up, to get more configuration options.
3. On Step 1, `File system settings`, you can choose the default settings. If you have been following along, in the `Availability and durability` section choose `One Zone` and the correct AZ.
4. On Step 2, `Network access`, choose the correct VPC, subnet, and `sg-private` security group.
5. Click through Step 3 and 4 to create the file system.
6. On the created filesystem, open the `Access points` tab. 
7. Create an access point with the following example config:
    * Root directory path - `/packages`
    * User ID - `1000`
    * Group ID - `1000`
    * Owner user ID - `1000`
    * Owner group ID - `1000`
    * POSIX permissions to apply - `777`

### Creating a Lambda function

1. Go to `Lambda` > `Functions` > `Create function`. Enter the function name and the runtime you want.
2. Click on `Change default execution role` > `Use an existing role` and select the role you created above.
3. In `Advanced settings`, click on `Enable VPC`.
4. Select your VPC, private subnet and `sg-private` as the security group.
5. Click on `Create function` and wait for the function to get created.
6. In the `Configuration` tab, click on `Filesystem`. Select the filesystem and access point you had created earlier, then set the local mount path as `/mnt/packages`.
7. Now, the lambda should be connected to EFS. We are going to install packages in the `/mnt/packages` location. To allow this, we can set an environment variable in Lambda by going to `Configuration` > `Environment variables` and creating a variable with Key = `PYTHONPATH` and Value = `/mnt/packages`.
8. In the function `Code` tab, you can run this code to get the Python version. Note the version down for later use when installing the packages.
    ```python
    import sys
    print (sys.version) # in my case, 3.9.11
    ```
    

### Creating an EC2 instance

1. Go to `EC2` > `Instances` > `Launch an instance`. Select your chosen instance options. 
2. If you haven't created a key pair, create one. I create one by the name `efs-kp.pem`.
3. Click to edit the network settings. Select your VPC, private subnet and `sg-public` as the security group. Make sure `Auto assign public IP` is enabled.
4. Click to create the instance.

### Installing the packages 

1. Open the instance dashboard. Click on `Connect to instance`. Follow the instructions given in the `SSH client` tab to SSH into the instance.
2. Update the version of Python to the version in Lambda using the following steps:
    * Update your system with `sudo yum update -y`
    * Download the dependencies to build the package:
    ```
    sudo yum groupinstall "Development Tools" -y
    sudo yum install wget openssl-devel bzip2-devel libffi-devel
    ```
    * Download the required Python version 3.9.11:
    ```
    sudo curl https://www.python.org/ftp/python/3.9.11/Python-3.9.11.tgz -O
    ```
    * Extract the Python package: `tar -xvf Python-3.9.11.tgz`
    * Change into the Python directory: `cd Python-3.9.11`
    * Run the configuration script and run the build process:
    ```
    sudo ./configure --enable-optimizations
    sudo make install
    ```
    * Verify the installation after it's finished : `python3 -V`
4. Once you enter the root directory, create a virtual environment with `python3 -m venv venv`
5. Activate the virtual environment with `source venv/bin/activate`
6. Verify the python version with : `python -V`
7. Return to the root directory : `cd ..`
8. Create a new directory to mount into : `mkdir efs`
9. Go to the filesystem your created. Click on the `Attach` button. Copy the NFS command from the popup that opens up. Run this command in the terminal.
    ```
    sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-080005337xxxxxxxx.efs.us-east-1.amazonaws.com:/ efs
    ```
10. Your EFS filesystem is now mounted. Now (with the virtual env enabled) install the required packages in the `efs/packages/` directory with
    ```
    pip install --target=efs/packages/ pandas numpy
    ```
    
### Verifying installation on Lambda

1. Try running the following code on Lambda.
    ```python
    import json
    import sys
    import numpy as np
    import pandas as pd

    print (sys.version)

    print (np.__version__)
    print (pd.__version__)

    def lambda_handler(event, context):
        # TODO implement
        return {
            'statusCode': 200,
            'body': json.dumps('Hello from Lambda!')
        }
    ```
2. This should print the versions of numpy and pandas respectively.