---
title: "使用 Power BI 來連接到 Azure Analysis Services | Microsoft Docs"
description: "了解如何使用 Power BI 來連接到 Azure Analysis Services 伺服器。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: 3a2af043feddb4a1d6d63f50e838c8a39035449f
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="connect-with-power-bi"></a>使用 Power BI 進行連接

您在 Azure 中建立一個伺服器，並將表格式模型部署至該伺服器後，您組織中的使用者便可以連線與開始瀏覽資料。 

> [!TIP]
> 請務必使用最新版的 [Power BI Desktop](https://powerbi.microsoft.com/desktop/)。
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>在 Power BI Desktop 中連線

1. 在 Power BI Desktop 中，按一下 [取得資料] > [Azure] > [Azure Analysis Services 資料庫]。

2. 在 [伺服器] 中，輸入伺服器名稱。 
    
    請務必包含完整的 URL。 例如 asazure://westcentralus.asazure.windows.net/advworks。

3. 在 [資料庫] 中，如果您知道所要連線表格式模型資料庫或檢視方塊的名稱，請在此貼上。 否則，您可以將此欄位保留空白，然後稍後選取資料庫或檢視方塊。

4. 保留預設的 [即時連接] 選項，然後按 [連接]。 

5. 如果出現提示，請輸入您的登入認證。 

6. 在 [導覽器] 中展開伺服器，然後選取您要連線的模型或檢視方塊，再按一下 [連線]。 按一下某個模型或檢視方塊，即可顯示該檢視的所有物件。

    模型會在 Power BI Desktop 中開啟，其中會在 [報表] 檢視下顯示一個空白報表。 [欄位] 清單會顯示所有非隱藏的模型物件。 連線狀態會顯示在右下角。

## <a name="connect-in-power-bi-service"></a>在 Power BI Desktop 中連線 (服務)

1. 建立與您伺服器上模型即時連線的 Power BI Desktop 檔案。
2. 在 [Power BI](https://powerbi.microsoft.com) 中，按一下 [取得資料]  >  [檔案]。 找到並選取您的檔案。



## <a name="see-also"></a>另請參閱
[連接到 Azure Analysis Services](analysis-services-connect.md)   
[用戶端程式庫](analysis-services-data-providers.md)

