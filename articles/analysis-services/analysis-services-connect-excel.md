---
title: "使用 Excel 來連接到 Azure Analysis Services | Microsoft Docs"
description: "了解如何使用 Excel 來連接到 Azure Analysis Services 伺服器。"
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
ms.openlocfilehash: d51b6980120f1cf9bc8d053d463a73ac600b915f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-with-excel"></a>使用 Excel 進行連接

您在 Azure 中建立伺服器並將表格式模型部署至該伺服器之後，即可連線及開始瀏覽資料。


## <a name="connect-in-excel"></a>在 Excel 中連線

藉由使用 Excel 2016 中的 [取得資料]，即可支援在 Excel 中連線到伺服器。 不支援在 Power Pivot 中使用 [匯入資料表精靈] 連線。 

**在 Excel 2016 中進行連接**

1. 在 Excel 2016 中的 [資料] 功能區上，按一下 [取得外部資料] >  **[從其他來源]** > [從 Analysis Services]。

2. 在 [資料連線精靈] 的 [伺服器名稱] 中，輸入包含通訊協定和 URI 的伺服器名稱。 然後在 [登入認證] 中，選取 [使用下列的使用者名稱和密碼]，接著輸入組織使用者名稱 (例如 nancy@adventureworks.com) 和密碼。

    ![從 Excel 登入來進行連接](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. 在 選取資料庫和資料表 中，選取資料庫和模型或檢視方塊，然後按一下完成。
   
    ![從 Excel 選取模型來進行連接](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>另請參閱
[用戶端程式庫](analysis-services-data-providers.md)   
[管理您的伺服器](analysis-services-manage.md)     


