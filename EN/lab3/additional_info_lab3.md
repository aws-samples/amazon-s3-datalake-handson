------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Alert settings in Elasticsearch Service

Amazon Elasticsearch Service (Elasticsearch Service) has started supporting event monitoring and alerts. Alerts are available for domains running Elasticsearch 6.2 or later. Click [here](https://aws.amazon.com/about-aws/whats-new/2019/04/amazon-elasticsearch-service-adds-event-monitoring-and-alerting-support/) for details. 
This page introduces alert notification settings using Amazon Simple Notification Service (Amazon SNS).

 1. Select **Simple Notification Service** from the list of services in the AWS Management Console, select **[Topics]** from the left pane, and click **[Create topic]**.

 2. On the **[Create topic]** screen, enter "**handson-minilake** (optional)" as the name and click **[Create topic]** to create the topic. For information on creating an endpoint subscription for a topic, see [here](https://docs.aws.amazon.com/sns/latest/dg/sns-getting-started.html) for registration.

 3. To add a policy to the created "**handson-minilake** (optional)" IAM role, select **IAM** from the list of services in the AWS Management Console, select **[Roles]** and click the role name of "**handson-minilake** (optional)".

 4. Select the **[Permissions]** tab and click **[Attach policies]**.

 5. Using search tool, check the **[AmazonSNSFullAccess]** policy and click **[Attach policy]**.

 6. Click the **[Trust relationships]** tab and click the **[Edit trust relationship]** button.
 
 7. On the **[Edit Trust Relationship]** screen, add **es** to the location of **”Service”: “ec2.amazonaws.com”**. Add **es.amazonaws.com** with "**[]**" and "**,**" and click **[Update Trust Policy]**.
   
 **[Example]**
 
 ```
 {
 		"Version": "2012-10-17",
 		"Statement": [
			{
      		"Effect": "Allow",
      		"Principal": {
        		"Service": [             
          		"glue.amazonaws.com", 	　
          		"ec2.amazonaws.com"
        		]                        
      		},
      		"Action": "sts:AssumeRole"
    		}
  		]
}
 ```

8. Open **Kibana** and select **[Alerting]**. Select the **[Destinations]** tab and select **[Add Destination]**.

9. On the **[Add Destination]** screen, enter the following and click **[Create]**.

 - Name：sns-handson-minilake (optional)
 - Type：Amazon SNS
 - SNS topic ARN：ARN of SNS topic used for notification
 - IAM role ARN：ARN of the role "handson-minilake (optional)"

10. Next, configure the monitoring settings. Select **[Monitors]** and click **[Create monitor]**.

11. Input in **[Configure Monitor]** is as follows.

 - Monitor name：monitor-handson-minilake (optional)
 - Frequency：By interval (default)
 - Every：3 Minutes
   
     **Note：** If you set interval every minutes, data transfer from CloudWatch Logs to Elasticsearch Service may be delayed. So, please set it every 3 minutes.


12. Enter in **[Define Monitor]** as follows and click **[Create]** at the bottom of the screen.
 
 - How do you want to define the monitor?：Define using extraction query
 - Index：Select "cwl-*"
 - Define extraction query：Copy contents of [3-define-extraction-query.txt](asset/ap-northeast-1/3-define-extraction-query.txt)
 
13. In order to move to the **[Create Trigger]** screen, set the trigger with **[Define Trigger]**. Enter as follows.
 
 - Trigger name：trigger-handson-minilake (optional)
 - Severity level：1 (default)
 - Execution query response：Not change
 - Trigger condition：Change to 50 
  ```
   ctx.results[0].hits.total > 50
  ```
 
14. In **[Configure Actions]**, enter the following and click **[Create]** at the bottom of the screen to create it.
 
 - Action name：action-handson-minilake (optional)
 - Destination name：sns-handson-minilake – (Amazon SNS)
 - Message subject：alert-handson-minilake (optional)
 
15. Since we set our system to generate 300 ERRORs every 10 minutes, you can see that an alert has been triggered after about 10 minutes.
