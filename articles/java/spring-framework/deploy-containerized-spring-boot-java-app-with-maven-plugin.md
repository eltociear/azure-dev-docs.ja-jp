---
title: Maven を使用して Spring Boot アプリを Azure にデプロイする
description: Azure Web Apps 用の Maven プラグインを使って、Spring Boot アプリを Azure にデプロイする方法について説明します。
services: app-service
documentationcenter: java
ms.date: 12/19/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: seo-java-july2019, seo-java-august2019
ms.openlocfilehash: b926b0bd1cb401f12f927abfea112e6004988812
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "81668398"
---
# <a name="use-maven-for-azure-web-apps-to-deploy-a-containerized-spring-boot-app-to-azure"></a>Azure Web Apps 用の Maven を使用して、コンテナー化された Spring Boot アプリを Azure にデプロイする

この記事では、[Azure Web Apps 用の Maven プラグイン](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin)を使用して、Docker コンテナーの Spring Boot サンプル アプリケーションを Azure App Services にデプロイする方法について説明します。

> [!NOTE]
> 
> [Apache Maven](https://maven.apache.org/) 用の Azure Web Apps 用 Maven プラグイン は、Maven プロジェクトに Azure App Service をシームレスに統合し、開発者が Web アプリを Azure App Service にデプロイするプロセスを効率化します。
> 
> Azure Web Apps の Maven プラグインは現在プレビューとして提供されています。 今後、機能が追加される予定ですが、現在は FTP 発行のみがサポートされています。
> 

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、次の前提条件を満たす必要があります。

* Azure サブスクリプション。Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典]を有効にするか、または[無料の Azure アカウント]にサインアップできます。
* [Azure コマンド ライン インターフェイス (CLI)]。
* サポートされている Java Development Kit (JDK)。 Azure での開発時に使用可能な JDK の詳細については、<https://aka.ms/azure-jdks> を参照してください。
* Apache の [Maven] 構築ツール (バージョン 3)。
* [Git] クライアント。
* [Docker] クライアント。

> [!NOTE]
>
> このチュートリアルには仮想化要件があるため、仮想マシンでこの記事の手順を実行することはできません。仮想化機能を有効にした物理コンピューターを使用する必要があります。
>

## <a name="clone-the-sample-spring-boot-on-docker-web-app"></a>Docker Web アプリの Spring Boot サンプルの複製

このセクションでは、コンテナー化された Spring Boot アプリケーションを複製してローカルでテストします。

1. コマンド プロンプトまたはターミナル ウィンドウを開き、Spring Boot アプリケーションを保持するためのローカル ディレクトリを作成して、次の例のようにそのディレクトリに移動します。
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- または --
   ```shell
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. [Docker での Spring Boot の使用開始]のサンプル プロジェクトを今作成したディレクトリに複製します。次に例を示します。
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker
   ```

1. 完成したプロジェクトにディレクトリを変更します。次に例を示します。
   ```shell
   cd gs-spring-boot-docker/complete
   ```

1. Maven を使用して JAR ファイルを構築します。次に例を示します。
   ```shell
   mvn clean package
   ```

1. Web アプリを作成したら、次の例のように Maven を使って Web アプリを起動します。
   ```shell
   mvn spring-boot:run
   ```

1. Web アプリのテストは、Web ブラウザーを使用してアプリをローカルで参照して行います。 たとえば、curl を使うことができる場合は次のようなコマンドを実行できます。
   ```shell
   curl http://localhost:8080
   ```

1. 次のメッセージが表示されるはずです。**Hello Docker World**

## <a name="create-an-azure-service-principal"></a>Azure サービス プリンシパルを作成する

このセクションでは、Azure にコンテナーをデプロイするときに、Maven プラグインが使用する Azure サービス プリンシパルを作成します。

1. コマンド プロンプトを開きます。

2. Azure CLI を使って、Azure アカウントにサインインします。
   ```shell
   az login
   ```
   指示に従って、サインインを完了します。

3. Azure サービス プリンシパルを作成します。
   ```shell
   az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp"
   ```
   各値の説明:

   | パラメーター  |                    説明                     |
   |------------|----------------------------------------------------|
   | `uuuuuuuu` | サービス プリンシパルのユーザー名を指定します。 |
   | `pppppppp` | サービス プリンシパルのパスワードを指定します。  |


