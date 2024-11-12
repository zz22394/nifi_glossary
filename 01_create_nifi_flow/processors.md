# NiFi の主なプロセッサー

NiFi には 300を超えるプロセッサーがあります。

すべてのプロセッサーの情報は、[公式ドキュメント](https://nifi.apache.org/components/)をご覧ください。

ここでは、プロセッサーの主な分類ごとに概要を説明します。

## 入力系

### Listen 系

HTTPやTCPなどのプロトコルを待ち受けるためのプロセッサーです。
フローの先頭に配置し、受け取った内容を後続処理に渡す役割を担うプロセッサーです。

主なプロセッサーには、以下があります。
- [ListenHTTP](https://nifi.apache.org/components/org.apache.nifi.processors.standard.ListenHTTP/)
- [ListenTCP](https://nifi.apache.org/components/org.apache.nifi.processors.standard.ListenTCP/)
- [ListenUDP](https://nifi.apache.org/components/org.apache.nifi.processors.standard.ListenUDP/)
- [ListenSyslog](https://nifi.apache.org/components/org.apache.nifi.processors.standard.ListenSyslog/)

### Consume 系

Kafka や MQTT など、ストリーミング系のキューからデータを取得する場合は Consume 系のプロセッサーを使用します。
Listen系同様、フローの先頭に配置し、受け取った内容を後続処理に渡す役割を担います。

主なプロセッサーには、以下があります。
- [ConsumeKafka](https://nifi.apache.org/components/org.apache.nifi.kafka.processors.ConsumeKafka/)
- [ConsumeMQTT](https://nifi.apache.org/components/org.apache.nifi.processors.mqtt.ConsumeMQTT/)

### List 系

ファイルシステムやオブジェクトストレージ等の特定の階層に存在するファイルやディレクトリの一覧を取得するプロセッサーです。  
ファイルを取得するためのフローの先頭に配置し、主にFetch系のプロセッサーに引き渡すために使われます。

シングルノードで実施するのがベストプラクティスです。

主なプロセッサーには、以下があります。
- [ListFile](https://nifi.apache.org/components/org.apache.nifi.processors.standard.ListFile/)
- [ListS3](https://nifi.apache.org/components/org.apache.nifi.processors.aws.s3.ListS3/)
- [ListDropbox](https://nifi.apache.org/components/org.apache.nifi.processors.dropbox.ListDropbox/)

### Fetch 系

ファイルシステムやオブジェクトストレージから、実際にファイルを取得するためのプロセッサーです。
List系プロセッサーと組み合わせることで、特定の階層からの全ファイル取得や条件指定によるファイル取得が可能になります。

主なプロセッサーには、以下があります。

- [FetchFile](https://nifi.apache.org/components/org.apache.nifi.processors.standard.FetchFile/)
- [FetchS3Object](https://nifi.apache.org/components/org.apache.nifi.processors.aws.s3.FetchS3Object/)
- [FetchDropbox](https://nifi.apache.org/components/org.apache.nifi.processors.dropbox.FetchDropbox/)

Fetch と似たプロセッサーに Get 系のプロセッサーがありますが、Fetch を使うことが推奨されています。
参考ページ：[Clouderaコミュ二ティページ（ログインが必要です）](https://community.cloudera.com/t5/Support-Questions/Fetch-Vs-Get-vs-List-processors-in-NiFi/m-p/269300#:~:text=The%20short%20answer%20is%20that,while%20GetX%20processors%20are%20not)

## 処理系

### 抽出系

Content の内容をもとに処理対象のデータを抽出する際には、[QueryRecord](https://nifi.apache.org/docs/nifi-docs/components/org.apache.nifi/nifi-standard-nar/1.6.0/org.apache.nifi.processors.standard.QueryRecord/index.html)プロセッサーを利用します。

参考ページ：[QueryRecordの使い方（英語）](https://medium.com/cloudera-inc/building-an-effective-nifi-flow-queryrecord-cca5ba51afd5)

### 更新系

#### Content の更新

Content を更新する際は、[UpdateRecord](https://nifi.apache.org/docs/nifi-docs/components/org.apache.nifi/nifi-standard-nar/1.6.0/org.apache.nifi.processors.standard.UpdateRecord/index.html)プロセッサーを利用することが一般的です。

#### Attribute の更新
[UpdateAttribute](https://nifi.apache.org/docs/nifi-docs/components/org.apache.nifi/nifi-update-attribute-nar/1.19.1/org.apache.nifi.processors.attributes.UpdateAttribute/index.html)プロセッサーで、Attribute の項目を更新・追加することができます。
ファイル出力時のファイル名の更新は、このプロセッサーで filename 属性を更新することで実装します。

### 分岐系

ファイルの内容をもとに処理を分岐する場合は[RouteOnContent](https://nifi.apache.org/docs/nifi-docs/components/org.apache.nifi/nifi-standard-nar/1.23.2/org.apache.nifi.processors.standard.RouteOnContent/index.html)、属性情報（Attribute）をもとに処理を分岐する場合は[RouteOnAttribute](https://nifi.apache.org/docs/nifi-docs/components/org.apache.nifi/nifi-standard-nar/1.6.0/org.apache.nifi.processors.standard.RouteOnAttribute/index.html)を利用します。

### Split / Merge 系

データを分割した上で統合する Split/Merge 系のプロセッサーもありますが、Splitによりフローファイルの数が一気に増えることで処理効率が落ちる場合が多く、一般的にはあまり推奨されません。  
上述の抽出・更新・分岐系の処理で対応できる限り、これらのプロセッサーを使って処理することが推奨されます。

## 出力系

### Put系

データベースやファイルシステム等にデータを書き込むためのプロセッサーです。 

主なプロセッサーには、以下があります。
- [PutFile](https://nifi.apache.org/components/org.apache.nifi.processors.standard.PutFile/)
- [PutS3Object](https://nifi.apache.org/components/org.apache.nifi.processors.aws.s3.PutS3Object/)
- [PutSNS](https://nifi.apache.org/components/org.apache.nifi.processors.aws.sns.PutSNS/)
- [PutSQS](https://nifi.apache.org/components/org.apache.nifi.processors.aws.sqs.PutSQS/)
- [PutSyslog](https://nifi.apache.org/components/org.apache.nifi.processors.standard.PutSyslog/)

### Publish 系

Kafka や MQTT など、ストリーミング系の処理にデータを受け渡すためのプロセッサーです。

主なプロセッサーには、以下があります。

- [PublishKafka](https://nifi.apache.org/components/org.apache.nifi.kafka.processors.PublishKafka/)
- [PublishMQTT](https://nifi.apache.org/components/org.apache.nifi.processors.mqtt.PublishMQTT/)