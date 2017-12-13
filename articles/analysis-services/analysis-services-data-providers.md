---
title: "連接到 Azure Analysis Services 所需的用戶端程式庫 | Microsoft Docs"
description: "說明用戶端應用程式和工具連接到 Azure Analysis Services 所需的用戶端程式庫"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/04/2017
ms.author: owend
ms.openlocfilehash: 9cf5bcc9f7e089bd919c1a46934b64383b88c0ab
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>可供連接到 Azure Analysis Services 的用戶端程式庫

用戶端程式庫是用戶端應用程式和工具連接到 Analysis Services 伺服器的必備條件。 

## <a name="download-the-latest-client-libraries"></a>下載最新的用戶端程式庫  

|下載  |版本  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    14.0.1000.397      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    14.0.1000.397      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   14.0.800.218      |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    14.0.1000.397      |

## <a name="understanding-client-libraries"></a>了解用戶端程式庫

Analysis Services 會利用三個用戶端程式庫，也稱為資料提供者。 ADOMD.NET 和「Analysis Services 管理物件」(AMO) 是受控用戶端程式庫。 Analysis Services OLE DB Provider (MSOLAP DLL) 是原生用戶端程式庫。 通常會同時安裝這所有三個程式庫。 **Azure Analysis Services 需要這三個程式庫的最新版本**。 

Microsoft 用戶端應用程式 (像是 Power BI Desktop 和 Excel) 會安裝這三個用戶端程式庫，並在新版本可用時加以更新。 部分用戶端程式庫可能不是 Azure Analysis Services 所需的最新版本，需依更新的版本或頻率而定。 同樣適用於自訂應用程式或其他介面，例如 AsCmd、TOM、ADOMD.NET。 這些應用程式需要手動或以程式設計方式安裝程式庫。 手動安裝的用戶端程式庫會以可散發的套件形式包含在 SQL Server 功能套件中。 不過，這些會用戶端程式庫與 SQL Server 版本繫結，而可能不是最新版本。  

用於用戶端連線的用戶端程式庫，與從 Azure Analysis Services 伺服器連接到資料來源所需的資料提供者不同。 若要深入了解資料來源連接，請參閱[資料來源連接](analysis-services-datasource.md)。

## <a name="client-library-types"></a>用戶端程式庫類型

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB Provider (MSOLAP) 

 Analysis Services OLE DB Provider (MSOLAP) 是 Analysis Services 資料庫連線的原生用戶端程式庫。 ADOMD.NET 和 AMO 會間接使用，將連線要求委派給資料提供者。 您也可以直接從應用程式程式碼呼叫 OLE DB Provider。  
  
 大部分用來存取 Analysis Services 資料庫的工具和用戶端應用程式會自動安裝 Analysis Services OLE DB Provider。 它必須安裝在用來存取 Analysis Services 資料的電腦上。  
  
 通常會在連接字串中指定 OLE DB Provider。 Analysis Services 連接字串會使用不同的命名法來表示 OLE DB Provider：MSOLAP.\<version>.dll。

### <a name="amo"></a>AMO  

 AMO 是用於伺服器管理及資料定義的受控用戶端程式庫。 工具和用戶端應用程式會加以安裝並使用。 例如，SQL Server Management Studio (SSMS) 會使用 AMO 來連線到 Analysis Services。  
  
 使用 AMO 的連線通常是最簡短的，由 `“data source=\<servername>”` 所組成。 建立連線之後，您可以使用 API 來處理資料庫集合與主要物件。 SSDT 和 SSMS 都會使用 AMO 來連線到 Analysis Services 執行個體。  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET 是用於查詢 Analysis Services 資料的受控資料用戶端程式庫。 工具和用戶端應用程式會加以安裝並使用。 
  
 連線到資料庫時，所有三個程式庫的連接字串屬性都會類似。 幾乎任何使用 [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) 針對 ADOMD.NET 定義的連接字串也都適用於 AMO 和 Analysis Services OLE DB Provider (MSOLAP)。 若要進一步了解，請參閱[連接字串屬性與 &#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services)。  

  
##  <a name="bkmk_LibUpdate"></a> 如何判斷用戶端程式庫版本   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  移至 `C:\Program Files\Microsoft Analysis Services\AS OLEDB\140`。 如果您有多個資料夾，請選擇較高的數字。
  
2.  以滑鼠右鍵按一下 [msolap140.dll] > [屬性] > [詳細資料]。  
    
    ![用戶端程式庫詳細資料](media/analysis-services-data-providers/aas-msolap-details.png)
  
### <a name="amo"></a>AMO

1. 移至 `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\v4.0_14.0.0.0__89845dcd8080cc91`。
2. 以滑鼠右鍵按一下 [Microsoft.AnalysisServices] > [屬性] > [詳細資料]。  

### <a name="adomd"></a>ADOMD

1. 移至 `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\v4.0_14.0.0.0__89845dcd8080cc91`。
2. 以滑鼠右鍵按一下 [Microsoft.AnalysisServices.AdomdClient] > [屬性] > [詳細資料]。  


## <a name="next-steps"></a>後續步驟
[使用 Excel 進行連接](analysis-services-connect-excel.md)    
[與 Power BI 連線](analysis-services-connect-pbi.md)
