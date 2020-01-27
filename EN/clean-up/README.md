------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Clean up

Follow the instructions below. Deletion target is described for each Lab.    
**If you have not completed this work, you will continue to be charged.**

## Lab6

1. Glue job (job name: minilake1)

2. Remove crawlers from Glue (crawler name: minilake-in1, minilake-out1, minilake-out2)  

3. Delete tables from Glue (table name: minilake\_out1, minilake\_out2)  

4. Delete database from Glue (database name: minilake)  


## Lab5

1. Remove QuickSight VPC connection

2. Unsubscribe QuickSight (Click the account name of QuickSight　→ [Manage QuickSight] → [Account settings] → [Unsubscribe]）

3. Delete S3 bucket (bucket name: [S3 bucket name created by you])

4. Delete Kinesis Firehose delivery stream (stream name: minilake1)

5. Delete a Redshift cluster

6. Delete table from Glue (table name: ec2log\_external)

7. Delete database from Glue (database name: spectrumdb)

8. Remove manually added rule (access from qs-rs-private-conn) for inbound rule of handson-minilake-private security group

9. Delete the security group qs-rs-private-conn

10. Removed “handson-minilake-private-subnet” stack from CloudFormation console.


## Lab4

 1. Unsubscribe QuickSight (Click the account name of QuickSight　→ [Manage QuickSight] → [Account settings] → [Unsubscribe]）

 2. Delete S3 bucket (bucket name: [S3 bucket name created by you])

 3. Delete Kinesis Firehose delivery stream (stream name: minilake1)

 4. Remove a crawler from Glue (crawler name: minilake1)

 5. Delete tables from Glue (table name: minilake\_in1, minilake\_out1, ec2log\_external)

 6. Delete databases from Glue (database name: minilake, spectrumdb)


## Lab1〜3 

 1. Delete Cloudwatch Logs (under log group name)
  - Tokyo region
      - /minilake_group  
      - /aws/lambda/LogsToElasticsearch_handson-minilake
      - /aws/kinesisfirehose/minilake1
      - Streams related to "minilake" under /aws-glue/crawlers/
      - The streams of /aws-glue/jobs/error and /aws-glue/jobs/output are created for each Job ID. If you haven't used Glue before, delete the entire log group.

 2. Delete Lambda (function name: LogsToElasticsearch-handson\_minilake)

 3. CloudWatch alarm (alarm name: minilake_errlog)

## Lab1〜Lab2

 1. Delete Amazon Elasticsearch Service (domain name: handson-minilake)

 2. Delete a stack with CloudFormation (deletion of EC2, EIP and so on)

 3. Delete an IAM role (role name: handson-minilake)

That's all for cleaning up.
