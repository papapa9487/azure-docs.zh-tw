---
title: "在應用程式服務中相應放大背景工作角色 - Azure Stack | Microsoft Docs"
description: "調整 Azure Stack 應用程式服務的詳細指導方針"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: 30ab325488684a26a6ef442e7c8241526a66aa4c
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2017
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>App Service on Azure Stack：新增更多基礎結構或背景工作角色

此文件提供如何調整 App Service on Azure Stack 基礎結構和背景工作角色的相關指示。 其中包含建立額外的背景工作角色以支援任何規模之應用程式的步驟。

> [!NOTE]
> 如果您 Azure Stack 環境的 RAM 未超過 96 GB，您可能很難增加額外的容量。

根據預設值，Azure Stack 上的 App Service 支援免費和共用背景工作角色層。 若要新增其他背景工作角色層，您需要新增更多背景工作角色。

如果您不確定已透過 Azure Stack 安裝上的預設 App Service 部署了哪個背景工作層，您可以在 [Azure Stack 上的 App Service 概觀](azure-stack-app-service-overview.md)中檢閱其他資訊。

Azure App Service on Azure Stack 會使用虛擬機器擴展集部署所有角色，實質上利用此工作負載的縮放功能。 因此，所有背景工作層的縮放是透過 App Service 管理員完成。

直接在 App Service 資源提供者系統管理內新增其他背景工作角色。

1. 以服務管理員身分登入 Azure Stack 管理入口網站。

2. 瀏覽至 [App Service]。

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)
  
3. 按一下 [角色]。 您可以在這裡看到所有已部署之 App Service 角色的明細。

4. 以滑鼠右鍵按一下您想要調整的類型列，然後按一下 [ScaleSet]。

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)
  
5. 按一下 [調整大小]，選取您想要調整為多少執行個體數，然後按一下 [儲存]。
    
    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Azure App Service on Azure Stack 現在將新增額外的 VM、設定它們、安裝所有必要的軟體，並在完成此程序時將它們標示為就緒。 此程序大約需要 80 分鐘。

7. 您可以檢視 [角色] 刀鋒視窗中的背景工作，來監視整備新角色的進度。

在將它們完整部署並且準備好後，背景工作會變成可供使用者用來將工作負載部署到當中。 以下顯示的是預設可用的多個定價層範例。 如果特定的背景工作層沒有可用的背景工作，就無法使用選擇對應定價層的選項。

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> 若要相應放大管理、前端或發行者角色新增，您必須相應放大對應的 VM 擴展集。 我們將在未來版本中新增透過 App Service 管理將這些角色相應放大的功能。

若要相應放大管理、前端或發行者角色，請遵循相同的步驟來選取適當的角色類型。 控制項不會部署為擴展集，因此這兩者應在所有生產環境部署的安裝期間進行部署。

### <a name="next-steps"></a>後續步驟

[設定部署來源](azure-stack-app-service-configure-deployment-sources.md)
