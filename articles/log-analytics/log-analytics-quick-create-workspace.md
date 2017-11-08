---
title: "在 Azure Log Analytics 中建立工作區 | Microsoft Docs"
description: "了解如何建立 Log Analytics 工作區，以從您的雲端和內部部署環境啟用管理解決方案和資料收集。"
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: magoedte
ms.openlocfilehash: 8259a97d28effa7bfa9cfb9d7cd9cd2a14c9d906
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>在 Azure 入口網站中建立 Log Analytics 工作區
您可以在 Azure 入口網站中設定 Log Analytics 工作區，這是一個唯一的 Log Analytics 環境，有其自己的資料存放庫、資料來源和解決方案。  如果您想要從下列來源收集資料，則需要本文中所述的步驟：

* 訂用帳戶中的 Azure 資源
* System Center Operations Manager 監視的內部部署電腦
* 來自 System Center Configuration Manager 的裝置集合 
* Azure 儲存體的診斷或記錄資料

針對其他來源，例如環境中的 Azure VM 和 Windows 或 Linux 電腦，請參閱下列主題：

*  [收集關於 Azure 虛擬機器的資料](log-analytics-quick-collect-azurevm.md) 
*  [收集關於 Linux 電腦的資料](log-analytics-quick-collect-linux-computer.md)
*  [收集關於 Windows 電腦的資料](log-analytics-quick-collect-windows-computer.md)

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure-portal"></a>登入 Azure 入口網站
登入 Azure 入口網站，網址為 [https://portal.azure.com](https://portal.azure.com)。 

## <a name="create-a-workspace"></a>建立工作區
1. 在 Azure 入口網站中，按一下左下角的 [更多服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics]。<br><br> ![Azure 入口網站](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. 按一下 [建立]，然後選取下列項目的選項：

  * 為新的 [OMS 工作區] 提供名稱，例如，*DefaultLAWorkspace*。 
  * 如果選取的預設值不合適，請從下拉式清單中選取要連結的 [訂用帳戶]。
  * 針對**資源群組**，選擇使用已設定的現有資源群組，或建立新的群組。  
  * 選取可用的**位置**。  如需詳細資訊，請查看 [Log Analytics 的可用區域](https://azure.microsoft.com/regions/services/)。
  * 您可以在 Log Analytics 中選擇三種不同的 [定價層]，但對於本快速入門，您要選取 [免費] 層。  如需特定層的詳細資訊，請參閱 [Log Analytics 價格詳細資料](https://azure.microsoft.com/pricing/details/log-analytics/)。

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. 在 [OMS 工作區] 窗格上提供必要資訊之後，按一下 [確定]。  

在驗證資訊及建立工作區時，您可以在功能表的 [通知] 底下追蹤其進度。 

## <a name="next-steps"></a>後續步驟
有了可用的工作區之後，您可以設定監視遙測的集合、執行記錄搜尋以分析該資料，並且新增管理解決方案，以提供額外的資料和分析深入解析。 

* 若要從具有 Azure 診斷或 Azure 儲存體的 Azure 資源啟用資料收集，請參閱[收集 Azure 服務的記錄和計量以便使用於 Log Analytics](log-analytics-azure-storage.md)。  
* [新增 System Center Operations Manager 作為資料來源](log-analytics-om-agents.md)，以從會報告 Operations Manager 管理群組的代理程式收集資料，並且將其儲存在 Log Analytics 工作區存放庫。 
* 連線 [Configuration Manager](log-analytics-sccm.md) 以匯入階層中集合成員的電腦。  
* 檢閱可用的[管理解決方案](/log-analytics-add-solutions.md)，以及如何從您的工作區新增或移除解決方案。

