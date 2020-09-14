------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Lab2：Visualization of application logs in real time

In this Lab, we visualize the log data recorded in EC2 instance in real time. We use the EC2 instance we launched in “Lab1: Getting Started”. 
The log output from EC2 is sent to Amazon Elasticsearch Service (Elasticsearch Service) in a stream using Fluentd of OSS.
This functionality to visualize using Kibana that comes with Elasticsearch Service. 
These logs continuously output approximately 10 every 2 minutes, and 300 log entries every 10 minutes are sent to Elasticsearch Service using Fluentd. These logs are visualized with Kibana.

## Section1：Elasticsearch Service settings
### Step1：Launch Elasticsearch Service

 1. Select **Elasticsearch Service** from the list of services in the AWS Management Console and then click **[Create a new domain]**. 
 
 2. At **"Step 1: Choose deployment type"**, select **[Development and testing]** in **[Deployment type]**, don't change **[Version]** and then click **[Next]**.
 
    **Note：** If you select a version other than 6.8, the preconfigured dashboards in Kibana may not work properly. Select 6.8 and perform this Hands-on.
 
 3. At **"Step 2: Configure domain"**, input "**handson-minilake** (optional)" as **[Elasticsearch domain name]** in **[Configure domain]**, select **[t2.small.elasticsearch]** which is for free tier, leave the other settings unchanged, and click **[Next]** at the bottom right of the screen.
 
 4. At **"Step 3: Configure access and security"**, select **[Public access]** in **[Network configuration]**. 
 
 5. In **[Access policy]**, choose **[Custom access policy]** in **[Domain access policy]**, set up as follows and click **[Next]**.

   - Choose **[IPv4 address]** for **[Select Type]**, input **"** **\*** **"** for **[Enter Principal]** and select **[Allow]** for **[Select Action]**.

     **Note：** This configuration is not recommended for any production environment. For this hands-on, we do not set security measure strictly, due to the nature of hands-on dealing with no confidential data.   
 
 6. At **"Step 4: Review"**, confirm the contents and click **[Confirm]** at the bottom right of the screen. 

    **Note：** At this point, the launch process of Elasticsearch Service has started. It takes about 15 minutes to complete launching. Please proceed to the next step without waiting for completion.
 

## Section2：EC2, Fluentd, Elasticsearch Service settings
### Step1：IAM role settings

Add a policy to the created "**handson-minilake** (optional)" IAM role as follows.

 1. Select **IAM** from the list of services in the AWS Management Console, select **[Roles]** in the left pane of **[Identity and Access Management (IAM)]** dashboard, click the role name "**handson-minilake** (optional)".

 2. Select the tab **[Permissions]** and click **[Attach policies]**.

 3. Search with "**amazones**" in the search window, check **[AmazonESFullAccess]**, and click **[Attach policy]**.

 4. Click the name of the changed role again, select the **[Permissions]** tab, and confirm that **[AmazonESFullAccess]** is attached.


### Step2：Fluentd settings

Configure settings for sending log data from Fluentd to Elasticsearch Service.

 1. Select **Elasticsearch Service** from the list of services in the AWS Management Console, click the domain name "**handson-minilake** (optional)" in **[Amazon Elasticsearch Service dashboard]**, copy and paste the **URL string** in **[Endpoint]** without "**https://**" in a notepad on your computer.

 2. Log in to EC2 and install the Elasticsearch plugin.

    **Asset** resource：[2-cmd.txt](asset/ap-northeast-1/2-cmd.txt)

 ```
 $ sudo su -
 # td-agent-gem install -v 2.6.0 fluent-plugin-elasticsearch
 ```
 
 3. Confirm plugin installation.

    **Asset** resource：[2-cmd.txt](asset/ap-northeast-1/2-cmd.txt)
 
 ```
 # td-agent-gem list | grep plugin-elasticsearch
 ```
 
  **[Execution result example]**
  
 ```
 fluent-plugin-elasticsearch (2.6.0, 2.4.0)
 ```
 
 4. To change the setting of "**/etc/td-agent/td-agent.conf**", delete the contents of "**/etc/td-agent/td-agent.conf**" once. Open it with an editor such as vi and delete it with ":%d".
 
 ```
 # vi /etc/td-agent/td-agent.conf
 ```

 5. Copy the contents of "**2-td-agent.conf**" in **Asset** resource and paste them.

    **Asset** resource：[2-td-agent.conf](asset/ap-northeast-1/2-td-agent.conf)

 6. After pasting, modify some of the contents. Replace the value of **eshost** with the value of the endpoint copied in step 1 and save.  

    **Note：** Do not include "**https://**" as the value of **eshost**.
 
 **[Before change]**
  
 ```
 host eshost
 ``` 
 
 **[Example after change]**
  
 ```
 host search-handson-minilake-ikop2vbusshbf3pgnuqzlxxxxx.ap-northeast-1.es.amazonaws.com
 ``` 

 7. Start the td-agent process.
 
     **Asset** resource：[2-cmd.txt](asset/ap-northeast-1/2-cmd.txt)
 
 ```
 # /etc/init.d/td-agent start
 ```
 
 8. Check the Fluentd log.
 
     **Asset** resource：[2-cmd.txt](asset/ap-northeast-1/2-cmd.txt)
 
 ```
 # tail -f /var/log/td-agent/td-agent.log
 ```
 
   **Note：** Check if there are any errors in the log. If startup is successful, the following text will be in the output.
 
 ```
 [info]: #0 Connection opened to Elasticsearch cluster => {.....
 ```