4. Azure が次の例に類似する JSON で応答します。
   ```json
   {
      "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "displayName": "uuuuuuuu",
      "name": "http://uuuuuuuu",
      "password": "pppppppp",
      "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

   > [!NOTE]
   >
   > Maven プラグインを構成して Azure にコンテナーをデプロイするときに、この JSON の応答にある値を使用します。 `aaaaaaaa``uuuuuuuu``pppppppp``tttttttt` はプレースホルダーの値であり、次のセクションで Maven の `settings.xml` ファイルを構成するときに、これらの値と値の各要素を簡単にマップできるよう使用されています。
   >
   >

## <a name="configure-maven-to-use-your-azure-service-principal"></a>Azure サービス プリンシパルを使用するように Maven を構成する

このセクションでは、Azure サービス プリンシパルの値を使用して、コンテナーを Azure にデプロイするときに Maven が使用する認証を構成します。

1. Maven の `settings.xml` ファイルをテキスト エディターで開きます。このファイルは次の例のようなパスに存在していることがあります。
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

2. このチュートリアルの前のセクションで説明した Azure サービス プリンシパルの設定を、次の例のように *settings.xml* ファイルの `<servers>` コレクションに追加します。

   ```xml
   <servers>
      <server>
        <id>azure-auth</id>
         <configuration>
            <client>aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa</client>
            <tenant>tttttttt-tttt-tttt-tttt-tttttttttttt</tenant>
            <key>pppppppp</key>
            <environment>AZURE</environment>
         </configuration>
      </server>
   </servers>
   ```
   各値の説明:

   |     要素     |                                                                                   説明                                                                                   |
   |-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |     `<id>`      |                                Web アプリを Azure にデプロイするとき、セキュリティ設定を検索するために Maven が使う一意の名前を指定します。                                |
   |   `<client>`    |                                                             サービス プリンシパルの `appId` 値が含まれています。                                                             |
   |   `<tenant>`    |                                                            サービス プリンシパルの `tenant` 値が含まれています。                                                             |
   |     `<key>`     |                                                           サービス プリンシパルの `password` 値が含まれています。                                                            |
   | `<environment>` | ターゲットの Azure クラウド環境を定義します。この例では `AZURE` です (環境の全リストは、「[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]」のドキュメントに記載しています) |


3. *settings.xml* ファイルを保存して閉じます。

## <a name="optional-deploy-your-local-docker-file-to-docker-hub"></a>省略可能: ローカルの Docker ファイルを Docker Hub にデプロイします

Docker アカウントがあれば、Docker コンテナー イメージをローカルで構築して Docker Hub にプッシュできます。 そのためには、次の手順に従います。

1. Spring Boot アプリケーションの `pom.xml` ファイルをテキスト エディターで開きます。

1. `<containerSettings>` 要素の `<imageName>` 子要素を見つけます。

1. `${docker.image.prefix}` の値を Docker アカウント名に更新します。
   ```xml
   <containerSettings>
      <imageName>mydockeraccountname/${project.artifactId}</imageName>
   </containerSettings>
   ```

1. 次のいずれかのデプロイ方法を選択してください。

   * Maven でコンテナー イメージをローカルで構築し、Docker を使用してコンテナーを Docker Hub にプッシュします。
      ```shell
      mvn clean package docker:build
      docker push
      ```

   * [Maven 用の Docker プラグイン]がインストールされていれば、`-DpushImage` パラメーターを使用してコンテナー イメージを自動で Docker Hub に構築できます。
      ```shell
      mvn clean package docker:build -DpushImage
      ```

## <a name="optional-customize-your-pomxml-before-deploying-your-container-to-azure"></a>省略可能: コンテナーを Azure にデプロイする前に pom.xml をカスタマイズします

Spring Boot アプリケーションの `pom.xml` ファイルをテキスト エディターで開き、`azure-webapp-maven-plugin` の `<plugin>` 要素を見つけます。 この要素は次の例のようになっています。

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>0.1.3</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>maven-plugin</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         </containerSettings>
         <appSettings>
            <property>
               <name>PORT</name>
               <value>8080</value>
            </property>
         </appSettings>
      </configuration>
   </plugin>
   ```

Maven プラグイン用に変更できる値は複数あります。これらの要素に関する詳しい説明はそれぞれ「[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]」のドキュメントに記載されています。 この記事でも、次のように重要な値については説明します。

| 要素 | 説明 |
|---|---|
| `<version>` | [Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]のバージョンを指定します。 [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) に表示されているバージョンを確認して、最新バージョンを使用していることを確認してください。 |
| `<authentication>` | Azure の認証情報を指定します。この例では `azure-auth` を含む `<serverId>` 要素が認証情報です。Maven はこの値を、この記事の前のセクションで定義した Maven の*settings.xml* ファイル内にある Azure サービス プリンシパルを見つけるために使います。 |
| `<resourceGroup>` | ターゲット リソース グループを指定します。この例では `maven-plugin` です。 リソース グループが存在しない場合は、デプロイ中に新しいリソース グループが作成されます。 |
| `<appName>` | Web アプリのターゲット名を指定します。 この例では、ターゲット名は `maven-linux-app-${maven.build.timestamp}` です。混乱を避けるため、この例ではサフィックスの `${maven.build.timestamp}` を追加しています (タイムスタンプは省略可能です。アプリ名には一意の文字列を指定できます)。 |
| `<region>` | ターゲット リージョンを指定します。この例では `westus` です (完全なリストについては、「[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]」 をご覧ください)。 |
| `<appSettings>` | Azure に Web アプリをデプロイするときに使用するために、Maven 用の一意の設定を指定します。 この例では、`<property>` 要素には、アプリのポートを指定する子要素の名前と値のペアが含まれています。 |

