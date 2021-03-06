---
title: 手順 6:Azure App Service から VS Code にログをストリーム配信する
description: チュートリアルの手順 6、アプリのログを Visual Studio Code にストリーム配信する
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: f2e19a52b673ddcef927165191d610776e06a32f
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "80441897"
---
# <a name="6-stream-logs-from-azure-app-service-into-visual-studio-code"></a>6: Azure App Service から Visual Studio Code にログをストリーム配信する

[前の手順: アプリをデプロイする](tutorial-deploy-app-service-on-linux-05.md)

この手順を使用して、Azure App Service から Visual Studio Code にログをストリーム配信します。

1. Visual Studio Code で **[Azure:App Service]** エクスプローラーを開き、App Service を右クリックし、 **[Start streaming logs]\(ログのストリーム配信を開始する\)** を選択します。

   ![App Service エクスプローラーからログのストリーム配信を開始する](media/deploy-azure/start-streaming-logs-in-visual-studio-code.png)

1. ファイルのログ記録を有効にして Web アプリを再起動するよう求められたら、 **[はい]** を選択します。 アプリの再起動中、VS Code の**出力**ウィンドウに進行状況が表示されます。 ログの有効化は、1 回限りのプロセスです。

1. ログを有効にしたら、App Service を右クリックし、再度、 **[Start streaming logs]\(ログのストリーム配信を開始する\)** を選択します。 VS Code の**出力**ウィンドウに "Starting Live Log Stream (ライブ ログ ストリームを開始しています)" と表示され、ログ出力が見え始めます。 ブラウザーで Web アプリを最新の情報に更新してみてください。さらに多くのログ情報が生成されます。

1. (ログを無効にせずに) ログのストリーム配信を停止するには、 **[Azure:App Service]** エクスプローラーでアプリを右クリックし、 **[Stop streaming logs]\(ログのストリーム配信を停止する\)** を選択します。

> [!div class="nextstepaction"]
> [ログを確認しました - 手順 7 に進む >>>](tutorial-deploy-app-service-on-linux-07.md)

[問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=06-stream-logs)
