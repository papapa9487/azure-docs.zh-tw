---
title: "Microsoft Azure Data Lake Analytics 概觀 | Microsoft Docs"
description: "Data Lake Analytics 是 Azure 巨量資料服務，可讓您使用從雲端中的資料獲得的深入資訊 (不論其所在位置和大小)，使用資料來推動業務。"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: 8817b511d779029421491194b50120d51ec9dbad
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Microsoft Azure Data Lake Analytics 概觀
## <a name="what-is-azure-data-lake-analytics"></a>什麼是 Azure Data Lake Analytics？
Azure Data Lake Analytics 是隨選分析作業服務，可簡化巨量資料分析。 您可以著重於撰寫、執行和管理作業，而非操作分散式基礎結構。 無需部署、設定及調整硬體，您只需寫入查詢便可轉換資料並擷取寶貴的深入見解。 透過針對所需的功能設定級別，此項分析服務便可立即處理任何規模的工作。 只有在作業進行時您才需要支付費用，十分符合成本效益。 分析服務支援 Azure Active Directory，讓您管理存取權及角色，且其與您的內部部署身分識別系統整合。 分析服務支援還包括 U-SQL，該語言可將 SQL 的優勢與使用者程式碼的運算能力結合在一起。 U-SQL 的可調整分散式執行階段可讓您有效地分析資料，範圍遍及存放區以及 Azure、Azure SQL Database 以及 Azure SQL 資料倉儲中的 SQL Server。

## <a name="key-capabilities"></a>主要功能
* **動態調整**
  
    Data Lake Analytics 專為雲端規模與效能所打造。  它會動態部署資源，讓您可以分析數 TB 乃至於數 EB 的資料。 當工作完成時，其會自動逐步釋放這些資源，因此您只需要支付所用處理功能的費用。 當您所儲存的資料量或所用的計算資源量有所增減時，也無需重新編寫程式碼。 如此您便可將心力全數投注在商業邏輯，而無需分神在處理與儲存大型資料集的方式上。
* **利用熟悉的工具縮短開發時間，並讓偵錯及最佳化工作更加敏捷**
  
    Data Lake Analytics 與 Visual Studio 的深度整合，讓您可以使用熟悉的工具來執行、偵錯及調整您的程式碼。 視覺化您的 U-SQL 工作可讓您了解程式碼在大規模執行時的狀況，從而輕鬆找出效能瓶頸與並達到最佳成本效益。
* **U-SQL：簡單、熟悉、強大且易於擴充**
  
    Data Lake Analytics 包括 U-SQL，此查詢語言透過 C# 的運算能力，將大家所熟悉 SQL 的簡單、宣告的性質加以擴充。 U-SQL 語言是建立在提供 Microsoft 巨量資料系統資源的相同分散式執行階段之上。 數百萬名 SQL 和 .NET 開發人員現在已可以利用他們本來具備的技術處理及分析資料。
* **與您的 IT 投資無縫整合**
  
    Data Lake Analytics 會運用您現有的 IT 投資，進行身分識別、管理、安全性及資料倉儲等工作。 此方法可以簡化資料管理，以及輕鬆擴充目前的資料應用程式。 Data Lake Analytics 與 Active Directory 整合，以進行使用者管理與授予使用者權限，並隨附內建的監視與稽核功能。
* **經濟實惠並符合成本效益**
  
    Data Lake Analytics 可用於執行巨量資料工作負載，是十分符合成本效益的解決方案。 所處理的資料將會以每件工作來計費。 您無需簽訂任何硬體、授權或服務相關的支援合約。 系統會自動在作業開始及完成時相應增加或減少，讓您的每一分錢都花在刀口上。
* **使用所有的 Azure 資料**
  
    Data Lake Analytics 已特別最佳化以使用 Azure Data Lake - 提供最高層級的效能、輸送量和巨量資料工作負載的平行處理。  Data Lake Analytics 也可以使用 Azure Blob 儲存體和 Azure SQL Database。

## <a name="next-steps"></a>後續步驟
 
  * 透過 [Azure 入口網站](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli2.md)開始使用 Data Lake Analytics
  * 透過 [Azure 入口網站](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md) 來開始使用 Data Lake Analytics
  * [使用 Azure 入口網站監視和疑難排解 Azure Data Lake Analytics 作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md) 
