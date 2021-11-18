------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# 補足説明：　Fluentd のインストール

AWS CloudFormation では、以下の手順が実施された状態のAMIからEC2を起動しています。
任意のEC2 にFluentdをインストールしたい場合は、以下の設定を行なってください。

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
