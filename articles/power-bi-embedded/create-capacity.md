---
title: "在 Azure 入口網站中建立 Power BI Embedded 容量 | Microsoft Docs"
description: "本文將逐步解說如何在 Microsoft Azure 中建立 Power BI Embedded 容量。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 1902e5c18cd7083ceeda79e6b9e779e4baaf175a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>在 Azure 入口網站中建立 Power BI Embedded 容量

本文將逐步解說如何在 Microsoft Azure 中建立 Power BI Embedded 容量。 Power BI Embedded 可協助您在應用程式中快速新增令人讚嘆的視覺效果、報表和儀表板，藉以簡化 Power BI 功能。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

> [!VIDEO https://www.youtube.com/embed/aXrvFfg_iSk]

## <a name="before-you-begin"></a>開始之前

若要完成本快速入門，您需要：

* **Azure 訂用帳戶**︰瀏覽 [Azure 免費試用](https://azure.microsoft.com/free/)來建立帳戶。
* **Azure Active Directory**：您的訂用帳戶必須與 Azure Active Directory (AAD) 租用戶相關聯。 而且，***您必須使用該租用戶中的帳戶來登入 Azure***。 不支援 Microsoft 帳戶。 若要深入了解，請參閱＜驗證和使用者權限＞。
* **Power BI 租用戶**：您的 AAD 租用戶中至少有一個帳戶必須已註冊 Power BI。
* **資源群組**：使用現有資源群組，或[建立新的群組](../azure-resource-manager/resource-group-overview.md)。

## <a name="create-a-capacity"></a>建立容量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [+ (新增)] > [資料 + 分析]。

3. 在搜尋方塊中，搜尋 *Power BI Embedded*。

4. 在 Power BI Embedded 中，選取 [建立]。

5. 填入必要的資訊，然後選取 [建立]。

    ![要填寫以建立新容量的欄位](media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |設定 |說明 |
    |---------|---------|
    |**資源名稱**|用於識別容量的名稱。 除了 Azure 入口網站，資源名稱還會顯示在 Power BI 管理入口網站中。|
    |**訂用帳戶**|您想要建立容量的訂用帳戶。|
    |**資源群組**|包含這個新容量的資源群組。 從現有的資源群組中挑選或另建一個。 如需詳細資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。|
    |**Power BI 容量管理員**|Power BI 容量管理員可以檢視 Power BI 管理入口網站中的容量，並為其他人授與指派權限。 根據預設，容量管理員就是您的帳戶。 容量管理員必須在您的 Power BI 租用戶中。|
    |**位置**|針對您的租用戶裝載 Power BI 的位置。 系統會自動解析此設定，無法選取不同的位置。|
    |**定價層**|選取符合您需求的 SKU (V 核心計數和記憶體大小)。  如需詳細資料，請參閱 [Power BI Embedded 定價](https://azure.microsoft.com/pricing/details/power-bi-embedded/)。|

6. 選取 [ **建立**]。

建立通常不到一分鐘即可完成；往往在數秒內。 如果您選取 [釘選到儀表板]，請瀏覽至您的儀表板以查看新容量。 或者，瀏覽至 [更多服務] > [Power BI Embedded] 以查看您的容量是否已就緒。

![含 Power BI Embedded 容量的 Azure 入口網站儀表板](media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>後續步驟

若要使用新的 Power BI Embedded 容量，請瀏覽至 Power BI 管理入口網站來指派工作區。 如需詳細資訊，請參閱[管理 Power BI Premium 和 Power BI Embedded 內的容量](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/)。

如果您不需使用這個容量，請暫停它以便停止計費。 如需詳細資訊，請參閱[在 Azure 入口網站中暫停和啟動您的 Power BI Embedded 容量](pause-start.md)。

若要在您的應用程式內開始內嵌 Power BI 內容，請參閱[如何內嵌 Power BI 儀表板、報表和磚](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)。

有其他疑問？ [嘗試在 Power BI 社群中提問](http://community.powerbi.com/) \(英文\)