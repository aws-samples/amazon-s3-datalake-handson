------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Addendum: Installing Fluentd

AWS CloudFormation launches EC2 from an AMI with the following steps in place:.
If you want to install Fluentd on any EC2 instance, do the following:.

 1. Log in to EC2 and install redhat-lsb-core and gcc.
   
    **Note：** This step can be skipped because it is already installed on a prepared AMI.
    **Asset**：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)

 ```
 $ sudo su -
 # yum -y install redhat-lsb-core gcc
 ```

 2. Install td-agent.

    **Asset**：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)
    
 ```
 # rpm -ivh http://packages.treasuredata.com.s3.amazonaws.com/3/redhat/6/x86_64/td-agent-3.1.1-0.el6.x86_64.rpm
 ```

 3. Modify the **TD\_AGENT\_USER** specification in line 18 of **/etc/init.d/td-agent** from **td-agent** to **root**.
 
    **Note：** The command example uses the vi editor, but if you are familiar with another editor, use it.
    **Asset**：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)

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

 4. Set up Fluentd to start automatically. (The actual startup will take place later.)

    **Asset**：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)

 ```
 # chkconfig td-agent on
 ```
