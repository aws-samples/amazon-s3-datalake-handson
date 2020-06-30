------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# 後片付け

下記、後片付けを行います。それぞれの Lab 単位で削除対象を記述しています。    
**この作業まで完了していない場合、継続して課金が発生しますので、必ず実施してください。**

## Lab6

1. Glue のジョブ（ジョブ名:minilake1）  

2. Glue からクローラの削除（クローラ名:minilake-in1、minilake-out1、minilake-out2）  

3. Glue からテーブルの削除（テーブル名:minilake\_out1、minilake\_out2）  

4. Glue からデータベースの削除（データベース名：minilake）  


## Lab5

1. QuickSight の VPC 接続を削除

2. QuickSight の Unsubscribe（QuickSight のアカウント名をクリック　→ [QuickSight の管理] → [アカウント設定] → [サブスクリプション解除]）

3. S3 バケットの削除（バケット名: [ご自身で作成されたS3バケット名]）

4. Kinesis Firehose の delivery stream の削除（ストリーム名:minilake1）

5. Redshift クラスターの削除

6. Glue からテーブルの削除（テーブル名:ec2log_external）

7. Glue からデータベースの削除（データベース名:spectrumdb）

8. handson-minilake-private セキュリティグループのインバウンドルールに対して、手動で追加したルール（qs-rs-private-conn からのアクセス許可）を削除

9. セキュリティグループの qs-rs-private-conn の削除

10. CloudFormation コンソールから、「handson-minilake-private-subnet」スタックを削除。


## Lab4

 1. QuickSight の　Unsubscribe（QuickSight のアカウント名をクリック　→ [QuickSight の管理] → [アカウント設定] → [サブスクリプション解除]）

 2. S3 バケットの削除（バケット名: [ご自身で作成された　S3 バケット名]）

 3. Kinesis Firehose の　delivery stream の削除（ストリーム名:minilake1）

 4. Glue からクローラの削除（クローラ名:minilake1）

 5. Glue からテーブルの削除（テーブル名:minilake\_in1、minilake\_out1、ec2log\_external）

 6. Glue からデータベースの削除（データベース名:minilake、spectrumdb）


## Lab1〜3 まで

 1. Cloudwatch Logs の削除（ロググループ名以下）  
  - 東京リージョン
      - /minilake_group  
      - /aws/lambda/LogsToElasticsearch_handson-minilake
      - /aws/kinesisfirehose/minilake1
      - /aws-glue/crawlers/の”minilake”関連ストリーム
      - /aws-glue/jobs/error、 /aws-glue/jobs/outputのストリームはジョブIDごとに作成されます。Glueをいままで使っていなければロググループごと消してください。

 2. Lambda の削除（Function 名:LogsToElasticsearch-handson_minilake）

 3. CloudWatch アラーム（アラーム名:minilake_errlog）

## Lab1〜Lab2

 1. Amazon Elasticsearch Service の削除 （ドメイン名：handson-minilake）

 2. CloudFormation でスタック削除（EC2 や　EIP の削除）

 3. IAM ロールの削除（ロール名：handson-minilake）
 
 4. （作成いただいた場合）キーペアの削除（キーペア名：handson）

後片付けは以上です。
