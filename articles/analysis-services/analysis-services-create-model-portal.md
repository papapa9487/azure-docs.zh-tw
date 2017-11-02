---
title: "使用 Azure Analysis Services Web 設計工具來建立表格式模型 | Microsoft Docs"
description: "了解如何在 Azure 入口網站中使用 Web 設計工具，建立 Azure Analysis Services 表格式模型。"
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
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 0a70ce4a106b8d9103080f050ab2317cd69348c1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2017
---
# <a name="create-a-model-in-azure-portal"></a>在 Azure 入口網站中建立模型

Azure 入口網站中的 Azure Analysis Services Web 設計工具 (預覽) 提供快速又簡單的方法，直接在瀏覽器中建立及編輯表格式模型和查詢模型資料。 

請記住，Web 設計工具為**預覽**狀態。 雖然在預覽中隨時會新增新功能，但是功能受到限制。 對於進階模型開發和測試，最好使用 Visual Studio (SSDT) 和 SQL Server Management Studio (SSMS)。

## <a name="prerequisites"></a>必要條件

- 在標準或開發人員層的 Azure Analysis Services 伺服器。 使用 Web 設計工具所建立的新模型是 DirectQuery，只受到這些層級的支援。
- 作為資料來源的 Azure SQL Database、Azure SQL 資料倉儲或 Power BI Desktop (.pbix) 檔案。 從 Power BI Desktop 檔案建立的新模型支援 Azure SQL Database、Azure SQL 資料倉儲、Oracle 和 Teradata 資料來源。
- 用來連線至 Azure SQL Database 或 Azure SQL 資料倉儲資料來源的 SQL Server 帳戶和密碼。

## <a name="to-create-a-new-tabular-model"></a>若要建立新的表格式模型

1. 在您的伺服器 [概觀] 刀鋒視窗 > [Web 設計工具] 中，按一下 [開啟]。

    ![在 Azure 入口網站中建立模型](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. 在 [Web 設計工具]  >  [模型] 中，按一下 [+ 新增]。

    ![在 Azure 入口網站中建立模型](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. 在 [新增模型] 中輸入模型名稱，然後選取資料來源。

    ![在 Azure 入口網站中的新增模型對話方塊](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. 在 [連線] 中，輸入連線屬性。 使用者名稱和密碼必須是 SQL Server 帳戶。

     ![在 Azure 入口網站中的連線對話方塊](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. 在 [資料表和檢視] 中，選取要包含在模型中的資料表，然後按一下 [建立]。 會使用金鑰組自動建立資料表之間的關聯性。

     ![選取資料表和檢視](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

新的模型會出現在您的瀏覽器。 您可以在這裡執行下列動作：   

- 將欄位拖曳到查詢設計工具並且新增篩選，來查詢模型資料。
- 在資料表中建立新的量值。
- 使用 json 編輯器來編輯模型中繼資料。
- 在 Visual Studio (SSDT)、Power BI Desktop 或 Excel 中開啟模型。

![選取資料表和檢視](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> 當您在瀏覽器中編輯模型中繼資料或建立新的量值時，您會將對於模型所做的變更儲存至 Azure。 如果您正在 SSDT、Power BI Desktop 或 Excel 中使用模型，您的模型可以不同步。


## <a name="next-steps"></a>後續步驟 
[管理資料庫角色和使用者](analysis-services-database-users.md)  
[與 Excel 連線](analysis-services-connect-excel.md)  