It may take some time for you to be able to see the output.

### Step3：Elasticsearch Service settings

 1. Select **Elasticsearch Service** from the list of services in the AWS Management Console.  

 2. In **[Amazon Elasticsearch Service dashboard]**, confirm that **[Domain status]** of the created domain "**handson-minilake** (optional)" is **[Active]** and the number of **[Searchable documents]** is more than one, and then click the domain "**handson-minilake** (optional)".

 3. Select **[Actions]** and click **[Modify access policy]**. Select **[Custome access policy]** for **"Domain access policy"**, set up as follows and click **[Submit]**.

   - Choose **[IAM ARN]** for **[Select Type]**, input **"** **\*** **"** for **[Enter Principal]** and select **[Allow]** for **[Select Action]**.

 4. Click the URL to the right of **[Kibana]**.
 
 5. **[Welcome to Kibana]** screen appears. Select **[Explore on my own]** to open **Kibana** screen.

 #### Kibana settings

 6. Click ![kibana_management](images/kibana_management.png)icon from the left pane of the **Kibana** screen, and then click **[Index Patterns]**.

 7. Click **[Create index pattern]**, enter "**testappec2log-***" in **[Index pattern]** and click **[Next step]** on the right.

 8. In **[Time Filter field name]**, select **[@timestamp]** and click **[Create index pattern]** at the bottom right of the screen.

 9. Click ![kibana_management](images/kibana_management.png)icon from the left pane of the **Kibana** screen, and then click **[Saved Objects]**. Click **[Import]** at the top right of the screen.

 10. On the **[Saved Objects]** screen, click the **[Import]** icon, select "**2-visualization.json**" in **Asset** resource and click the **[Import]** icon. Then, at the next screen, select "**testappec2log-\***" and click **[Confirm all changes]** to complete importing. After importing without any errors, click **[Done]** to return to the original screen.

     **Asset** resource：[2-visualization.json](asset/ap-northeast-1/2-visualization.json)

 11. Next, on the **[Saved Objects]** screen, click the **[Import]** icon again, select "**2-dashboard.json**" in **Asset** resource, and click the **[Import]** icon to import. After importing without any errors, click **[Done]** to return to the original screen.

     **Asset** resource：[2-dashboard.json](asset/ap-northeast-1/2-dashboard.json)

 12. Click ![kibana_dashboard](images/kibana_dashboard.png)icon from the left pane of the **Kibana** screen, click the imported "**test1-dashboard**", and if the values ​​are displayed as shown below, you have completed this section.  

 <img src="images/kibana_capture01.png">  

 13. On the **Kibana** screen, you can select the time range in the upper right, so let's set the period to **[Last 1 hour]**. You can confirmthat the graph changes to show the acquired value for 1 hour.

 14. Click ![kibana_discover](images/kibana_discover.png)icon from the left pane of the **Kibana** screen, and click **[alarmlevel]** in the menu on the right to see the percentage for each alarm level. 

 15. Click **[add]** to the right of **[alarmlevel]**. Similarly, if you click **[add]** to the right of **[user]**, you can limit the view to only the target columns (Time, alarmlevel, user).  

     **Note：** **[add]** is displayed only when the cursor is on it.

 16. Enter "**user:"imai"**" in the search window and press Enter to filter by the user "**imai**".


## Section3：Summary

Searching through logs is exhausting task, if you have a large amount of EC2 instances. However, if logs from EC2 instances are sent to Elasticsearch Service as a stream and visualized with Kibana, it is easier to find the error log entry that you are looking for. This architecture make the task efficient.


<img src="../images/architecture_lab2.png">

That's it for Lab2. Try the following procedure according with the path you have selected.

（1） Implementation of near real-time data analysis environment (speed layer)：[Lab1](../lab1/README.md) → [Lab2](../lab2/README.md) → [Lab3](../lab3/README.md)  
（2） Implementation of an environment for batch analysis of long-term data (batch layer) and optimization of performance and cost：[Lab1](../lab1/README.md) → [Lab4](../lab4/README.md) or [Lab5](../lab5/README.md) → [Lab6](../lab6/README.md)  
（3） All labs：[Lab1](../lab1/README.md) → [Lab2](../lab2/README.md) → [Lab3](../lab3/README.md) → [Lab4](../lab4/README.md) → [Lab5](../lab5/README.md) → [Lab6](../lab6/README.md) 

Please follow [these instructions](../clean-up/README.md) when deleting an environment.