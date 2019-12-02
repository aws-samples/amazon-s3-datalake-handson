------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Lab1：はじめの準備
残りの5つの Lab で必要となる共通の環境を構築します。  
AWS CloudFormation（以降、CloudFormation）にて、 Amazon VPC（以降、VPC）、 Amazon EC2（以降、EC2）を構築し、 AWS IAM（以降、IAM）で権限設定を行います。その後、手動でログ収集ソフトウェアの Fluentd をインストールします。

## Section1：事前準備
### Step1：AWS マネジメントコンソールにログイン

 1. AWS マネジメントコンソールにログインします。ログイン後、画面右上のヘッダー部のリージョン選択にて、 **[東京]** となっていることを確認します。  

    **Note：** **[東京]** となっていない場合は変更します。

 2. AWS マネジメントコンソールのサービス一覧から **EC2** を選択します。 **[EC2 ダッシュボード]** の左ペインから **[キーペア]** をクリックし、 **[キーペア作成]** ボタンをクリックし、**[キーペア名]** に任意の値（例：handson）を入力し、**[作成]** をクリックします。操作しているパソコンに秘密鍵（例：handson.pem）がダウンロードされます。  

   **Note：** 既存のキーペアを使われる場合は、こちらの手順は飛ばしてください。

## Section2：EC2 環境構築
### Step1：EC2 1台を CloudFormation で構築

CloudFormation を使い、 VPC を作成し、作成された VPC にログを出力し続ける EC2 を構築します。 ログは2分おきに10件前後出力され、10分おきに300件のエラーログが出力されます。  

 1. AWS マネジメントコンソールのサービス一覧から **CloudFormation** を選択します。  

    **Note：** CloudFormation が見つけられない場合、検索窓に「cloudform」などと入力し、選択します。
  
 2. **[CloudFormation]** の画面において、画面右上の **[スタックの作成]** をクリックし、 **[新しいリソースを使用（標準）]** を選択します。  
 
 3. **[スタックの作成]** 画面の **[前提条件 - テンプレートの準備]** において、 **[テンプレートの準備完了]** を選択します。 

    **Note：** デフォルトで選択されている場合、そのまま進めます。
 
 4. 続いて、 **[スタックの作成]** 画面の **[テンプレートの指定]** において、 **[テンプレートファイルのアップロード]** を選択し、 **[ファイルの選択]** をクリックし、ダウンロードしたテンプレート「 **1-minilake_ec2.yaml** 」を指定し、 **[次へ]** をクリックします。 

    **Asset** 資料：[1-minilake_ec2.yaml](asset/ap-northeast-1/1-minilake_ec2.yaml)
  
 5. **[スタックの名前]** に 「 **handson-minilake**（任意）」、 **[パラメータ]** の **[InstanceType]** に「 **t2.micro** 」を選択し、 **[KeyPair]** に **Section1** で作成したキーペア「**handson.pem**（任意）」、もしくは既に作成済みの場合はそのキーペアを指定して、 **[次へ]** をクリックします。  
 
 6. オプションの **タグ** で、 **キー** に 「 **Name** 」 、 **値** に 「 **handson-minilake**（任意）」 と入力し、 **[次へ]** をクリックします。
 
 7. 最後の確認ページの内容を確認し、 **[スタックの作成]** をクリックします。数分ほど待つと EC2 一台ができあがり、 **/root/es-demo/testapp.log** にログ出力が始まります。  
 
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
 
### Step2：IAM ロールの作成と EC2 へのアタッチ

EC2 に対して、後続の Lab で利用するための IAM ロールを作成します。  

