---
title: "Azure Functions 外部資料表繫結 (預覽) | Microsoft Docs"
description: "使用 Azure Functions 中的外部資料表繫結"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 7b226aa4ec71535aa0222389aacd74764a80021a
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-external-table-binding-preview"></a>Azure Functions 外部資料表繫結 (預覽)
本文說明如何使用內建繫結，在您的函數內操作 SaaS 提供者 (例如 SharePoint, Dynamics) 上的表格式資料。 Azure Functions 支援外部資料表的輸入和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API 連線

資料表繫結會運用外部 API 連線以向第三方 SaaS 提供者進行驗證。 

指派繫結時，您可以建立新的 API 連線，或是使用相同資源群組內的現有 API 連線

### <a name="supported-api-connections-tables"></a>支援的 API 連線 (資料表)

|連接器|觸發程序|輸入|輸出|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 for Operations (英文)](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV (英文)](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google 試算表](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service (英文)](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x


> [!NOTE]
> 外部資料表連線也可以用在 [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list) 中

### <a name="creating-an-api-connection-step-by-step"></a>建立 API 連線：逐步解說

1. 建立函數 > 自訂函數 ![建立自訂函數](./media/functions-bindings-storage-table/create-custom-function.jpg)
1. 案例 `Experimental` > `ExternalTable-CSharp` 範本 > 建立新的 `External Table connection`
![選擇資料表輸入範本](./media/functions-bindings-storage-table/create-template-table.jpg)
1. 選擇您的 SaaS 提供者 > 選擇/建立連線 ![設定 SaaS 連線](./media/functions-bindings-storage-table/authorize-API-connection.jpg)
1. 選取您的 API 連線 > 建立函數 ![建立資料表函數](./media/functions-bindings-storage-table/table-template-options.jpg)
1. 選取 `Integrate` > `External Table`
    1. 設定連線以使用目標資料表。 這些設定會依 SaaS 提供者不同而有所差異。 它們會在下方的[資料來源設定](#datasourcesettings)中說明
![設定資料表](./media/functions-bindings-storage-table/configure-API-connection.jpg)

## <a name="usage"></a>使用量

此範例會連線至名為 "Contact"，並具有 ID, LastName 及 FirstName 資料行的資料表。 程式碼會在資料表中列出 Contact 實體，並記錄名字和姓氏。

### <a name="bindings"></a>繫結
```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```
`entityId` 針對資料表繫結必須為空白。

`ConnectionAppSettingsKey` 會識別儲存 API 連接字串的應用程式設定。 應用程式設定會在您於整合 UI 中新增 API 連線時自動建立。

表格式連接器會提供資料集，每個資料集皆包含資料表。 預設資料集的名稱為 "default"。 以下列出各種 SaaS 提供者的資料集和資料表標題：

|連接器|Dataset|資料表|
|:-----|:---|:---| 
|**SharePoint**|網站|SharePoint 清單
|**SQL**|資料庫|資料表 
|**Google 試算表**|試算表|工作表 
|**Excel**|Excel 檔案|工作表 

<!--
See the language-specific sample that copies the input file to the output file.

* [C#](#incsharp)
* [Node.js](#innodejs)

-->
<a name="incsharp"></a>

### <a name="usage-in-c"></a>C# 中的使用方式 #

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retreive table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

<!--
<a name="innodejs"></a>

### Usage in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```
-->
<a name="datasourcesettings"></a>
## 資料來源設定

### <a name="sql-server"></a>SQL Server

建立和填入 Contact 資料表的指令碼如下。 dataSetName 為 "default"。

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets"></a>Google 試算表
在 Google 文件中，建立具有名為 `Contact` 之工作表的試算表。 連接器不能使用試算表顯示名稱。 內部名稱 (以粗體顯示) 必須做為 dataSetName 使用，例如：`docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** 將資料行名稱 `Id`, `LastName`, `FirstName` 新增至第一個資料列，然後將資料填入後續資料列。

### <a name="salesforce"></a>Salesforce
dataSetName 為 "default"。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
