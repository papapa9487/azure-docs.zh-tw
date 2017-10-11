---
title: "Azure Analysis Services 中支援的資料來源 | Microsoft Docs"
description: "說明 Azure Analysis Services 中資料模型支援的資料來源。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: 8bd6c3b5a923ce2f3cd0f60af82e59c5cc27cbb4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services 中支援的資料來源
Azure Analysis Services 伺服器支援連線到您組織中的雲端和內部部署資料來源。 其他支援的資料來源持續新增。 請經常回來查看。 

目前支援下列資料來源：

| 雲端  |
|---|
| Azure Blob 儲存體*  |
| Azure SQL Database  |
| Azure 資料倉儲 |


| 內部部署  |   |   |   |
|---|---|---|---|
| Access 資料庫  | 資料夾* | Oracle 資料庫  | Teradata 資料庫 |
| Active Directory*  | JSON 文件*  | Postgre SQL 資料庫*  |XML 表格* |
| Analysis Services  | Lines from binary*  | SAP HANA*  |
| 分析平台系統  | MySQL Database  | SAP Business Warehouse*  | |
| Dynamics CRM*  | OData 摘要*  | SharePoint*  |
| Excel 活頁簿  | ODBC 查詢  | SQL Database  |
| Exchange*  | OLE DB  | Sybase 資料庫  |

\* 僅限表格式 1400 模型。 

> [!IMPORTANT]
> 連線到內部部署資料來源需要在您環境中的電腦上安裝[內部部署資料閘道](analysis-services-gateway.md)。

## <a name="data-providers"></a>資料提供者

Azure Analysis Services 中的資料模型連線至某些資料來源時，可能需要不同的資料提供者。 在某些情況下，使用原生提供者 (例如 SQL Server Native Client (SQLNCLI11)) 連接到資料來源的表格式模型可能會傳回錯誤。

針對與雲端資料來源 (例如 Azure SQL Database) 連線的資料模型，如果您使用 SQLOLEDB 以外的原生提供者，可能會看見以下的錯誤訊息：**“The provider 'SQLNCLI11.1' is not registered”** (尚未註冊提供者 'SQLNCLI11.1')。 或者，當您有 DirectQuery 模型與內部部署資料來源連線時，如果您使用原生提供者，可能會看見錯誤訊息：**“Error creating OLE DB row set.Incorrect syntax near 'LIMIT'”** (建立 OLE DB 列集時發生錯誤。'LIMIT' 附近的語法錯誤)。

與雲端或內部部署中的資料來源連線時，In-Memory 或 DirectQuery 資料模型支援下列資料來源提供者：

### <a name="cloud"></a>雲端
| **資料來源** | **In-memory** | **DirectQuery** |
|  --- | --- | --- |
| Azure SQL 資料倉儲 |.NET Framework Data Provider for SQL Server |.NET Framework Data Provider for SQL Server |
| Azure SQL Database |.NET Framework Data Provider for SQL Server |.NET Framework Data Provider for SQL Server | |

### <a name="on-premises-via-gateway"></a>內部部署 (透過閘道)
|**資料來源** | **In-memory** | **DirectQuery** |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |.NET Framework Data Provider for SQL Server |
| SQL Server |Microsoft OLE DB Provider for SQL Server |.NET Framework Data Provider for SQL Server | |
| SQL Server |.NET Framework Data Provider for SQL Server |.NET Framework Data Provider for SQL Server | |
| Oracle |Microsoft OLE DB Provider for Oracle |Oracle Data Provider for .NET | |
| Oracle |Oracle Data Provider for .NET |Oracle Data Provider for .NET | |
| Teradata |Teradata 的 OLE DB 提供者 |Teradata Data Provider for .NET | |
| Teradata |Teradata Data Provider for .NET |Teradata Data Provider for .NET | |
| 分析平台系統 |.NET Framework Data Provider for SQL Server |.NET Framework Data Provider for SQL Server | |

> [!NOTE]
> 使用內部部署閘道時，請確定已安裝 64 位元提供者。
> 
> 

將內部部署 SQL Server Analysis Services 表格式模型移轉至 Azure Analysis Services 時，可能需要變更提供者。

**提供資料來源提供者**

1. 在 SSDT > **Tabular Model Explorer** (表格式模型總管)  >  [資料來源] 中，以滑鼠右鍵按一下資料來源連線，然後按一下 [編輯資料來源]。
2. 在 [編輯連線] 中按一下 [進階]，以開啟 [進階屬性] 視窗。
3. 在[設定進階屬性]  >  [提供者] 中，然後選取適當的提供者。

## <a name="impersonation"></a>模擬
在某些情況下，可能需要指定不同的模擬帳戶。 模擬帳戶可以在 SSDT 或 SSMS 中指定。

對於內部部署資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。
* 如果使用 Windows 驗證，請設定 Windows 使用者/密碼。 對於 SQL Server，In-Memory 資料模型僅支援具有特定模擬帳戶的 Windows 驗證。

對於雲端資料來源：

* 如果使用 SQL 驗證，模擬應為服務帳戶。

## <a name="next-steps"></a>後續步驟
如果您有內部部署資料來源，請務必安裝 [內部部署閘道](analysis-services-gateway.md)。   
若要深入了解在 SSDT 或 SSMS 中管理您伺服器的相關資訊，請參閱[管理您的伺服器](analysis-services-manage.md)。