**Note：** AWS Systems Manager Session Manager（以降、Session Manager）で EC2 に対してアクセスを行なっている場合、 Session Manager 側の手順で、 IAM ロールを作成している為、本手順の6番まではスキップしていただいて問題ありません。  

 1. AWS マネジメントコンソールのサービス一覧から **IAM** を選択し、 **[Identity and Access Management (IAM)]** 画面の左ペインから **[ロール]** を選択します。
 
 2. **[ロールの作成]** をクリックします。
 
 3. **[AWS サービス]** を選択し、 **[EC2]** を選択、 **[次のステップ：アクセス権限]** をクリックします。
 
 4. **[Attach アクセス権限ポリシー]** の画面で、何も変更せずにそのまま **[次のステップ：タグ]** をクリックします。  

    **Note：** この段階ではポリシーなしでロールを作成します。 Session Manager 利用の方は **AmazonEC2RoleforSSM** のみがアタッチされています。

 5. **[タグの追加（オプション）]** 画面で、そのまま **[次のステップ：確認]** をクリックします。
 
 6. **ロール名** に「 **handson-minilake**（任意）」と入力し、 **[ロールの作成]** をクリックします。
 
 7. AWS マネージメントコンソールのサービス一覧から **EC2** を選択し、 **[EC2 ダッシュボード]** 画面の左ペインから **[インスタンス]** を選択し、今回作成したインスタンス「 **handson-minilake**（任意）」にチェックを入れ、 **[アクション] → [インスタンスの設定] → [IAM ロールの割り当て/置換]** をクリックします。
 
 8. **[IAM ロールの割り当て/置換]** の画面において、 **[IAM ロール]** に「 **handson-minilake**（任意）」を選択し、 **[適用]** をクリックします。
 
 9. **[閉じる]** をクリックすると、インスタンス一覧の画面に戻ります。
  

### Step3：Fluentd のインストール

EC2 にログインし、ログ収集ソフトウェアの Fluentd のインストールを行い、設定を行います。

 1. EC2 にログインし、 redhat-lsb-core と gcc をインストールします。
   
    **Note：** 準備済みの AMI にはすでにインストールされているため、スキップ可能な手順です。
    **Asset** 資料：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)

 ```
 $ sudo su -
 # yum -y install redhat-lsb-core gcc
 ```

 2. td-agent をインストールします。

    **Asset** 資料：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)
    
 ```
 # rpm -ivh http://packages.treasuredata.com.s3.amazonaws.com/3/redhat/6/x86_64/td-agent-3.1.1-0.el6.x86_64.rpm
 ```

 3. **/etc/init.d/td-agent** の18行目の **TD\_AGENT\_USER** の指定を **td-agent** から **root** に修正します。  
 
    **Note：** コマンド例は、 vi エディタを使用した例ですが、他に使い慣れたエディタがある場合は、そちらをご利用ください。
    **Asset** 資料：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)

 ```
 # vi /etc/init.d/td-agent
 ```

 **[変更前]**
 
 ```
 TD_AGENT_USER=td-agent
 ```
 
 **[変更後]**
 
 ```
 TD_AGENT_USER=root 
 ```

 4. Fluentd の自動起動設定をします。（実際の起動は後ほど行います。）

    **Asset** 資料：[1-cmd.txt](asset/ap-northeast-1/1-cmd.txt)

 ```
 # chkconfig td-agent on
 ```
 

Lab1 は以上です。選択されているパターンに合わせて次の手順を実施ください。

（1） ニアリアルタイムデータ分析環境（スピードレイヤ）の構築：[Lab1](../lab1/README.md) → [Lab2](../lab2/README.md) → [Lab3](../lab3/README.md)  
（2） 長期間のデータをバッチ分析する環境（バッチレイヤ）の構築と、パフォーマンスとコストの最適化：[Lab1](../lab1/README.md) → [Lab4](../lab4/README.md) or [Lab5](../lab5/README.md) → [Lab6](../lab6/README.md)  
（3） すべて実施：[Lab1](../lab1/README.md) → [Lab2](../lab2/README.md) → [Lab3](../lab3/README.md) → [Lab4](../lab4/README.md) → [Lab5](../lab5/README.md) → [Lab6](../lab6/README.md) 

環境を削除される際は、[こちら](../clean-up/README.md)の手順をご覧ください。