------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Lab1：Getting started
Build a common environment prerequisite  to the following five labs.
Amazon VPC (VPC) and Amazon EC2 (EC2) are built and the permission is configured appropriately in AWS IAM (IAM), using AWS CloudFormation (CloudFormation). Then we will manually install the Fluentd; the log collection software.

## Section1：Before you start
### Step1：Log in to the AWS Management Console

 1. Log in to the AWS Management Console. After logging in, confirm that **[Tokyo]** is set in the region selection in the header section at the top right of the screen.

    **Note：** If it is not **[Tokyo]**, please change to **[Tokyo]**.

 2. Select **EC2** from the list of services in the AWS Management Console. From the left pane of **[EC2 Dashboard]**, click **[Key Pairs]**, click the **[Create Key Pair]** button, enter any value for **[Key pair name]** (Example: handson) and click **[Create]**. The private key (Example: handson.pem) is downloaded to your PC.  
   **Note：** If you are using an existing key pair, skip this step.

## Section2：EC2 environment
### Step1：Launch an EC2 instance with CloudFormation

Use CloudFormation to create a VPC and launch an EC2 instance which continuously outputs logs to the VPC. About 10 logs appear every 2 minutes, and 300 error logs appear every 10 minutes.

 1. Select **CloudFormation** from the list of services in the AWS Management Console.  

    **Note：** If you cannot find CloudFormation, enter a part of the word, such as “cloudform” in the search window and select it.
  
 2. On the **[CloudFormation]** dashboard, click **[Create stack]** at the top right of the dashboard. 
 
 3. On the **[Create stack]** screen, select **[Template is ready]** in **[Prerequisite - Prepare template]**. 

    **Note：** If it is selected by default, proceed to the next, leaving the default as it is.
 
 4. Then, in **[Specify template]** of **[Create stack]** screen, select **[Upload a template file]**, click **[Choose file]**, specify the downloaded template "**1-minilake_ec2.yaml**" and click **[Next]**. 

    **Asset** resource：[1-minilake_ec2.yaml](asset/ap-northeast-1/1-minilake_ec2.yaml)
  
 5. Specify "**handson-minilake** (optional)" as **[Stack name]**, and "**handson.pem** (optional)" which was created in **Section1** as **[KeyPair]** or the existing keypair name if you already have it. Then, click **[Next]**.  
 
 6. In the optional **tag**, enter "**Name**" for **Key** and "**handson-minilake** (optional)" for **Value**, then click **[Next]**.
 
 7. Review the contents of the final confirmation page and click **[Create stack]**. After a few minutes, EC2 launches and log starts to output in **/root/es-demo/testapp.log**.  
 
 8. Log in to EC2 **with SSH and switch to root**. Then, you can check the logs appearing every 2 minutes.
 
    **Note：** See [here](additional_info_lab1.md#EC2へのログイン方法) for EC2 login instructions. For the IP address information of the EC2 connection destination, select the appropriate CloudFormation stack from the **[CloudFormation]** dashboard and click the **[Outputs]** tab. **[AllowIPAddress]** section have IP address information.

 ```
 $ sudo su -
 # tail -f /root/es-demo/testapp.log
 ```
 
 **[Log output example]**

 ``` 
[2019-09-16 15:14:01+0900] WARNING prd-db02 uehara 1001 [This is Warning.]
[2019-09-16 15:14:01+0900] INFO prd-db02 uehara 1001 [This is Information.]
[2019-09-16 15:14:01+0900] INFO prd-web002 uchida 1001 [This is Information.]
[2019-09-16 15:18:01+0900] INFO prd-ap001 uehara 1001 [This is Information.]
[2019-09-16 15:18:01+0900] ERROR prd-db02 uchida 1001 [This is ERROR.]
 ```
 
### Step2：Create an IAM role and attach it to EC2

Create an IAM role for EC2 to use in the remaining labs.  

**Note：** If you are connecting an EC2 instance with AWS Systems Manager Session Manager (Session Manager), you can skip this step because there is an IAM role created in the process of setting the Session Manager .

 1. Select **IAM** from the list of services in the AWS Management Console and click **[Roles]** from the left pane of the **[Identity and Access Management (IAM)]** dashboard.
 
 2. Click **[Create role]**.
 
 3. Select **[AWS Service]**, select **[EC2]**, and click **[Next: Permissions]**.
 
 4. On the **[Attach permission policies]** screen, click **[Next: Tags]** without changing anything.  

    **Note：** At this step, you create a role without any policy. If you use Session Manager, only **AmazonEC2RoleforSSM** is attached.

 5. On the **[Add tags (optional)]** screen, just click **[Next: Review]**.
 
 6. Enter "**handson-minilake** (optional)" for **[Role name]** and click **[Create role]**.
 
 7. Select **EC2** from the list of services in the AWS Management Console, click **[Instances]** from the left pane of the **[EC2 Dashboard]** screen, select the instance **handson-minilake** (optional)" and click **[Actions] → [Security] → [Modify IAM role]**.
 
 8. On the **[Modiry IAM role]** screen, select "**handson-minilake** (optional)" for **[IAM role]** and click **[Save]**.
  

### Step3：Install Fluentd

Log in to EC2, install the log collection software, Fluentd, and configure the settings.

 1. Log in to EC2 and install redhat-lsb-core and gcc.  
   
    **Note：** This step can be skipped if you used CloudFormation template in Step1 since the prepared AMI which has these components already installed to.  
    **Asset** resource：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)

 ```
 $ sudo su -
 # yum -y install redhat-lsb-core gcc
 ```

 2. Install td-agent.

    **Asset** resource：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)
    
 ```
 # rpm -ivh http://packages.treasuredata.com.s3.amazonaws.com/3/redhat/6/x86_64/td-agent-3.1.1-0.el6.x86_64.rpm
 ```

 3. Modify the 18th line of **/etc/init.d/td-agent** from **td-agent** to **root**.  
 
    **Note：** The command example uses the vi editor, but if you have another editor you are familiar with, please feel free using the tool of your choice.  
    **Asset** resource：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)

 ```
 # vi /etc/init.d/td-agent
 ```

 **[Before change]**
 
 ```
 TD_AGENT_USER=td-agent
 ```
 
 **[After change]**
 
 ```
 TD_AGENT_USER=root 
 ```

 4. Set Fluentd to start at the system starting time. (The procedures of manually  starting up Fluentd will be covered later in the lab.)

    **Asset** resource：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)

 ```
 # chkconfig td-agent on
 ```
 

That's it for Lab1. Try the following procedure according with the path you have selected.

（1） Implementation of near real-time data analysis environment (speed layer)：[Lab1](../lab1/README.md) → [Lab2](../lab2/README.md) → [Lab3](../lab3/README.md)  
（2） Implementation of an environment for batch analysis of long-term data (batch layer) and optimization of performance and cost：[Lab1](../lab1/README.md) → [Lab4](../lab4/README.md) or [Lab5](../lab5/README.md) → [Lab6](../lab6/README.md)  
（3） All labs：[Lab1](../lab1/README.md) → [Lab2](../lab2/README.md) → [Lab3](../lab3/README.md) → [Lab4](../lab4/README.md) → [Lab5](../lab5/README.md) → [Lab6](../lab6/README.md) 

Please follow [these instructions](../clean-up/README.md) when deleting an environment.