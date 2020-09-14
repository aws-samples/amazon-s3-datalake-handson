------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Elasticsearch Service でのアラート設定

Amazon Elasticsearch Service（以下、Elasticsearch Service）で、イベント監視およびアラートのサポートを開始しました。アラートは、 Elasticsearch 6.2 以降を実行しているドメインで利用可能です。詳細は[こちら](https://aws.amazon.com/jp/about-aws/whats-new/2019/04/amazon-elasticsearch-service-adds-event-monitoring-and-alerting-support/) を参照ください。 
本資料では、 Amazon Simple Notification Service（以下、Amazon SNS）を用いたアラート通知設定を紹介します。  

 1. AWS マネジメントコンソールのサービス一覧から **Simple Notification Service** を選択し、左ペインから **[トピック]** を選択し、 **[トピックの作成]** をクリックします。

 2. **[トピックの作成]** 画面で、名前に「 **handson-minilake** （任意）」と入力し、 **[トピックの作成]** をクリックし、トピックを作成します。トピックに対するエンドポイントのサブスクリプションの作成については[こちら](https://docs.aws.amazon.com/ja_jp/sns/latest/dg/sns-getting-started.html)を参照し、登録を行います。

 3. 作成済の「 **handson-minilake** （任意）」の IAM ロールにポリシーを追加するために、 AWS マネジメントコンソールのサービス一覧から **IAM** を選択し、 **[ロール]** をクリックし、「 **handson-minilake** （任意）」のロール名をクリックします。

 4. **[アクセス権限]** タブを選択し、 **[ポリシーのアタッチ]** をクリックします。

 5. 検索などを使いながら、 **[AmazonSNSFullAccess]** のポリシーにチェックを入れ、 **[ポリシーのアタッチ]** をクリックします。

 6. **[信頼関係]** タブをクリックし、 **[信頼関係の編集]** ボタンをクリックします。
 
 7. **[信頼関係の編集]** 画面において、**”Service”: “ec2.amazonaws.com”** の箇所に **es** を追記します。 **[]**でくくり、 **カンマ** で区切り、 **es.amazonaws.com** を追記し、**[信頼ポリシーの更新]** をクリックします。
   
 **[記入例]**
 
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

8. **Kibana** を開き、 **[Alerting]** を選択します。**[Destinations]** タブを選択し、 **[Add Destination]** を選択します。

9. **[Add Destination]** 画面で、下記の通り入力し、 **[Create]** をクリックし作成を選択します。

 - Name：sns-handson-minilake（任意）
 - Type：Amazon SNS
 - SNS topic ARN：通知に使用するSNSトピックのARN
 - IAM role ARN：handson-minilake（任意）ロールのARN

10. 続いて、監視の設定を行います。 **[Monitors]** を選択し、**[Create monitor]** をクリックします。

11. **[Configure Monitor]** 内の入力については、下記の通り行います。

 - Monitor name：monitor-handson-minilake（任意）
 - Frequency：By interval（デフォルト）
 - Every：3 Minutes
   
     **Note：** 1分毎とした場合、 Elasticsearch Service への CloudWatch Logs の情報流し込みが多少遅延する可能性があるため、3分毎としています。


12. **[Define Monitor]** 内の入力については、下記の通り行い、画面下部の **[Create]** をクリックします。
 
 - How do you want to define the monitor?：Define using extraction query
 - Index：cwl-* を選択
 - Define extraction query：[3-define-extraction-query.txt](asset/ap-northeast-1/3-define-extraction-query.txt)ファイルの内容をコピー
 
13. **[Create Trigger]** の画面に遷移するため、**Define Trigger** にて、トリガーの設定を行う。下記の通り入力する。
 
 - Trigger name：trigger-handson-minilake（任意）
 - Severity level：1（デフォルト）
 - Execution query response：変更しない
 - Trigger condition：50に変更  
  ```
   ctx.results[0].hits.total > 50
  ```
 
14. **[Configure Actions]** にて、下記の通り入力し、画面下部の **[Create]** をクリックし、作成します。
 
 - Action name：action-handson-minilake（任意）
 - Destination name：sns-handson-minilake – (Amazon SNS)
 - Message subject：alert-handson-minilake（任意）
 
15. 10 分ごとに ERROR が 300 件出る設定になっていますので、10 分程度経過すると、アラートがトリガーされていることが確認できます。
