---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: cc634f89170f4db6f961da91e3eb3abe7393539d
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830990"
---
### <a name="validate-that-the-supported-java-version-works-correctly"></a>サポートされている Java バージョンが正しく動作することを検証する

Azure への WebLogic の移行パスのすべてにおいて、パスごとに異なる特定の Java バージョンが必要です。 そのサポートされているバージョンを使用してアプリケーションを正常に実行できることを検証する必要があります。

現在のバージョンを取得するには、実稼働サーバーにサインインし、次のコマンドを実行します。

```bash
java -version
```

> [!NOTE]
> Azure 仮想マシン上の WebLogic に移行する場合、特定の Java バージョンの要件は、仮想マシンにプレインストールされている Java によって決まります。