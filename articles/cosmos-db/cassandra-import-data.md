---
title: "將 Cassandra 資料匯入到 Azure Cosmos DB | Microsoft Docs"
description: "了解如何使用 CQL Copy 命令將 Cassandra 資料複製到 Azure Cosmos DB。"
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: 
ms.assetid: eced5f6a-3f56-417a-b544-18cf000af33a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 21168d0862cfdaaaced60fa80a2dc04859f49550
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure Cosmos DB：匯入 Cassandra 資料

本教學課程會指示您使用 Cassandra 查詢語言 (CQL) COPY 命令，將 Cassandra 資料匯入到 Azure Cosmos DB。 

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 擷取連接字串
> * 使用 cqlsh COPY 命令匯入資料
> * 使用 Spark 連接器匯入 

# <a name="prerequisites"></a>必要條件

* 安裝 [Apache Cassandra](http://cassandra.apache.org/download/)，並特別確認 *cqlsh* 已存在。
* 增加輸送量︰資料移轉的時間長短取決於您為資料表佈建的輸送量。 針對較大資料移轉，請務必增加輸送量。 完成移轉之後，再降低輸送量以節省成本。 如需在 [Azure 入口網站](https://portal.azure.com)增加輸送量的詳細資訊，請參閱[設定 Azure Cosmos DB 容器的輸送量](set-throughput.md)。
* 啟用 SSL：Azure Cosmos DB 有嚴格的安全性需求和標準。 與您的帳戶互動時，請務必啟用 SSL。 當您透過 SSH 使用 CQL 時，會有選項要您提供 SSL 資訊。 

## <a name="find-your-connection-string"></a>尋找您的連接字串

1. 在最左側的 [Azure 入口網站](https://portal.azure.com)中，按一下 [Azure Cosmos DB]。

2. 在 [訂用帳戶] 窗格中，選取您的帳戶名稱。

3. 按一下 [連接字串]。 右窗格中有您成功連接到您的帳戶所需的所有資訊。

    ![連接字串頁面](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>使用 cqlsh COPY

若要將 Cassandra 資料匯入到 Azure Cosmos DB 以搭配使用 Cassandra API，請使用下列指示：

1. 使用入口網站中的連線資訊登入 cqhsh。
2. 使用 [CQL COPY 命令](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) 將本機資料複製到 Apache Cassandra API 端點。 請確定來源和目標位在相同資料中心，以減少延遲問題。

### <a name="guide-for-moving-data-with-cqlsh"></a>使用 cqlsh 移動資料的指南

1. 預先建立並調整您的資料表：
    * 依預設，Azure Cosmos DB 會佈建每秒 1,000 個要求單位 (RU/s) 的新 Cassandra API 資料表 (CQL 式的建立則會以 400 RU/s 佈建)。 使用 cqlsh 開始移轉之前，請先從 [Azure 入口網站](https://portal.azure.com)或從 cqlsh 預先建立所有資料表。 

    * 從 [Azure 入口網站](https://portal.azure.com)中，針對移轉時間，將資料表的輸送量從預設輸送量 (400 或 1000 RU/s) 增加至 10,000 RU/s。 藉由較高的輸送量，您可以避免節流，並花費較少的時間進行移轉。 由於 Azure Cosmos DB 是以每小時計費，所以您可以在移轉之後立即減少輸送量以節省成本。

2. 判斷作業的 RU 費用。 您可以選擇使用 Azure Cosmos DB Cassandra API SDK 來執行此項目。 此範例會示範取得 RU 費用的 .NET 版本。 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. 判斷從您的電腦到 Azure Cosmos DB 服務的延遲。 如果您在 Azure 資料中心內，延遲的毫秒數應是偏低的單一數字。 如果您在 Azure 資料中心以外的地方，則您可以使用 psping 或 azurespeed.com 從您的位置取得延遲的約略值。   

4. 計算可提供良好效能的適當參數值 (NUMPROCESS、INGESTRATE、MAXBATCHSIZE 或 MINBATCHSIZE)。 

5. 執行最後的移轉命令。 執行此命令會假設您已經使用連接字串資訊來啟動 cqlsh。

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>使用 Spark 匯入資料

對於位於 Azure 虛擬機器中現有叢集中的資料，使用 Spark 匯入資料也是可行的選項。 這需要將 Spark 設定為一次性或定期擷取的中繼項目。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何完成下列工作：

> [!div class="checklist"]
> * 擷取連接字串
> * 使用 cql copy 命令匯入資料
> * 使用 Spark 連接器匯入 

您現在可以繼續進行＜概念＞一節，了解有關 Azure Cosmos DB 的詳細資訊。 

> [!div class="nextstepaction"]
>[Azure Cosmos DB 中的 Tunable 資料一致性層級](../cosmos-db/consistency-levels.md)
