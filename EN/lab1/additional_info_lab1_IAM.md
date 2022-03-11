------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Addendum: Creating and Attaching an IAM Role to EC2

Steps to create an IAM role that gives EC2 permission to access AWS resources. AWS CloudFormation automates the following settings.

 1. In the AWS Management Console, select **IAM** from the list of services, and then select **[Roles]** from the left pane of the **[Identity and Access Management (IAM)]** screen.
 
 2. Click **[Create Role]**.
 
 3. Select **[AWS Services]**, select EC2, and then click **[Next]**.
 
 4. In the **[Add Permissions]** screen, click **[Next]** without making any changes. 

    **Note：** At this stage, you create the role without a policy. Only **[AmazonEC2RollforSSM]** is attached for Session Manager users.

 5. On the **[Name, review, and create]** screen, type **"handson-minilake (optional)"** for the role name, and then click Create Role.
 
 6. Select **[EC2]** from the service list in the AWS Management Console, select **[Instances]** from the left pane of the **[EC2 Dashboard]**, check the "**handson-minilake (optional)**" instance you just created, and click **[Actions]** > **[Security]** > **[Change IAM Role]**.
  
 7. In the **[Change IAM Role]** screen, select "**handson-minilake (optional)**" for IAM role, and click **[Save]**.
   
