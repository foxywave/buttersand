# はじめに
IoTの勉強のために自分でIoT環境を組んでみた  
あとGitHubをまったく知らないのでGitHubで公開してみようと思いこれを書いているが、何を書けばよいのかよくわからない…  
構築なんてとりあえずパッケージをインストールして適当に設定すればとりあえずは使えるようにはなっているので、
構築の時にお世話になったページのリンクと調べたこととか書いていく  
あとは別ファイルでDHT11から情報取得して、EC2に投げてるプログラムも載せる  

# 構成  
```DHT11 -- ラズパイ -- インターネット -- EC2(InfluxDB & Grafana)```  
DHT11で取得した温度/湿度のデータをInfluxDBに送り、Grafanaで見るという簡素なシステム  

# 参考情報  
[Influx Document](https://docs.influxdata.com/influxdb/v1.8/about_the_project/)  
公式のドキュメント  
[InfluxDB入門](https://www.magata.net/memo/index.php?InfluxDB%C6%FE%CC%E7#o28fc611)  
利用するコマンド等がまとまってる。基本これ使うのでいい気がする  
[InfluxDB-Python](https://github.com/influxdata/influxdb-python)  
pythonを使ってInfluxDBにデータを送るときに使うライブラリ。ほかの人のGitHubのページのリンク貼るときのお作法みたいなのがあるのか?  
[時系列データ可視化のための InfluxDB、Grafana、AWS IoTの連携](https://aws.amazon.com/jp/blogs/news/influxdb-and-grafana-with-aws-iot-to-visualize-time-series-data/)  
AWSのブログ。このページを見て環境を構築するやる気がでてきた。結局違う構成になってしまったが…  

# コマンド  
## InfluxDB  
・influxdbへのログイン方法  
```influx -precision rfc3339```  
※デフォルトの場合。アカウント管理をしてる場合は引数にユーザーの指定が必要  

・データを見るまでの一連の操作  
```show databases```  
存在するデータベースを確認  
```use <database>```  
利用するデータベースを指定する  
```show measurements```  
登録しているメジャーメントを確認(InfluxDBではテーブルのことをmeasurementという)  
```show tag keys from <measurement> ```  
タグとして設定されている項目の一覧を確認(タグはキーのようにレコードを特定するために使われる)  
```show field keys from <measurement> ```  
フィールドとして設定されている項目の一覧を確認(フィールドはタグ以外の項目、被ってもよい項目で利用される)  
※対象measurementの項目一覧を表示するコマンドはないみたい  

・measurementの作成方法  
空のメジャーメントを作成することはできないみたいなので、ダミーのデータをインサートする方法でメジャーメントを作成する  
```INSERT <measurement>[,<tag_key>=<tag_value>[,<tag_key>=<tag_value>]] <field_key>=<field_value>[,<field_key>=<field_value>] [<timestamp>]```  
※なぜかtimestampの引数にrfc3339形式のデータを入力してもエラーではじかれた…  

# 今後
今回の構成ではIoT GW(ラズパイ)から直接データをDBに送信したが、そんな構成はビジネスシーンではありえないと思うので
DBの前段にメッセージキューとしてkafkaを構築したいと思う  

# 最後に(感想)  
今回はGitHubとは何かを学ぶためにこのようにまとめてみたけど、GitHubって本来コード管理の仕組みなので文字の羅列ってどうなの?という思いが終始付きまとった。  
あとIoTシステム一式構築して思ったのが、やっぱりプログラミングって大事だなーってこと。  
デバイスからのデータをどうやって抜き出すか、集めたデータを活用するために何をするか(グラフ化?AIでの予測?)というところがIoTでの大事な部分で、
個性出せる部分だと思うのだが、そこってプログラムで実現することがほとんどだと思うのでそこを知らないって致命的だなと改めて痛感した。  
OSI参照モデルとかって仕組みとしてはいいと思うが、エンジニア側までOSI参照モデルに準拠してるのってどうなの?って思った。  
プログラミング勉強するか…  
