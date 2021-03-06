---
title: "Azure Portal で Azure Data Lake Analytics の使用を開始する | Microsoft Docs"
description: "Azure Portal を使用して Data Lake Analytics アカウントを作成し、U-SQL で Data Lake Analytics ジョブを作成して、ジョブを送信する方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: eb85d8ef6b29605d7e26b0d2139a4a95c35141fb
ms.contentlocale: ja-jp
ms.lasthandoff: 06/01/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>チュートリアル: Azure Portal で Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Portal を使用して Azure Data Lake Analytics アカウントを作成し、[U-SQL](data-lake-analytics-u-sql-get-started.md) でジョブを定義して、Data Lake Analytics サービスにジョブを送信する方法について説明します。 Data Lake Analytics の詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、**Azure サブスクリプション**が必要です。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する

次に、Data Lake Analytics アカウントと Data Lake Store アカウントを同時に作成します。  この手順は単純な作業で、所要時間は約 60 秒です。

1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. **[新規]** >  **[インテリジェンス + 分析]** > **[Data Lake Analytics]** の順にクリックします。
3. 次の項目の値を選択します。
   * **名前**: Data Lake Analytics アカウントに名前を付けます (英小文字と数字のみ使用できます)。
   * **サブスクリプション**: Analytics アカウントに使用する Azure サブスクリプションを選択します。
   * **リソース グループ**。 既存の Azure リソース グループを選択するか、新しいものを作成します。
   * **場所**。 Data Lake Analytics アカウントの Azure データ センターを選択します。
   * **Data Lake Store**: 以下の指示に従って、新しい Data Lake Store アカウントを作成するか、既存のものを選択します。 
4. 必要に応じて、Data Lake Analytics アカウントの価格レベルを選択します。
5. ページの下部にある **[Create]**」を参照してください。 

## <a name="create-and-submit-data-lake-analytics-jobs"></a>Data Lake Analytics ジョブの作成と送信
ソース データの準備ができたら、U-SQL スクリプトの開発を開始できます。  

**ジョブを送信するには**

1. Data Lake Analytics アカウントから **[新しいジョブ]** をクリックします。
2. **ジョブ名**を入力し、次の U-SQL スクリプトを入力します。

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();
```


この U-SQL スクリプトでは、**Extractors.Tsv()** を使用してソース データ ファイルを読み取ってから、**Outputters.Csv()** を使用して csv ファイルを作成します。

1. **[ジョブの送信]**をクリックします。   
2. ジョブの状態が **[成功]**に変わるまで待機します。
3. ジョブが失敗した場合は、[Data Lake Analytics ジョブの監視とトラブルシューティング](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)に関する記事をご覧ください。
4. **[出力]** タブをクリックし、`SearchLog-from-Data-Lake.csv` をクリックします。 

## <a name="see-also"></a>関連項目

* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。

