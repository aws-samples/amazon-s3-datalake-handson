------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Lab1：はじめの準備
残りの5つの Lab で必要となる共通の環境を構築します。  
AWS CloudFormation（以降、CloudFormation）にて、 Amazon VPC（以降、VPC）、 Amazon EC2（以降、EC2）の構築、そして AWS IAM（以降、IAM）の権限設定を行います。CloudFormation を実行することで、FluentdがインストールされたEC2が起動します。

## Section1：事前準備
### Step1：AWS マネジメントコンソールにログイン

 1. AWS マネジメントコンソールにログインします。ログイン後、画面右上のヘッダー部のリージョン選択にて、 **[東京]** となっていることを確認します。  

    **Note：** **[東京]** となっていない場合は変更します。

 2. AWS マネジメントコンソールのサービス一覧から **EC2** を選択します。 **[EC2 ダッシュボード]** の左ペインから **[キーペア]** をクリックし、 **[キーペアを作成]** ボタンをクリックし、**[名前]** に任意の値（例：handson）を入力し、 **[キーペアを作成]** をクリックします。操作しているパソコンに秘密鍵（例：handson.pem）がダウンロードされます。  

    **Note：** 既存のキーペアを使われる場合は、こちらの手順は飛ばしてください。

## Section2：EC2 環境構築
### Step1：EC2 1台を CloudFormation で構築

CloudFormation を使い、 VPC を作成し、作成された VPC にログを出力し続ける EC2 を構築します。 ログは2分おきに10件前後出力され、10分おきに300件のエラーログが出力されます。  

 1. AWS マネジメントコンソールのサービス一覧から **CloudFormation** を選択します。  

    **Note：** CloudFormation が見つけられない場合、検索窓に「cloudform」などと入力し、選択します。
  
 2. **[CloudFormation]** の画面において、画面右上の **[スタックの作成]** をクリックし、 **[新しいリソースを使用（標準）]** を選択します。  

    **Note：** 場合によっては、 **[スタックの作成]** をクリックすることで、手順 3 の画面に遷移しますが、そのまま進めていただいて問題ありません。
 
 3. **[スタックの作成]** 画面の **[前提条件 - テンプレートの準備]** において、 **[テンプレートの準備完了]** を選択します。 

    **Note：** デフォルトで選択されている場合、そのまま進めます。
 
 4. 続いて、 **[スタックの作成]** 画面の **[テンプレートの指定]** において、 **[テンプレートファイルのアップロード]** を選択し、 **[ファイルの選択]** をクリックし、ダウンロードしたテンプレート「 **1-minilake_ec2.yaml** 」を指定し、 **[次へ]** をクリックします。 

    **Asset** 資料：[1-minilake_ec2.yaml](asset/ap-northeast-1/1-minilake_ec2.yaml)
  
 5. **[スタックの名前]** に 「 **handson-minilake**（任意）」、 **[パラメータ]** の **[KeyPair]** に **Section1** で作成したキーペア「**handson.pem**（任意）」、もしくは既に作成済みの場合はそのキーペアを指定し、 **[RoleName]** に「 **handson-minilake-role**（任意）」と入力し、 **[次へ]** をクリックします。  
 
 6. オプションの **タグ** で、 **キー** に 「 **Name** 」 、 **値** に 「 **handson-minilake**（任意）」 と入力し、 **[次へ]** をクリックします。
 
 7. 最後の確認ページの内容を確認し、 確認ページ下部の「 **AWS CloudFormation によって IAM リソースがカスタム名で作成される場合があることを承認します。** 」にチェックを入れ **[スタックの作成]** をクリックします。数分ほど待つと EC2 一台ができあがり、 **/root/es-demo/testapp.log** にログ出力が始まります。
 
     **Note：** SSMでログインされる場合は、インスタンスが起動してからSSM接続ができるまでタイムラグが発生する可能性があるため、10分程度休憩時間を取得することを推奨します。

 8. EC2 へ **SSH ログインして root にスイッチし、** ログが2分おきに出力していることを確認します。  
 
    **Note：** EC2 のログイン方法については、[こちら](additional_info_lab1.md#EC2へのログイン方法)を参照ください。 EC2 の 接続先の IP アドレス情報につきましては、 **[CloudFormation]** の画面から、該当の CloudFormation のスタックを選択し、 **[出力]** のタブをクリックすると、 **[AllowIPAddress]** の情報から確認できます。

 ```
 $ sudo su -
 # tail -f /root/es-demo/testapp.log
 ```
 
 **[ログの出力例]**

 ``` 
[2019-09-16 15:14:01+0900] WARNING prd-db02 uehara 1001 [This is Warning.]
[2019-09-16 15:14:01+0900] INFO prd-db02 uehara 1001 [This is Information.]
[2019-09-16 15:14:01+0900] INFO prd-web002 uchida 1001 [This is Information.]
[2019-09-16 15:18:01+0900] INFO prd-ap001 uehara 1001 [This is Information.]
[2019-09-16 15:18:01+0900] ERROR prd-db02 uchida 1001 [This is ERROR.]
 ```

## Section3：まとめ

CloudFormation を使い、 以下の設定を行いました。

   1. VPC を作成し、2分おきに10件前後のログを出力し、10分おきに300件のエラーログを出力し続ける EC2 を作成しました。
   2. VPC 内に作成した構築した EC2 に AWS リソースにアクセスするための権限を付与しました。詳細については[こちら](./additional_info_lab1_IAM.md) をご覧下さい。
   3. 構築したEC2に、ログ収集ソフトウェアの Fluentd をインストールしました。詳細については[こちら](./additional_info_lab1_Fluentd.md) をご覧下さい。

<img src="../images/architecture_lab1.png">

Lab1 は以上です。選択されているパターンに合わせて次の手順を実施ください。

（1） ニアリアルタイムデータ分析環境（スピードレイヤ）の構築：[Lab1](../lab1/README.md) → [Lab2](../lab2/README.md) → [Lab3](../lab3/README.md)  
（2） 長期間のデータをバッチ分析する環境（バッチレイヤ）の構築と、パフォーマンスとコストの最適化：[Lab1](../lab1/README.md) → [Lab4](../lab4/README.md) or [Lab5](../lab5/README.md) → [Lab6](../lab6/README.md)  
（3） すべて実施：[Lab1](../lab1/README.md) → [Lab2](../lab2/README.md) → [Lab3](../lab3/README.md) → [Lab4](../lab4/README.md) → [Lab5](../lab5/README.md) → [Lab6](../lab6/README.md) 

環境を削除される際は、[こちら](../clean-up/README.md)の手順をご覧ください。