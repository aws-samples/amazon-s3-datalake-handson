------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Lab3：アプリケーションログのリアルタイム可視化とアラーム
「Lab2：アプリケーションログをリアルタイムで可視化」で実施した可視化に加え、アラーム検知を実施します。
Fluentd から OpenSearch Service に送信する前段に Amazon CloudWatch（以降、CloudWatch）、 AWS Lambda（以降、Lambda）を配置して、アラーム通知をする処理を追加します。

## Section1：EC2 の設定変更
### Step1：IAM ロールの設定

作成済の「 **handson-minilake**（任意）」の IAM ロールに以下のようにポリシーを追加します。

 1. AWS マネジメントコンソールのサービス一覧から **IAM** を選択し、 **[Identity and Access Management (IAM)]** 画面の左ペインから **[ロール]** を選択し、「 **handson-minilake-role**（任意）」のロール名をクリックします。

 2. **[アクセス権限]** タブを選択し、 **[ポリシーをアタッチします]** をクリックします。

 3. 検索などを使いながら、 **[CloudWatchLogsFullAccess]** のポリシーにチェックを入れ、 **[ポリシーのアタッチ]** をクリックします。

 4. 変更実施したロールの **[アクセス権限]** タブを選択し、 **[CloudWatchLogsFullAccess]** がアタッチされたことを確認します。

 5. 次に **[信頼関係]** タブを選択し、 **[信頼関係の編集]** を押下し、ポリシーに以下を追加します。

 **[追加するポリシー]**

 ```
 "lambda.amazonaws.com"
 ```

 **[追加例]**

 ```
 {
   "Version": "2012-10-17",
   "Statement": [
     {
       "Effect": "Allow",
       "Principal": {
         "Service": [
           "ec2.amazonaws.com",
           "lambda.amazonaws.com"
         ]
       },
       "Action": "sts:AssumeRole"
     }
   ]
 }
 ```

 6. 次に **[信頼されたエンティティ]** に、 **[Lambda]** が追加されていることを確認します。

 7. あとで使用するため、 **[ロールARN]** の値をメモしておきます。

### Step2：OpenSearch へのロールの認証設定

1. **Kibana** の画面を開き、 **Kibana** の画面左にある![kibana_pain](images/kibana_pain2.png)アイコンをクリックし、 **[Security]** をクリックします。  

2. 左ペインにあるRolesを押下し、Role一覧の中にある **all_access** をクリックする。

3. **Mapped users** のタブを選択し、 **Manage mapping** を押下する。

4. **Backend roles** に先ほどメモした、 「**handson-minilake**（任意）」の **[ロールARN]** の値を入力し **Map** ボタンを押下する。


### Step3：Fluentd の設定

Fluentd から CloudWatch Logs にログデータを送信するための設定を行います。

 1. EC2 にログインし、プラグインのインストール状況を確認します。

    **Asset** 資料：[3-cmd.txt](asset/ap-northeast-1/3-cmd.txt)

 ```
 # td-agent-gem list | grep cloudwatch-logs
 ```

 **[実行結果例]**

 ```
 fluent-plugin-cloudwatch-logs (0.4.4)
 ```

 2. 「 **/etc/td-agent/td-agent.conf** 」の設定を **Lab3** 向けに変更するために、あらかじめ用意しておいた **asset** に 以下の cp コマンドを用いて置き換えます。その際、ファイルを上書きするかの確認が入る為、 **yes** と入力します。 

 ```
 # cp -p /root/asset/3-td-agent.conf /etc/td-agent/td-agent.conf
 ```

 3. Fluentd を再起動します。

    **Asset** 資料：[3-cmd.txt](asset/ap-northeast-1/3-cmd.txt)

 ```
 # /etc/init.d/td-agent restart
 ```

 4. Fluentd のログを確認し、ログの中にエラーが出続けることがないかを確認します。

     **Asset** 資料：[3-cmd.txt](asset/ap-northeast-1/3-cmd.txt)

 ```
 # tail -f /var/log/td-agent/td-agent.log
 ```

## Section2：CloudWatch, OpenSearch Service の設定変更
### Step1：CloudWatch Logs の設定

 1. AWS マネジメントコンソールのサービス一覧から **CloudWatch** を選択し、 **[CloudWatch]** の画面の左側ペインから **[ロググループ]** をクリックします。  

 2. ロググループ「 **minilake_group**（任意）」が出力されていることを確認し、クリックします。

    **Note：** 数分待ってもログが出ない場合は、 EC2 に IAM ロールがアタッチされてるか確認します。

 3. ログストリーム「 **testapplog_stream**（任意）」をクリックします。直近のログが出力されていることを確認します。画面上部の **[ロググループ]** の文字列をクリックし、ロググループに戻ります。  

 4. ロググループ「 **minilake_group**（任意）」にチェックを入れ、 **[アクション]** をクリックし、 **[サブスクリプションフィルター]** の中にある **[Create OpenSearch subscription filter]** をクリックします。  

    **Note：** 裏側では自動で Lambda Function が作られます。

 5. **"送信先の選択"** において、 **[アカウントの選択]** で **[This account]** を選択し、 **[Amazon ES cluster]** において、作成済みの「 **handson-minilake**（任意）」を選択し、 **[Lambda IAM Execution Role]** において、 「**handson-minilake-role**（任意）」 を選択します。  

　　**Note：** ブラウザでポップアップブロックが走ったら、許可して、1つ前の手順からやり直して下さい。

 6. **"ログ形式とフィルターの設定"** 画面において、　**[ログの形式]** に **[その他]** を選択し、　**[サブスクリプションフィルター名]** に、「**handson-minilake01**（任意）」と入力し、 **[ストリーミングの開始]** をクリックします。


