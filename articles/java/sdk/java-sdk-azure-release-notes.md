---
title: Azure Management Libraries for Java リリース ノート | Microsoft Docs
description: Azure Management Libraries for Java の新機能や重大な変更が記載されています。
keywords: Azure,  Java, API, リファレンス,  ノート,  更新, 非推奨
ms.assetid: 1f128cf9-f747-4344-84e1-f9964709deb8
ms.topic: reference
ms.date: 3/06/2016
ms.openlocfilehash: 69c2c29935f9333dd1d31b26b0941e0446ca5504
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82105203"
---
# <a name="release-notes"></a>リリース ノート 

## <a name="october-5-2017---130"></a>2017 年 10 月 5 日 - 1.3.0 

バージョン 1.3.0 は、以前のリリースで一般公開 (安定) 段階に達したサービスおよび機能の使用について、以前のバージョンと下位互換性があります。

これらのサービスに関する以前のバージョンからの重大な変更には、@Beta という注釈が付いています。


### <a name="generally-available-in-v13"></a>V1.3 での一般提供

以前のリリースではまだベータ版だった一部の API が GA になりました。具体的な内容は次のとおりです。

- 非同期メソッド
- これまで Beta 版であった CDN のすべてのメソッド
- これまで Beta 版であった Application Gateway のすべてのメソッドおよびインターフェイス

  ライブラリの一部については引き続きプレビューとなります。 現在のライブラリの状態については、次の表を参照してください。

サービスまたは機能 | GA として利用可能 | プレビューとして利用可能 
---------|---------|---------|-
Compute  | 仮想マシンと VM の拡張機能、仮想マシン スケール セット、マネージド ディスク   | Azure Container Service、Azure Container Registry 
ストレージ   |  ストレージ アカウント       |    暗号化     
SQL Database  | データベース、ファイアウォール、エラスティック プール              
ネットワーク    |  仮想ネットワーク、ネットワーク インターフェイス、IP アドレス、ルーティング テーブル、ネットワーク セキュリティ グループ、DNS、Traffic Manager、Application Gateway  |    ロード バランサー、ネットワーク ピアリング、仮想ネットワーク ゲートウェイ、ネットワーク ウォッチャー 
その他のサービス    |  リソース マネージャー、Key Vault、Redis、CDN、Batch       |  Web アプリ、関数アプリ、Service Bus、Graph RBAC、Cosmos DB、検索  
基礎     |   認証 - コア、非同期メソッド、管理対象サービス ID      |      |

> ライブラリのクラス、インターフェイス、メソッド レベルでは、プレビュー機能に `@Beta` という注釈が付いています。 これらの機能は、変更されることがあります。 将来的に、何らかの変更が行われたり、削除されたりする可能性があります。

### <a name="import-with-maven"></a>Maven でのインポート

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure</artifactId>
    <version>1.3.0</version>
</dependency>
```

### <a name="get-help-and-give-feedback"></a>質問とフィードバック

独自のコードでライブラリを使用する方法については、[Stack Overflow](http://stackoverflow.com/questions/tagged/azure-java-sdk) コミュニティを参照してください。 バグを見つけられた場合や、これらのライブラリの改善に向けた提案をお持ちの場合は、[GitHub](https://github.com/Azure/azure-sdk-for-java/issues) 経由でお寄せください。


