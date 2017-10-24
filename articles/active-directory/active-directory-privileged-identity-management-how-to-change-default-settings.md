---
title: "如何管理角色啟用設定 | Microsoft Docs"
description: "了解如何利用 Azure AD Privileged Identity Management 擴充功能，變更特殊權限身分識別的預設設定。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: f6cbcb6a-8a89-4077-afd8-06c94a64f4aa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 23605e89cd1846d2e06e48cb5d3e0191cb9e9b4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>如何管理 Azure AD Privileged Identity Management 的角色啟用設定
特殊權限角色管理員可以自訂其組織中的 Azure AD Privileged Identity Management (PIM)，包括變更啟用合格角色指派之使用者的體驗。

## <a name="manage-the-role-activation-settings"></a>管理角色啟用設定
1. 移至 [Azure 入口網站](https://portal.azure.com)，然後從儀表板中選取 [Azure AD Privileged Identity Management] 應用程式。
2. 選取 [管理特殊權限角色] > [設定] > [特殊權限角色]。
3. 選擇您想要管理其設定的角色。

在每個角色的設定頁面上，有一些您可以設定的設定。 這些設定只會影響身為合格系統管理員 (而不是永久系統管理員) 的使用者。

**啟用**︰角色在到期前維持作用中狀態的時間 (以小時為單位)。 此時間可介於 1 到 72 小時。

**通知**︰您可以選擇是否要讓系統傳送電子郵件給系統管理員來確認他們已啟用角色。 這對偵測未經授權或不合法的啟用而言相當有用。

**事件/要求票證**︰您可以選擇是否要要求合格系統管理員在啟用其角色時包含票證號碼。 當您執行角色存取稽核時，這會相當有用。

**Multi-Factor Authentication**：您可以選擇是否要要求使用者在啟用其角色之前，先以 MFA 驗證其身分識別。 他們只需在每一工作階段進行一次驗證，而不需在每次啟用角色時都進行驗證。 啟用 MFA 時，需要記住兩個秘訣：

* 使用 Microsoft 帳戶作為電子郵件地址 (通常是 @outlook.com，但不一定) 的使用者無法註冊 Azure MFA。 如果您想要將角色指派給使用 Microsoft 帳戶的使用者，您應該將他們設為永久系統管理員，或是停用該角色的 MFA。
* 您無法將 Azure AD 和 Office365 中高特殊權限角色的 MFA 停用。 這是一項安全功能，因為這些角色應該嚴密地受到保護：  
  
  * 應用程式管理員
  * 應用程式 Proxy 伺服器管理員
  * 計費管理員  
  * 規範管理員  
  * CRM 服務管理員
  * 客戶 LockBox 存取核准者
  * 目錄寫入器  
  * Exchange 系統管理員  
  * 全域管理員
  * Intune 服務管理員
  * 信箱管理員  
  * 合作夥伴第 1 層支援  
  * 合作夥伴第 2 層支援  
  * 特殊權限角色管理員   
  * 安全性系統管理員  
  * SharePoint 管理員  
  * 商務用 Skype 的管理員  
  * 使用者帳戶管理員  

如需關於搭配 PIM 使用 MFA 的詳細資訊，請參閱 [如何要求 MFA](active-directory-privileged-identity-management-how-to-require-mfa.md)。

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

