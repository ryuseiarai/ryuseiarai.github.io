---
layout: single
title:  "ネットワーク制限をかけたストレージアカウントにアクセスする方法"
date:   2021-12-09 23:11:07 +0900
categories:
  - azure
tags: 
  - Functions
  - App Service
  - Storage Account
toc: true
toc_label: "目次"
toc_icon: "cog"
---

# ネットワーク制限をかけたストレージアカウントにアクセスする方法


<img src="../../assets/images/icon/azure/Storage/10086-icon-service-Storage-Accounts.svg" width="100">

ストレージアカウントでパブリックを許可していると誰でもアクセスすることが可能になってしまいます。


※もちろんアクセスキーやAD認証などが必要なので、誰でも取得可能ではありません。  
そのため、ストレージアカウントのファイアウォール機能を使い許可されたネットワークからでしかアクセスできないようにすることができます。


## Storageファイアウォールが有効化されてい状態でのアクセス方法

[ストレージアカウント]-[ネットワーク]からファイアウォール設定画面に遷移します。

![image01](../../assets/images/2021/09-01.png)

### ファイアウォール

![image02](../../assets/images/2021/09-02.png)

こちらはオーソドックスなファイアウォール設定です。  
許可したいIPを設定して保存することでアクセス可能となるホワイトリスト形式のファイアウォールです。

注意点としてはグローバルIPのみ設定することが可能です。  
同一リージョンのVNETに属していないAzureリソースはたとえグローバルIPを付与していてもAzure同一リージョンではプライベートIPを使ってアクセスするためここで許可設定をいれてもアクセスできません。

### 仮装ネットワーク

![image03](../../assets/images/2021/09-03.png)

こちらはVNET内のサービスエンドポイントを利用して接続するための設定です。
Azure Virtual MachineなどのAzureのリソースはサブネットに属しており、そのサブネットから**サービスエンドポイント**を用い、Azure内のネットワークでストレージアカウントにアクセスできます。

