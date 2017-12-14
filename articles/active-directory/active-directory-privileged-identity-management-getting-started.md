---
title: "開始使用 Azure AD Privileged Identity Management | Microsoft Docs"
description: "了解如何在 Azure 入口網站中使用 Azure Active Directory Privileged Identity Management 應用程式來管理特殊權限身分識別。"
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 11b5d1bc4e931ad902b623fe55e62097ae40d8ef
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>開始使用 Azure AD Privileged Identity Management

您可以利用 Azure Active Directory (AD) Privileged Identity Management 來管理、控制和監視組織內的存取行為。 此範圍包括存取 Azure 資源、Azure AD 和其他 Microsoft 線上服務 (例如 Office 365 或 Microsoft Intune)。

本文會告訴您如何將 Azure AD PIM 應用程式加入 Azure 入口網站儀表板中。

## <a name="add-the-privileged-identity-management-application"></a>加入 Privileged Identity Management 應用程式

使用 Azure AD Privileged Identity Management 之前，您需要先將應用程式加入 Azure 入口網站儀表板中。

1. 以目錄的全域系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/) 。
2. 如果貴組織有多個目錄，請選取 Azure 入口網站右上角的使用者名稱。 選取您要在其中使用 PIM 的目錄。
3. 選取 [更多服務] 並使用 [篩選器] 文字方塊來搜尋 [Azure AD Privileged Identity Management]。
4. 選取 [釘選到儀表板]，然後按一下 [建立]。 Privileged Identity Management 應用程式隨即開啟。

如果您是目錄中使用 Azure AD Privileged Identity Management 的第一人，則會自動獲指派目錄中的「安全性系統管理員」和「特殊權限角色管理員」角色。 只有特殊權限角色管理員才能管理使用者的 Azure AD Directory 角色指派。 此外，您還可以選擇執行[安全性精靈](active-directory-privileged-identity-management-security-wizard.md) 引導您完成初始探索和指派體驗。

## <a name="navigate-to-your-tasks"></a>瀏覽至您的工作

Azure AD Privileged Identity Management 設定完畢後，您會在每次開啟應用程式時看到導覽刀鋒視窗。 使用此刀鋒視窗來完成您的身分識別管理工作。

![PIM 的最上層工作 - 螢幕擷取畫面](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

- [我的角色] 會顯示一個清單，列出已指派給您的合格和使用中角色。 您可以在這裡啟動任何指派的合格角色。
- [核准要求 (預覽)] 會顯示一個清單，列出您目錄中的使用者為了啟動合格 Azure AD Directory 角色所提出的要求，而此目錄是指定給您以進行核准的目錄。 [深入了解。](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
- [擱置要求 (預覽)] 會顯示您啟動合格角色指派的任何擱置要求。
- [檢閱存取權] 會列出指派給您完成的使用中存取權檢閱 (無論您是在檢閱自己還是他人的存取權)。
- 左瀏覽功能表的 [管理] 區段下的 [Azure AD Directory 角色] 會顯示儀表板，可讓特殊權限角色管理員用來管理角色指派、變更角色啟用設定、開始存取權檢閱等。 對於任何不是特殊權限角色管理員的人員，系統會停用此儀表板。 這些使用者可以存取標題為 [我的檢視] 的特殊儀表板。 [我的檢視] 儀表板只會顯示存取儀表板的使用者，而非整個租用戶的相關資訊。
- 左瀏覽功能表的 [管理] 區段下的 [Azure Resource 角色] 會顯示一個清單，列出您可以為其選擇角色指派的訂用帳戶資源 

## <a name="next-steps"></a>後續步驟
[Azure AD Privileged Identity Management 概觀](active-directory-privileged-identity-management-configure.md) 包含如何管理貴組織系統管理存取的更多詳細資訊。

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
