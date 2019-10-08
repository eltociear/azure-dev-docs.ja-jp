---
title: Visual Studio Code から静的 Node.js Web サイトを Azure にデプロイする
description: チュートリアル パート 1、概要と前提条件。
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: kraigb
ms.openlocfilehash: 45dfb1f32d98385b2cb944340b4601de804e133f
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685911"
---
# <a name="deploy-a-static-website-to-azure-from-visual-studio-code"></a>Visual Studio Code から静的 Web サイトを Azure にデプロイする

このチュートリアルでは、[Azure Storage](https://docs.microsoft.com/azure/storage) を使用して、静的 Web サイトを作成し、Azure にデプロイします。 静的 Web サイトは、HTML、CSS、JavaScript、および画像やフォントなどのその他のファイルで構成されています。 静的サイトは通常、Angular または React で記述されたシングルページ アプリケーション ([SPA](https://en.wikipedia.org/wiki/Single-page_application)) です。 アプリの設計方法に関係なく、これらのファイルは、Web サーバーを使用するのではなく、*ストレージ*から直接ホストして提供します。 ストレージでのホスティングは、Web サーバーを維持するよりも簡単でコストが低くなります。

> [!NOTE]
> Node.js/Express app などの独自のサーバー コードがある場合は、代わりに [App Service のチュートリアル](tutorial-vscode-azure-app-service-node-01.md)に従ってください。

## <a name="prerequisites"></a>前提条件

- [Azure サブスクリプション](#azure-subscription)。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Azure Storage の拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)。
- [Node.js と npm](https://nodejs.org/en/download)、Node.js パッケージ マネージャー。 (この要件は、サンプル プロジェクトを生成するためだけに使用されます。 既にアプリ コードがある場合は、Node.js をインストールする必要はありません。)

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-azurestorage">Azure Storage 拡張機能をインストールする</a>

### <a name="azure-subscription"></a>Azure サブスクリプション

Azure サブスクリプションをお持ちでない場合は、無料アカウントに[今すぐご登録](https://azure.microsoft.com/en-us/free/?utm_source=campaign&utm_campaign=vscode-tutorial-static-website&mktingSource=vscode-tutorial-static-website)いただけます。Azure クレジットの 200 ドルを使用してさまざまな組み合わせのサービスをお試しください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [前提条件をインストールしました](tutorial-vscode-static-website-node-02.md) [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=getting-started)