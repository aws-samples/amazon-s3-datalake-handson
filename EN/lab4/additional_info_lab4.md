------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Additional guidance of query execution in Athena
## Execution example when data has accumulated for a while

 1. Execute the following SQL in the query editor.

 ```
 SELECT * FROM "minilake"."minilake_in1";
 ```
 
 **[Execution result example]**
 
 ```
 (Run time: 4.66 seconds, Data scanned: 27.02 MB)
 ``` 

 You can confirm that the execution time and the amount of scanned data increase, according with the amount of data.


 2. Try running a query with a Where clause.

 ```
 SELECT * FROM "minilake"."minilake_in1" where partition_0 = '2019' AND partition_1 = '09' AND partition_2 = '27' AND partition_3 = '14';
 ```

 **[Execution result example]**
 
 ```
 (Run time: 2.46 seconds, Data scanned: 284.42 KB)
 ``` 

  **Note：** Enter the date of the Where clause that has data.
  

 Similar to 1, you can confirm that the execution time and the amount of scanned data increase, according with the amount of data.



**Athena charges you for the amount of data scanned, so if you don't scan volumes of data, you can keep costs low. In many cases, small scans can improve performance.**  
 Use partition, compression, and columnar formats to reduce the amount of reading. Click [here](https://aws.amazon.com/jp/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/) for reference information.