> [!NOTE]
>
> 既定と異なるポートに変更する場合のみ、この例のポート番号を変更する設定が必要になります。
>

## <a name="build-and-deploy-your-container-to-azure"></a>Azure にコンテナーを構築してデプロイする

この記事の前のセクションで説明した設定をすべて構成したら、次は Azure にコンテナーをデプロイします。 そのためには、次の手順を実行してください。

1. *pom.xml* ファイルを変更する場合は、以前使っていたコマンド プロンプトまたはターミナル ウィンドウで、次の例のように Maven を使って JAR ファイルをリビルドします。
   ```shell
   mvn clean package
   ```

1. Maven を使って次の例のように Azure に Web アプリをデプロイします。
   ```shell
   mvn azure-webapp:deploy
   ```

Maven が Web アプリを Azure にデプロイします。Web アプリが存在しない場合は新たに作成されます。

> [!NOTE]
>
> デプロイ開始時に、*pom.xml* ファイルの `<region>` 要素で指定したリージョンに十分な数の使用可能なサーバーがない場合は、次の例のようなエラーが表示されることがあります。
>
> ```xml
> [INFO] Start deploying to Web App maven-linux-app-20170804...
> [INFO] ------------------------------------------------------------------------
> [INFO] BUILD FAILURE
> [INFO] ------------------------------------------------------------------------
> [INFO] Total time: 31.059 s
> [INFO] Finished at: 2017-08-04T12:15:47-07:00
> [INFO] Final Memory: 51M/279M
> [INFO] ------------------------------------------------------------------------
> [ERROR] Failed to execute goal com.microsoft.azure:azure-webapp-maven-plugin:0.1.3:deploy (default-cli) on project gs-spring-boot-docker: null: MojoExecutionException: CloudException: OnError while emitting onNext value: retrofit2.Response.class
> ```
>
> この場合、別のリージョンを指定し、Maven コマンドを再実行してアプリケーションをデプロイできます。
>
>

Web アプリのデプロイが完了すると、[Azure Portal] を使って Web アプリを管理できるようになります。

* Web アプリは **App Services** に一覧表示されます。

   ![Azure Portal の App Services に一覧表示される Web アプリ][AP01]

* Web アプリの URL は、Web アプリの **[概要]** に一覧表示されます。

   ![Web アプリの URL の決定][AP02]

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>次のステップ

Spring および Azure の詳細については、Azure ドキュメント センターで引き続き Spring に関するドキュメントをご確認ください。

> [!div class="nextstepaction"]
> [Azure の Spring](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>その他のリソース

この記事で説明しているさまざまなテクノロジの詳細については、次の記事をご覧ください。

* [Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]

* [Azure CLI から Azure へのログイン](/azure/xplat-cli-connect)

* [Azure Web Apps 用の Maven プラグインを使用して、Spring Boot アプリを Azure App Service にデプロイする方法](deploy-spring-boot-java-app-with-maven-plugin.md)

* [Azure CLI 2.0 で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven の設定リファレンス](https://maven.apache.org/settings.html)

* [Maven 用の Docker プラグイン]

Java での Azure の使用の詳細については、「[Java 開発者向けの Azure]」および「[Azure DevOps と Java の操作]」を参照してください。

<!-- URL List -->

[Azure コマンド ライン インターフェイス (CLI)]: /cli/azure/overview
[Java 開発者向けの Azure]: /azure/developer/java/
[Azure Portal]: https://portal.azure.com/
[Docker]: https://www.docker.com/
[Maven 用の Docker プラグイン]: https://github.com/spotify/docker-maven-plugin
[無料の Azure アカウント]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Azure DevOps と Java の操作]: /azure/devops/
[Maven]: https://maven.apache.org/
[MSDN サブスクライバーの特典]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: https://projects.spring.io/spring-boot/
[Docker での Spring Boot の使用開始]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/
[Maven Plugin for Azure Web Apps (Azure Web Apps 用の Maven プラグイン)]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[AP01]: media/deploy-containerized-spring-boot-java-app-with-maven-plugin/AP01.png
[AP02]: media/deploy-containerized-spring-boot-java-app-with-maven-plugin/AP02.png