![publicimage01](https://docs.microsoft.com/ja-jp/azure/virtual-network/media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)  
引用：https://docs.microsoft.com/ja-jp/azure/virtual-network/virtual-network-service-endpoint-policies-overview

### システム割り当てマネージド ID

![image04](../../assets/images/2021/09-04.png)

以下、サービスのシステム割り当てマネージドIDを利用してアクセスすることができます。  
ここでアクセス許可したいマネージドIDを追加し、マネージドIDに対して必要なアクセス権限を付与することでアクセス可能になります。

※MS公式Docs引用

|サービス	|リソース プロバイダー名	|目的|
|:-|:-|:-|
|Azure API Management	|Microsoft.ApiManagement/service	|ポリシーを使用して、API Management サービスが、ファイアウォールの背後にあるストレージ アカウントにアクセスできるようにします。 詳細については、こちらを参照してください。
|Azure Cache for Redis	|Microsoft.Cache/Redis	|Azure Cache for Redis からのストレージ アカウントへのアクセスを許可します。
|Azure Cognitive Search	|Microsoft.Search/searchServices	|インデックス作成、処理、およびクエリのために、Cognitive Search サービスがストレージ アカウントにアクセスできるようになります。
|Azure Cognitive Services	|Microsoft.CognitiveService/accounts	|Cognitive Services がストレージ アカウントにアクセスできるようにします。 詳細については、こちらを参照してください。
|Azure Container Registry タスク	|Microsoft.ContainerRegistry/registries	|ACR タスクは、コンテナー イメージを作成するときにストレージアカウントにアクセスできます。
|Azure Data Factory	|Microsoft.DataFactory/factories	|ADF ランタイムを使用してストレージ アカウントへのアクセスを許可します。
|Azure Data Share	|Microsoft.DataShare/accounts	|Data Share を使用してストレージ アカウントにアクセスできるようになります。
|Azure DevTest Labs	|Microsoft.DevTestLab/labs	|DevTest Labs からのストレージ アカウントへのアクセスを許可します。
|Azure Event Grid	|Microsoft.EventGrid/topics	|Azure Event Grid を使用してストレージ アカウントへのアクセスを許可します。
|Azure Healthcare APIs	|Microsoft.HealthcareApis/services	|Azure Healthcare APIs からのストレージ アカウントへのアクセスを許可します。
|Azure IoT Central アプリケーション	|Microsoft.IoTCentral/IoTApps	|Azure IoT Central アプリケーションからのストレージ アカウントへのアクセスを許可します。
|Azure IoT Hub	|Microsoft.Devices/IotHubs	|IoT ハブからのデータを BLOB ストレージに書き込むことができます。 詳細情報
|Azure Logic Apps	|Microsoft.Logic/workflows	|ロジック アプリがストレージ アカウントにアクセスできるようにします。 詳細については、こちらを参照してください。
|Azure Machine Learning サービス	|Microsoft.MachineLearningServices	|承認された Azure Machine Learning ワークスペースでは、BLOB ストレージに実験の出力、モデル、およびログを書き込み、データを読み取ります。 詳細については、こちらを参照してください。
|Azure Media Services	|Microsoft.Media/mediaservices	|Media Services からのストレージ アカウントへのアクセスを許可します。
|Azure Migrate	|Microsoft.Migrate/migrateprojects	|Azure Migrate からのストレージ アカウントへのアクセスを許可します。
|Azure Purview	|Microsoft.Purview/accounts	|Purview にストレージ アカウントへのアクセスを許可します。
|Azure Remote Rendering	|Microsoft.MixedReality/remoteRenderingAccounts	|Remote Rendering からのストレージ アカウントへのアクセスを許可します。
|Azure Site Recovery	|Microsoft.RecoveryServices/vaults	|Site Recovery からのストレージ アカウントへのアクセスを許可します。
|Azure SQL データベース	|Microsoft.Sql	|ファイアウォールの内側にあるストレージ アカウントへの監査データの書き込みを許可します。
|Azure Synapse Analytics	|Microsoft.Sql	|COPY ステートメントまたは PolyBase を使用して (専用プール)、またはサーバーレス プールで openrowset 関数と外部テーブルを使用して、特定の SQL データベースのデータのインポートとエクスポートを行うことを許可します。 詳細については、こちらを参照してください。
|Azure Stream Analytics	|Microsoft.StreamAnalytics	|ストリーミング ジョブからのデータを Blob Storage に書き込むことができます。 詳細については、こちらを参照してください。
|Azure Synapse Analytics	|Microsoft.Synapse |ワークスペース	Azure Synapse Analytics から Azure Storage のデータにアクセスできるようにします。

### 例外

![image05](../../assets/images/2021/09-05.png)

ストレージアカウントと**同じサブスクリプション**にある下記リソースは「信頼されたサービスの一覧にある Azure サービスがこのストレージ アカウントにアクセスすることを許可します。」にチェックをいれることでアクセス可能となります。

|サービス |リソースプロバイダー|
|:-|:-|
|Azure Backup |Microsoft.RecoveryServices
|Azure Data Box	|Microsoft.DataBox	
|Azure DevTest Labs	|Microsoft.DevTestLab
|Azure Event Grid	|Microsoft.EventGrid	
|Azure Event Hubs	|Microsoft.EventHub	
|Azure File Sync	|Microsoft.StorageSync	
|Azure HDInsight	|Microsoft.HDInsight	
|Azure Import Export	|Microsoft.ImportExport	
|Azure Monitor	|Microsoft.Insights	
|Azure のネットワーク	|Microsoft.Network	Network 
|Azure Site Recovery	|Microsoft.SiteRecovery	

### プライベートエンドポイント

![publicimage02](https://docs.microsoft.com/ja-jp/azure/storage/common/media/storage-private-endpoints/storage-private-endpoints-overview.jpg)  
引用：https://docs.microsoft.com/ja-jp/azure/storage/common/storage-private-endpoints

VNET上にプライベートエンドポイントを作成し、プライベートエンドポイントを経由してアクセスすることも可能です。

プライベートエンドポイントの使い道としては社内ネットワークをExpress RouteなどでVNETと繋ぎ、従業員もインターネット経由で直接ストレージアカウントへアクセスするのではなく、プライベートエンドポイントにアクセスすることでインターネットに出ずにストレージアカウントにアクセスすることが可能です。

## App ServiceやFunctionsでネットワーク制限されている同一リージョンのストレージアカウントにアクセスする方法

<img src="../../assets/images/icon/azure/App Services/10035-icon-service-App-Services.svg" width="100"> 
<img src="../../assets/images/icon/azure/Functions/10029-icon-service-Function-Apps.svg" width="100">

デフォルトでは**無理です。**  
これがけっこうハマるポイントです。

### 回避策

App ServiceやFunctionsではプラン次第ですが、VNET統合ができます。  
VNET統合をした上で仮装ネットワークを追加してあげればネットワーク制限された同一リージョンのストレージアカウントにアクセスすることが可能です。  

VNET統合できないプランの場合(Functions 従量課金など)はどう頑張っても無理なので別リージョンにストレージアカウントを作成しましょう。