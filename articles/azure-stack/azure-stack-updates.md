---
title: "管理 Azure Stack 中的更新概觀 | Microsoft Docs"
description: "深入了解 Azure Stack 整合系統的更新管理。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: f5c7810a88bee65cf22276082f72b18a7f9e983f
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="manage-updates-in-azure-stack-overview"></a>在 Azure Stack 中管理更新概觀

適用於：Azure Stack 整合系統

Microsoft 會以一般的步調釋出 Azure Stack 整合系統的更新套件，通常會落在每個月的第四個星期二，從正式上市開始。 請洽詢您的 OEM 了解其特定通知程序，以確保貴組織收到更新通知，或在 Concepts\Release notes\Integrated 系統版本資訊下在這裡查看，了解關於特定版本的詳細資訊。

每次發行的 Microsoft 軟體更新均以單一更新封裝的形式提供。 身為 Azure Stack 操作員，您可以從管理員入口網站輕鬆匯入和安裝這些更新封裝，並監視這些更新封裝的安裝進度。 

原始設備製造商 (OEM) 硬體廠商也會發行更新，例如驅動程式和韌體更新。 這些更新是由 OEM 硬體廠商以個別封裝的形式提供，並從 Microsoft Update 分別管理。

為了讓系統獲得支援，您必須將 Azure Stack 更新為特定版本等級。 務必檢閱 [Azure Stack 服務原則](azure-stack-servicing-policy.md)。

> [!NOTE]
> 您無法將 Azure Stack 更新封裝套用於 Azure Stack 開發封裝。 更新封裝專為整合系統所設計。

## <a name="the-update-resource-provider"></a>更新資源提供者

Azure Stack 包含協調 Microsoft 軟體應用程式更新的更新資源提供者。 對於有實體主機、Service Fabric 應用程式和執行階段，以及所有基礎結構虛擬機器和其相關聯服務，這個資源提供者可確保均套用更新。

在更新安裝中，更新程序以 Azure Stack 中的各種子系統 (例如，實體主機和基礎結構虛擬機器) 為目標時，您可以輕鬆檢視高階狀態。

## <a name="plan-for-updates"></a>規劃更新

強烈建議您向使用者通知任何維護工作，並且盡可能將一般的維護期間安排在非上班時間。 維護作業會影響租用戶工作負載和入口網站作業。

## <a name="using-the-update-tile-to-manage-updates"></a>[更新] 圖格可用來管理更新
從系統管理員入口網站管理更新是簡單的程序。 Azure Stack 操作員可以巡覽至儀表板的 [更新] 圖格：

- 檢視重要資訊，例如目前的版本。
- 安裝更新，並監視進度。
- 檢閱先前安裝之更新本身的更新歷程記錄。
 
## <a name="determine-the-current-version"></a>判斷目前版本

[更新] 圖格會顯示目前的 Azure Stack 版本。 您可以在系統管理員入口網站中使用下列其中一種方法移至 [更新] 圖格：

- 在儀表板上，在 [更新] 圖格檢視目前的版本。
 
   ![預設儀表板上的 [更新] 圖格](./media/azure-stack-updates/image1.png)
 
- 在 [區域管理] 圖格上，按一下區域名稱。 在 [更新] 圖格中檢視目前的版本。

## <a name="next-steps"></a>後續步驟

- [Azure Stack 服務原則](azure-stack-servicing-policy.md) 
- [Azure Stack 中的區域管理](azure-stack-region-management.md)     