### Step2：OpenSearch Service の設定

 1. **Kibana** の画面を開き、 **Kibana** の画面左にある![kibana_pain](images/kibana_pain2.png)アイコンをクリックし、 **[Management]** 内にある **[Stack Management]** をクリックします。  

 2. 左ペインの **[Index Pattern]** を選択し、 **[Create index pattern]** をクリックします。

 3. **[Index pattern]** に「 __cwl-*__ 」を入力し、右側の **[Next step]** をクリックします。

    **Note：** インデックス作成に多少の時間がかかるため、 **[Next step]** がクリックできるようになるまで、少し時間がかかることが想定されます。  

 4. **[Time Filter field name]** に **[@timestamp]** を選択し、右下の **[Create index pattern]** をクリックします。

 5. **Kibana** の画面の左ペインから **[Saved Objects]** をクリックします。画面右上の **[Import]** アイコンをクリックします。

 6. **[Import saved objects]** 画面において、 **[Import]** アイコンをクリックし、 **Asset** 資料の「 **3-visualization.json** 」を選択し、 **[Import]** をクリックします。続く画面において、 **[New index patten]** に対して、「 **cwl-\*** 」を選択し、 **[Confirm all changes]** をクリックし、インポートを完了します。問題なくインポートが完了したら、 **[Done]** をクリックすると、元の画面に戻ります。

    **Asset** 資料：[3-visualization.json](asset/ap-northeast-1/3-visualization.json)

 7. 続いて、再度 **[Saved Objects]** 画面において、 **[Import]** アイコンをクリックし、 **Asset** 資料の「 **3-dashboard.json** 」を選択し、 **[Import]** をクリックし、インポートします。問題なくインポートが完了したら、 **[Done]** をクリックすると、元の画面に戻ります。

    **Asset** 資料：[3-dashboard.json](asset/ap-northeast-1/3-dashboard.json)

### Step3：CloudWatch アラームの設定

 1. AWS マネジメントコンソールのサービス一覧から **CloudWatch** を選択し、**[ロググループ]** をクリックし、「 **minilake_group**（任意）」のロググループにチェックを入れ、 **[アクション]** から、**[メトリクスフィルターの作成]** をクリックします。

 2. フィルターパターンに「 **ERROR** 」を入力し、 **[パターンをテスト]** の **[テストするログデータを選択]** にて、 **[testapplog_stream]** を選択し、画面右下の **[Next]** をクリックします。  

 3. **[フィルター名]** に「 **ERROR** 」と入力し、 **[メトリクス名前空間]** と **[メトリクス名]** に「 **minilake_errlog**（任意）」と入力し、 最後に **[メトリクス値]** に「 **1** 」を入力し、 **[Next]** をクリックします。

 4. 確認画面に遷移するので、右下の **[メトリクスフィルターの作成]** をクリックするとフィルタが作成されます。画面を閉じずそのまま、作成したメトリクスフィルターにチェックを入れ、画面の右側の **[アラームの作成]** をクリックし、続けてアラーム設定を行います。

 5. **[期間]** を「 **1 分** 」に設定し、そのまま **[条件]** に下記を設定し、 **[次へ]** をクリックします。  

   - しきい値の種類：静的  
   - アラーム条件を定義：以上  
   - しきい値：50  

 6. 下記を設定し、 **[トピックの作成]** をクリックし、トピックを作成します。  

   - SNS トピックの選択：新しいトピックの作成
   - 新規トピックの作成中：任意の文字列（例：Default\_CloudWatch\_Alarms\_Topic）
   - 通知を受け取るEメールエンドポイント：ハンズオン実施中に受信可能なメールアドレス

     **Note：** 登録後、この手順で登録したメールアドレスに確認メールが飛びますので、メール本文の **[Confirm subscription]** をクリックし確認ください。

 7. 必要な設定は完了したため、 **[次へ]** をクリックします。

 8. **[アラーム名]** に「 **minilake-handson-alarm**（任意）」と入力し、 **[次へ]** をクリックし、続いての画面で、 **[アラームの作成]** をクリックします。

 9. **[CloudWatch]** の **[アラーム]** をクリックし、 「**minilake-handson-alarm** 」をクリックし、グラフ画面を確認します。最初は状態が **[データ不足]** と表示されていますが、しばらくすると **[OK]** になります。1分間で50件以上  **ERROR** が発生するとアラートが上がります。10分ごとに ERROR が300件出る設定になっている為、10分毎にアラートが上がります。

### 補足：Elasticsearch Service で、イベント監視およびアラートのサポートを開始しました。アラートは、 Elasticsearch 6.2 以降を実行しているドメインで利用可能です。本ハンズオンの該当する手順を置き換えた詳細な手順は[こちら](additional_info_lab3.md)をご覧ください。

## Section3：まとめ

リアルタイムなログのモニタリングと監視の環境を構築することができました。

 <img src="../images/architecture_lab3.png">

Lab3 は以上です。選択されているパターンに合わせて次の手順を実施ください。

（1） ニアリアルタイムデータ分析環境（スピードレイヤ）の構築：[Lab1](../lab1/README.md) → [Lab2](../lab2/README.md) → [Lab3](../lab3/README.md)  
（2） 長期間のデータをバッチ分析する環境（バッチレイヤ）の構築と、パフォーマンスとコストの最適化：[Lab1](../lab1/README.md) → [Lab4](../lab4/README.md) or [Lab5](../lab5/README.md) → [Lab6](../lab6/README.md)  
（3） すべて実施：[Lab1](../lab1/README.md) → [Lab2](../lab2/README.md) → [Lab3](../lab3/README.md) → [Lab4](../lab4/README.md) → [Lab5](../lab5/README.md) → [Lab6](../lab6/README.md)

環境を削除される際は、[こちら](../clean-up/README.md)の手順をご覧ください。
