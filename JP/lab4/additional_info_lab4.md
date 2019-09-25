------------------------------------------------------------------------------------
Copyright <first-edit-year> Amazon.com, Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: MIT-0

------------------------------------------------------------------------------------


# Athena におけるクエリ実行の補足説明
## しばらく時間が経過し、データが溜まった状態での実行例

 1. クエリエディタで下記 SQL を実行します。

 ```
 SELECT * FROM "minilake"."minilake_in1";
 ```
 
 **[実行結果例]**
 
 ```
 (実行時間: 4.66 秒, スキャンしたデータ: 27.02 MB)
 ``` 

 データの容量に応じて、実行時間、スキャンデータ量が増えていることが確認できます。


 2. Where 句をつけたクエリを実行してみます。

 ```
 SELECT * FROM "minilake"."minilake_in1" where partition_0 = '2019' AND partition_1 = '09' AND partition_2 = '27' AND partition_3 = '14';
 ```

 **[実行結果例]**
 
 ```
 (実行時間: 2.46 秒, スキャンしたデータ: 284.42 KB)
 ``` 

  **Note：** Where 句の日付はデータが存在するものを入力してください。
  

 1と同様、データの容量に応じて、実行時間、スキャンデータ量が増えていることが確認できます。  



**Athena はスキャンしたデータ量による課金、スキャン量がすくなければコストも低く抑えられさらにスキャン量が少なければパフォーマンス向上にもなります。**  
 読み取る量を減らす工夫のために、パーティション、圧縮、カラムナフォーマットを利用します。参考情報は[こちら](https://aws.amazon.com/jp/blogs/news/top-10-performance-tuning-tips-for-amazon-athena/)を参照ください。


