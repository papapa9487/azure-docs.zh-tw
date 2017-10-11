---
title: "Azure Privileged Identity Management 核准工作流程 | Microsoft Docs"
description: "了解 Privileged Identity Management (PIM) 中的核准工作流程"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: cf6a9213fa0a1cba8725aabb42abe51b805ece7a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="approvals-preview"></a>核准 (預覽)

## <a name="overview"></a>概觀

利用 Privileged Identity Management 的核准，您可以將角色設定為需要核准才能啟用，並選擇一或多個使用者或群組做為委派核准者。 請繼續閱讀，以了解如何設定角色和選取核准者。

>[!NOTE]
請記住，這項功能仍處於開發階段，而您可能會遇到 Bug。 包括文字和命名慣例在內的功能均有可能變更，不應將其視為最終結果。


## <a name="key-terminology"></a>重要術語

*合格角色使用者*：合格角色使用者是您組織內已在符合資格時指派給 Azure AD 角色的使用者 (角色需要啟用)。

*委派核准者*：委派核准者是您 Azure AD 內的一或多個個人或群組，其負責核准啟用角色的要求。

## <a name="scenarios"></a>案例

私人預覽支援下列案例：

**身為特殊權限角色管理員 (PRA)，您可以：**

-   [啟用特定角色的核准](#enable-approval-for-specific-roles)

-   [指定核准者使用者和/或群組來核准要求](#specify-approver-users-and/or-groups-to-approve-requests)

-   [檢視所有特殊權限角色的要求和核准歷程記錄](#view-request-and-approval-history-for-all-privileged-roles)

**身為指定的核准者，您可以：**

-   [檢視待決的核准 (要求)](#view-pending-approvals-requests)

-   [核准或拒絕提高角色權限 (單一和/或大量) 的要求](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [提供我的核准/拒絕理由](#provide-justification-for-my-approval/rejection) 

**身為合格角色使用者，您可以：**

-   [要求啟用需要核准的角色](#request-activation-of-a-role-that-requires-approval)

-   [檢視要啟用之要求的狀態](#view-the-status-of-your-request-to-activate)

-   [如果已核准啟用，在 Azure AD 中完成您的工作](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>導覽

我們已更新導覽來支援核准

![](media/azure-ad-pim-approval-workflow/image001.png)

預設的登陸頁面讓您能夠方便存取有關 PIM 和新核准文件的資訊。

![](media/azure-ad-pim-approval-workflow/image002.png)

我們也針對 PIM [我的稽核歷程記錄] 的所有使用者新增了新的區段。 您可以在此處找到與您身分識別相關的所有資訊。 這包括您的所有待決和完成的要求、您對於所解決之要求所做出的任何決策，以及您過去在某一個便利位置中的所有角色啟用。

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>啟用特定角色的核准

若要啟用特定角色的核准，請先從左側導覽中選取 [目錄角色]。

![](media/azure-ad-pim-approval-workflow/image004.png)

在 [目錄角色] 左側導覽中尋找並選取 [設定]

![](media/azure-ad-pim-approval-workflow/image006.png)

選取 [特殊權限角色]：

![](media/azure-ad-pim-approval-workflow/image009.png)

在 [要求核准] 區段中選取 [啟用]：

![](media/azure-ad-pim-approval-workflow/image011.png)

啟用之後，刀鋒視窗將展開以顯示下列詳細資料：

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
如果您未指定任何核准者，PRA 就會變成預設核准者。 需要有 PRA，才能核准此角色的所有啟用要求。

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>指定核准者使用者和/或群組來核准要求

若要委派核准，按一下 [選取核准者] 的選項：

![](media/azure-ad-pim-approval-workflow/image015.png)

當 [選取核准者] 刀鋒視窗載入時，您就能使用頂端的搜尋列來搜尋特定使用者或群組，或是從預先填入的清單中加以選取，然後在完成時按一下 [選取]：

![](media/azure-ad-pim-approval-workflow/image017.png)

注意︰您可以同時選取多個使用者或群組。

您的選項將出現在所選取的核准者清單中，如下所示：

![](media/azure-ad-pim-approval-workflow/image019.png)

若要移除核准者，只需按一下其名稱旁邊的 [移除] 按鈕。

若要新增其他核准者，請重複執行此程序。

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>檢視所有特殊權限角色的要求和核准歷程記錄

若要檢視所有特殊權限角色的要求和核准歷程記錄，請從儀表板選取 [稽核歷程記錄]：

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
您可以依 [動作] 排序資料，並尋找「已核准啟用」

### <a name="view-pending-approvals-requests"></a>檢視待決的核准 (要求)

身為委派核准者，當要求正在等待您的核准時，您將會收到電子郵件通知。 若要在 PIM 入口網站中檢視這些要求，可從儀表板 (在新的導覽中) 選取左側導覽列中的 [等待核准要求] 索引標籤。

![](media/azure-ad-pim-approval-workflow/image023.png)

您將在該處看到一份等待核准的要求清單：

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>核准或拒絕提高角色權限 (單一和/或大量) 的要求

選取您想要核准或拒絕的要求，然後按一下與您的決策相對應之動作列上的按鈕：

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>提供我的核准/拒絕理由

這將開啟新的刀鋒視窗，一次核准或拒絕多個要求。 輸入您的決策理由，然後按一下底端或刀鋒視窗中的 [核准] \(或 [拒絕])：

![](media/azure-ad-pim-approval-workflow/image029.png)

當要求程序完成時，狀態符號將會反映您所做的決策 (在此範例中，決策是核准)：

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>要求啟用需要核准的角色

要求啟用需要核准的角色可從舊的 PIM 導覽或新的導覽中起始，因為啟用角色的程序仍維持不變。 只需從角色清單中選取要啟用的角色：

![](media/azure-ad-pim-approval-workflow/image033.png)

如果特殊權限角色需要 Multi-Factor Authentication，系統將提示您先完成該工作：

![](media/azure-ad-pim-approval-workflow/image035.png)

完成之後，按一下 [啟用] 並提供理由 (如有必要)：

![](media/azure-ad-pim-approval-workflow/image037.png)

要求者將會看到要求正在等待核准的通知：

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>檢視要啟用之要求的狀態

檢視要啟用之要求的狀態，必須從新的導覽中加以存取。 從左側導覽列中，選取 [我的要求] 索引標籤：

![](media/azure-ad-pim-approval-workflow/image041.png)

要求狀態預設為「待決」，但您可以切換為查看所有或拒絕的要求。

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>如果已核准啟用，在 Azure AD 中完成您的工作

要求一經核准，角色即會處於作用中狀態，而您就能繼續進行任何要求此角色的工作。

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>後續步驟

您的意見反應對我們非常寶貴。 隨時歡迎在此處與我們分享註解或意見反應！
