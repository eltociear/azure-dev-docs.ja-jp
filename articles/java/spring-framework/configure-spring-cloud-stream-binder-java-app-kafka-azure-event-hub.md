---
title: Azure Event Hubs で Apache Kafka 用 Spring Boot Starter を使用する方法
description: Spring Boot Initializer を使用して作成されたアプリケーションを、Apache Kafka と Azure Event Hubs を使用するように構成する方法について説明します。
services: event-hubs
documentationcenter: java
ms.date: 12/19/2018
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: 1141c54e613003f6fcc9396b11528765eaf3f8ad
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "81669598"
---
# <a name="how-to-use-the-spring-boot-starter-for-apache-kafka-with-azure-event-hubs"></a>Azure Event Hubs で Apache Kafka 用 Spring Boot Starter を使用する方法

この記事では、Spring Boot Initializr を使用して作成された Java ベースの Spring Cloud Stream Binder を、[Apache Kafka] と Azure Event Hubs を使用するように構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順に従うには、次の前提条件が必要です。

* Azure サブスクリプション。Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典]を有効にするか、または[無料の Azure アカウント]にサインアップできます。
* サポートされている Java Development Kit (JDK)。 Azure での開発時に使用可能な JDK の詳細については、<https://aka.ms/azure-jdks> を参照してください。
* [Apache Maven](http://maven.apache.org/) バージョン 3.0 以降。

> [!IMPORTANT]
>
> この記事の手順を完了するには、Spring Boot 2.0 以上のバージョンが必要です。
>

## <a name="create-an-azure-event-hub-using-the-azure-portal"></a>Azure portal を使用して Azure イベント ハブを作成する

### <a name="create-an-azure-event-hub-namespace"></a>Azure イベント ハブの名前空間を作成する

1. Azure portal (<https://portal.azure.com/>) を参照し、サインインします。

1. **[+ リソースの作成]** 、 **[モノのインターネット]** の順にクリックし、*Event Hubs** を検索します。

1. **Create** をクリックしてください。

   ![Azure イベント ハブの名前空間を作成する][IMG01]

1. **[名前空間の作成]** ページで、次の情報を入力します。

   * 一意の**名前**を入力します。この名前は、イベント ハブの名前空間の URI の一部になります。 たとえば、 **[名前]** に「**wingtiptoys**」と入力した場合、URI は *wingtiptoys.servicebus.windows.net* になります。
   * 価格レベル。
   * 名前空間に対して **[Kafka を有効にする]** を指定します。
   * 名前空間に使用する**サブスクリプション**を選択します。
   * 名前空間の新しい**リソース グループ**を作成するか、既存のリソース グループを選択するかを指定します。
   * イベント ハブの名前空間の**場所**を指定します。
   * 名前空間の **[スループット ユニット]** を指定することもできます。

   ![Azure イベント ハブの名前空間のオプションを指定する][IMG02]

1. 上記のオプションを指定したら、 **[作成]** をクリックして名前空間を作成します。

### <a name="create-an-azure-event-hub-in-your-namespace"></a>名前空間に Azure イベント ハブを作成する

名前空間のデプロイ後、名前空間にイベント ハブを作成できます。

1. 前の手順で作成した名前空間に移動します。

1. 上部のメニュー バーの **[+ イベント ハブ]** をクリックします。

1. イベント ハブに名前を指定します。

1. **Create** をクリックしてください。

   ![イベント ハブの作成][IMG05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Spring Initializr でシンプルな Spring Boot アプリケーションを作成する

1. [https://www.microsoft.com](<https://start.spring.io/>) を参照します。

1. 次のオプションを指定します。

   * **Java** で **Maven** プロジェクトを生成します。
   * **Spring Boot** のバージョンとして、2.0 以上を指定します。
   * アプリケーションの**グループ (Group)** と**成果物 (Artifact)** の名前を指定します。
   * **Web** 依存関係を追加します。

      ![基本的な Spring Initializr オプション][SI01]

   > [!NOTE]
   >
   > Spring Initializr では、**グループ (Group)** と**成果物 (Artifact)** の名前を使用してパッケージ名を作成します (例: *com.wingtiptoys.kafka*)。
   >

1. 上記のオプションを指定したら、 **[Generate Project]\(プロジェクトの生成\)** をクリックします。

1. メッセージが表示されたら、ローカル コンピューター上のパスにプロジェクトをダウンロードします。

1. ファイルをローカル システム上に展開したら、シンプルな Spring Boot アプリケーションの編集を開始できます。

## <a name="configure-your-spring-boot-app-to-use-the-spring-cloud-kafka-stream-and-azure-event-hub-starters"></a>Spring Cloud の Kafka Stream スターターと Azure Event Hub スターターを使用するように Spring Boot アプリを構成する

1. アプリのルート ディレクトリで *pom.xml* ファイルを探します。次に例を示します。

   `C:\SpringBoot\kafka\pom.xml`

   または

   `/users/example/home/kafka/pom.xml`

1. テキスト エディターで *pom.xml* ファイルを開き、Spring Cloud の Kafka Stream スターターと Azure Event Hub スターターを `<dependencies>` のリストに追加します。

   ```xml
   <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-stream-kafka</artifactId>
      <version>2.0.1.RELEASE</version>
   </dependency>
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-cloud-azure-starter-eventhub</artifactId>
      <version>1.0.0.M2</version>
   </dependency>
   ```

   ![pom.xml ファイルを編集する][SI03]

1. *pom.xml* ファイルを保存して閉じます。

## <a name="create-an-azure-credential-file"></a>Azure 資格情報ファイルを作成する

1. コマンド プロンプトを開きます。

1. Spring Boot アプリの *resources* ディレクトリに移動します。次に例を示します。

   ```shell
   cd C:\SpringBoot\eventhub\src\main\resources
   ```

   または

   ```shell
   cd /users/example/home/eventhub/src/main/resources
   ```

1. Azure アカウントにサインインします。

   ```azurecli
   az login
   ```

1. サブスクリプションを一覧表示します。

   ```azurecli
   az account list
   ```
   Azure からサブスクリプションの一覧が返されます。使用するサブスクリプションの GUID をコピーする必要があります。次に例を示します。

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "11111111-1111-1111-1111-111111111111",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "22222222-2222-2222-2222-222222222222",
       "user": {
         "name": "gena.soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```
   
1. Azure で使用するサブスクリプションの GUID を指定します。次に例を示します。

   ```azurecli
   az account set -s 11111111-1111-1111-1111-111111111111
   ```

1. Azure 資格情報ファイルを作成します。

   ```azurecli
   az ad sp create-for-rbac --sdk-auth > my.azureauth
   ```

   このコマンドにより、*resources* ディレクトリに、次の例のような内容の *my.azureauth* ファイルが作成されます。

   ```json
   {
     "clientId": "33333333-3333-3333-3333-333333333333",
     "clientSecret": "44444444-4444-4444-4444-444444444444",
     "subscriptionId": "11111111-1111-1111-1111-111111111111",
     "tenantId": "22222222-2222-2222-2222-222222222222",
     "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
     "resourceManagerEndpointUrl": "https://management.azure.com/",
     "activeDirectoryGraphResourceId": "https://graph.windows.net/",
     "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
     "galleryEndpointUrl": "https://gallery.azure.com/",
     "managementEndpointUrl": "https://management.core.windows.net/"
   }
   ```

## <a name="configure-your-spring-boot-app-to-use-your-azure-event-hub"></a>Azure イベント ハブを使用するように Spring Boot アプリを構成する

1. アプリの *resources* ディレクトリで *application.properties* を探します。次に例を示します。

   `C:\SpringBoot\eventhub\src\main\resources\application.properties`

   または

   `/users/example/home/eventhub/src/main/resources/application.properties`

2. テキスト エディターで *application.properties* ファイルを開きます。次の行を追加し、サンプルの値をイベント ハブの適切なプロパティに置き換えます。

   ```yaml
   spring.cloud.azure.credential-file-path=my.azureauth
   spring.cloud.azure.resource-group=wingtiptoysresources
   spring.cloud.azure.region=West US
   spring.cloud.azure.eventhub.namespace=wingtiptoys

   spring.cloud.stream.bindings.input.destination=wingtiptoyshub
   spring.cloud.stream.bindings.input.group=$Default
   spring.cloud.stream.bindings.output.destination=wingtiptoyshub
   ```
   各値の説明:

   |                       フィールド                       |                                                                                   説明                                                                                    |
   |---------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |     `spring.cloud.azure.credential-file-path`     |                                                    このチュートリアルで作成した Azure 資格情報ファイルを指定します。                                                    |
   |        `spring.cloud.azure.resource-group`        |                                                      Azure イベント ハブを含む Azure リソース グループを指定します。                                                      |
   |            `spring.cloud.azure.region`            |                                           Azure イベント ハブの作成時に指定した地理的リージョンを指定します。                                            |
   |      `spring.cloud.azure.eventhub.namespace`      |                                          Azure イベント ハブの名前空間の作成時に指定した一意の名前を指定します。                                           |
   | `spring.cloud.stream.bindings.input.destination`  |                            入力先の Azure イベント ハブを指定します。ここでは、このチュートリアルで作成したハブを指定します。                            |
   |    `spring.cloud.stream.bindings.input.group `    | Azure イベント ハブのコンシューマー グループを指定します。Azure イベント ハブの作成時に作成された基本コンシューマー グループを使用するには、"$ Default" に設定します。 |
   | `spring.cloud.stream.bindings.output.destination` |                               出力先の Azure イベント ハブを指定します。ここでは、入力先と同じものになります。                               |


3. *application.properties* ファイルを保存して閉じます。

## <a name="add-sample-code-to-implement-basic-event-hub-functionality"></a>イベント ハブの基本的な機能を実装するサンプル コードを追加する

このセクションでは、イベント ハブにイベントを送信するために必要な Java クラスを作成します。

### <a name="modify-the-main-application-class"></a>アプリケーションのメイン クラスを変更する

1. アプリのパッケージ ディレクトリでメイン アプリケーションの Java ファイルを探します。次に例を示します。

   `C:\SpringBoot\kafka\src\main\java\com\wingtiptoys\kafka\KafkaApplication.java`

   または

   `/users/example/home/kafka/src/main/java/com/wingtiptoys/kafka/KafkaApplication.java`

1. テキスト エディターでメイン アプリケーションの Java ファイルを開き、ファイルに次の行を追加します。

   ```java
   package com.wingtiptoys.kafka;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class KafkaApplication {
      public static void main(String[] args) {
         SpringApplication.run(KafkaApplication.class, args);
      }
   }
   ```

1. メイン アプリケーションの Java ファイルを保存して閉じます。


### <a name="create-a-new-class-for-the-source-connector"></a>ソース コネクタの新しいクラスを作成する

1. アプリのパッケージ ディレクトリに *KafkaSource.java* という名前の新しい Java ファイルを作成し、テキスト エディターでファイルを開いて、次の行を追加します。

   ```java
   package com.wingtiptoys.kafka;

   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.cloud.stream.annotation.EnableBinding;
   import org.springframework.cloud.stream.messaging.Source;
   import org.springframework.messaging.support.GenericMessage;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.bind.annotation.RestController;

   @EnableBinding(Source.class)
   @RestController
   public class KafkaSource {
      @Autowired
      private Source source;

      @PostMapping("/messages")
      public String sendMessage(@RequestBody String message) {
         this.source.output().send(new GenericMessage<>(message));
         return message;
      }
   }
   ```

1. *KafkaSource.java* ファイルを保存して閉じます。

### <a name="create-a-new-class-for-the-sink-connector"></a>シンク コネクタの新しいクラスを作成する

1. アプリのパッケージ ディレクトリに *KafkaSink.java* という名前の新しい Java ファイルを作成し、テキスト エディターでファイルを開いて、次の行を追加します。

   ```java
   package com.wingtiptoys.kafka;

   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.cloud.stream.annotation.EnableBinding;
   import org.springframework.cloud.stream.annotation.StreamListener;
   import org.springframework.cloud.stream.messaging.Sink;

   @EnableBinding(Sink.class)
   public class KafkaSink {
      private static final Logger LOGGER = LoggerFactory.getLogger(KafkaSink.class);

      @StreamListener(Sink.INPUT)
      public void handleMessage(String message) {
         LOGGER.info("New message received: " + message);
      }
   }
   ```

1. *KafkaSink.java* ファイルを保存して閉じます。

## <a name="build-and-test-your-application"></a>アプリケーションをビルドしてテストする

1. コマンド プロンプトを開き、ディレクトリを *pom.xml* ファイルが置かれているフォルダーに変更します。次に例を示します。

   `cd C:\SpringBoot\kafka`

   または

   `cd /users/example/home/kafka`

1. Spring Boot アプリケーションを Maven でビルドし、実行します。次に例を示します。

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. アプリケーションが実行されたら、*curl* を使用してアプリケーションをテストできます。次に例を示します。

   ```shell
   curl -X POST -H "Content-Type: text/plain" -d "hello" http://localhost:8080/messages
   ```
   アプリケーションのログに送信された "hello" が表示されます。 次に例を示します。

   ```shell
   [http-nio-8080-exec-2] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka version : 1.0.2
   [http-nio-8080-exec-2] INFO org.apache.kafka.common.utils.AppInfoParser - Kafka commitId : 2a121f7b1d402825
   [wingtiptoyshub.container-0-C-1] INFO com.wingtiptoys.kafka.KafkaSink - New message received: hello
   ```


> [!NOTE]
> 
> テストのために、*KafkaSource.java* を変更して、次の例のような簡単な HTML フォームを含めることができます。
> 
> ```java
> package com.wingtiptoys.kafka;
>    
> import org.springframework.beans.factory.annotation.Autowired;
> import org.springframework.cloud.stream.annotation.EnableBinding;
> import org.springframework.cloud.stream.messaging.Source;
> import org.springframework.messaging.support.GenericMessage;
> import org.springframework.web.bind.annotation.GetMapping;
> import org.springframework.web.bind.annotation.PostMapping;
> import org.springframework.web.bind.annotation.RequestBody;
> import org.springframework.web.bind.annotation.RequestParam;
> import org.springframework.web.bind.annotation.RestController;
> 
> @EnableBinding(Source.class)
> @RestController
> public class KafkaSource {
>   @Autowired
>   private Source source;
> 
>   @GetMapping("/")
>   public String sendForm() {
>     return "<html><body>" +
>       "<form action=\"/messages\" method=\"post\">" +
>       "<input type=\"text\" name=\"text\">" +
>       "<input type=\"submit\">" +
>       "</form></body><html>";
>     }
> 
>   @PostMapping("/messages")
>   public String sendMessage(@RequestBody String message) {
>     this.source.output().send(new GenericMessage<>(message));
>     return message;
>   }
> }
> ```
> 
> これにより、Web ブラウザーを使用してアプリケーションをテストできます。
> 
> ![Web ブラウザーを使用したアプリケーションのテスト][TB01]
> 
> フォームを送信すると、アプリケーションの結果が表示されます。
> 
> ![Web ブラウザーでのアプリケーションの応答][TB02]
> 

## <a name="next-steps"></a>次のステップ

Spring および Azure の詳細については、Azure ドキュメント センターで引き続き Spring に関するドキュメントをご確認ください。

> [!div class="nextstepaction"]
> [Azure の Spring](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>その他のリソース

Azure による Event Hub Stream Binder と Apache Kafka のサポートの詳細については、次の記事をご覧ください。

* [Azure Event Hubs とは](/azure/event-hubs/event-hubs-about)

* [Apache Kafka 用の Azure Event Hubs](/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview)

* [Azure portal を使用して Event Hubs 名前空間とイベント ハブを作成する](/azure/event-hubs/event-hubs-create)

* [Apache Kafka 対応イベント ハブの作成](/azure/event-hubs/event-hubs-create-kafka-enabled)

Java での Azure の使用の詳細については、「Java 開発者向けの Azure」および「[Azure DevOps と Java の操作]」を参照してください。

**[Spring Framework]** は Java 開発者のエンタープライズ レベルのアプリケーション作成を支援するオープンソース ソリューションです。 このプラットフォームで構築される特に知られたプロジェクトの 1 つが [Spring Boot] です。これによって、スタンドアロンの Java アプリケーションの作成方法が簡略化されます。 Spring Boot を使い始めた開発者を支援するために、<https://github.com/spring-guides/> では、サンプルの Spring Boot パッケージがいくつか用意されています。 基本的な Spring Boot プロジェクトの一覧から選択するだけでなく、 **[Spring Initializr]** は、開発者がカスタム Spring Boot アプリケーションの作成を開始できるように支援します。

<!-- URL List -->

[Apache Kafka]: http://kafka.apache.org
[無料の Azure アカウント]: https://azure.microsoft.com/pricing/free-trial/
[Azure DevOps と Java の操作]: /azure/devops/
[MSDN サブスクライバーの特典]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[IMG01]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-01.png
[IMG02]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-02.png
[IMG03]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-03.png
[IMG04]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-04.png
[IMG05]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-05.png
[IMG06]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-06.png
[IMG07]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-07.png
[IMG08]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-08.png

[SI01]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-project-01.png
[SI02]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-project-02.png
[SI03]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-project-03.png

[TB01]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/test-browser-01.png
[TB02]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/test-browser-02.png
