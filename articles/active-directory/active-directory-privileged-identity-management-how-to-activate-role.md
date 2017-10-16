---
title: "如何啟用或停用角色 | Microsoft Docs"
description: "了解如何利用 Azure Privileged Identity Management 應用程式，啟用特殊權限身分識別的角色。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 1ce9e2e7-452b-4f66-9588-0d9cd2539e45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: a143fd78eae52fda0cbadb7e74fd8209f24629a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management：如何啟用或停用角色
Azure Active Directory (AD) Privileged Identity Management 簡化了企業管理以特殊權限身分存取 Azure AD 中的資源和其他 Microsoft 線上服務 (如 Office 365 或 Microsoft Intune) 的方式。  

如果您已被設為符合系統管理角色資格，即表示您可以在需要執行特殊權限動作時，啟用該角色。 例如，如果您偶爾會管理 Office 365 功能，則貴組織的特殊權限角色管理員可能不會讓您成為永久全域管理員，因為該角色也會影響其他服務。 他們反而會讓您符合 Azure AD 角色 (例如「Exchange Online 管理員」) 的資格。 您可以在需要權限時，要求停用該角色，然後您將會在預定的時段內擁有系統管理控制權。

本文適用對象是必須在 Azure AD Privileged Identity Management (PIM) 中啟用其角色的系統管理員。 它會逐步引導您在需要權限時啟用角色，並在完成時停用角色。 此外，特殊權限角色系統管理員可以要求核准以啟用角色 (預覽)。 在這裡深入了解 [PIM 核准工作流程](./privileged-identity-management/azure-ad-pim-approval-workflow.md)。

## <a name="add-the-privileged-identity-management-application"></a>加入 Privileged Identity Management 應用程式
請在 [Azure 入口網站](https://portal.azure.com/) 中使用 Azure AD Privileged Identity Management 應用程式來要求角色啟用，即使您是要在另一個入口網站中或透過 PowerShell 來操作，也是如此。 如果您在 Azure 入口網站沒有 Azure AD Privileged Identity Management 應用程式，請遵循下列步驟操作。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 Azure 入口網站右上角的使用者名稱，然後選取您要操作的目錄。
3. 選取 [更多服務] 並使用 [篩選器] 文字方塊來搜尋 [Azure AD Privileged Identity Management]。
4. 選取 釘選到儀表板，然後按一下建立。 Privileged Identity Management 應用程式隨即開啟。

## <a name="activate-a-role"></a>啟用角色
當您需要擔任某個角色時，您可以選取 Azure AD Privileged Identity Management 應用程式左側瀏覽資料行中的 [我的角色] 瀏覽選項來要求啟用。

1. 登入 [Azure 入口網站](https://portal.azure.com/) ，選取 [Azure AD Privileged Identity Management] 圖格。
2. 選取 [我的角色]。 指派的合格角色清單會出現在分頁頂端的群組。
3. 選取要啟用的角色。
4. 選取 [ **啟用**]。 [要求啟用角色]  刀鋒視窗隨即出現。
5. 有些角色會要求必須先執行 Multi-Factor Authentication (MFA) 才能啟用。 您只需在每個工作階段驗證一次。
   
    ![在啟用角色之前先以 MFA 驗證 - 螢幕擷取畫面][2]
6. 在啟用要求的文字欄位中輸入啟用原因。  有些角色會要求您提供問題票證號碼。
7. 選取 [確定] 。  如果角色不需要核准，它現在就會啟用，角色會出現在使用中角色的清單中 (符合角色指派資格清單正下方)。 如果[角色需要核准](./privileged-identity-management/azure-ad-pim-approval-workflow.md)才能啟用，快顯通知會短暫出現在瀏覽器右上角，通知您要求正在等待核准。

    ![要求擱置通知 - 螢幕擷取畫面][3]

## <a name="deactivate-a-role"></a>停用角色
角色一經啟用，就會在達到時間限制 (合格的持續時間) 時自動停用。

如果您提早完成管理員工作，也可以在 Azure AD Privileged Identity Management 應用程式中手動停用角色。  選取 [我的角色]，從 [使用中角色指派] 群組中選擇完成使用的角色，然後選取 [停用]。  

## <a name="cancel-a-pending-request"></a>取消擱置要求
萬一您不需要啟用需要核准的角色，您可以隨時取消擱置要求。 只要選取 Azure AD Privileged Identity Management 應用程式左側瀏覽資料行中的 [我的角色] 瀏覽選項即可。

1. 登入 [Azure 入口網站](https://portal.azure.com/) ，選取 [Azure AD Privileged Identity Management] 圖格。
2. 選取 [我的角色]。 指派的合格角色清單會出現在分頁頂端的群組。
3. 選取角色。
4. 選取角色啟用詳細資料刀鋒視窗上的 [啟用正在等待核准] 橫幅。
5. 選取 [等待核准] 刀鋒視窗頂端的 [取消]。

   ![取消擱置要求螢幕擷取畫面][4]

## <a name="next-steps"></a>後續步驟
如果您有興趣進一步了解 Azure AD Privileged Identity Management，下列連結會提供詳細資訊。

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
