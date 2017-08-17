---
title: "Azure Data Factory 複製活動容錯 - 跳過不相容的資料列 | Microsoft Docs"
description: "在複製期間使用 Azure Data Factory 略過相容的資料列，了解容錯功能。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: d613537657af3bbe379a53e92532bf6b184d762b
ms.contentlocale: zh-tw
ms.lasthandoff: 07/20/2017

---
# <a name="copy-activity-fault-tolerance---skip-incompatible-rows"></a>複製活動容錯 - 略過不相容的資料列

使用[複製活動](data-factory-data-movement-activities.md)，在來源和接收資料存放區之間複製資料時，您會有不同的選項處理不相容的資料列。 您可以選擇在遇到不相容的資料時中止並放棄複製活動 (預設行為)，或略過這些不相容的資料列，繼續複製所有資料。 此外，您也可以選擇將不相容的資料列記錄在 Azure Blob，以便檢查失敗的原因、修正資料來源上的資料，然後重試。

## <a name="supported-scenarios"></a>支援的案例
目前，複製活動支援在複製期間偵測、略過和記錄下列不相容的情況：

- **來源和接收原生類型之間的資料類型不相容**

    範例：要從 Azure Blob 的 CSV 檔案複製到 Azure SQL Database，且在 Azure SQL Database 中定義的結構描述有三個 *INT* 類型資料行。 然後，成功複製來源 CSV 檔案中有數值資料的資料列 (例如 `123,456,789`)，同時將包含非數值的資料列 (例如 `123,456,abc`) 當做不相容的資料列略過。

- **來源與接收之間的資料行數目不相符**

    範例：要從 Azure Blob 的 CSV 檔案複製到 Azure SQL Database，且在 SQL Azure 中定義的結構描述有六個資料行。 然後，成功複製來源 CSV 檔案中有六個資料行的資料列，同時將其他資料行數的資料列當做不相容的資料列略過。

- **寫入關聯式資料庫時發生主索引鍵違規**

    範例：要從 SQL Server 複製到 Azure SQL Database，有在接收 Azure SQL Database 中定義的主索引鍵，但沒有在來源 SQL Server 中定義的這類主索引鍵。 來源中可以存在的重複資料列，在寫入接收時不被允許。 複製活動只會將第一個資料列複製到接收，但略過從來源到接收第二個或更多主索引鍵值重複的資料列。

## <a name="configuration"></a>組態
下例提供有關如何設定在複製活動中略過不相容資料列的 JSON 定義：

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| 屬性 | 說明 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| enableSkipIncompatibleRow | 啟用或停用在複製期間略過不相容的資料列。 | True<br/>FALSE (預設值) | 否 |
| redirectIncompatibleRowSettings | 當您想要記錄不相容的資料列時，可指定的一組屬性。 | &nbsp; | 否 |
| 預設容器 | Azure 儲存體的連結服務，儲存包含所有略過資料列的記錄。 | 指定 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 或 [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 連結服務的名稱，以代表儲存記錄檔所使用的儲存體執行個體。 | 否 |
| 路徑 | 包含所有略過資料列的記錄檔路徑。 | 指定要記錄不相容資料的 Blob 儲存體路徑。 如不提供路徑，服務會為您建立容器。 | 否 |

## <a name="monitoring"></a>監視
複製活動執行完成之後，您會在 [監視] 區段中看到略過的資料列數目，如下所示：

![略過不相容的資料列監視](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

如果設定記錄不相容的資料列，以找出略過的內容和不相容的根本原因，您可在此路徑找到記錄檔：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`。

原始資料和對應的錯誤都會記錄在檔案中。 記錄檔內容範例如下所示：
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).,
```

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure Data Factory 複製活動，請參閱[使用複製活動來移動資料](data-factory-data-movement-activities.md)。
