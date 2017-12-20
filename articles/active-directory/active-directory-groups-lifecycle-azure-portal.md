---
title: "Azure Active Directory 中的 Office 365 群組到期日 | Microsoft Docs"
description: "如何設定 Azure Active Directory 中 Office 365 群組的到期日 (預覽)"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: c2dd56bd34e5b7845298fab1f36e231113a2e28e
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="configure-expiration-for-office-365-groups-preview"></a>設定 Office 365 群組的到期日 (預覽)

您現在可以藉由設定 Office 365 群組的到期日，來管理 Office 365 群組的生命週期。 您可以只設定 Azure Active Directory (Azure AD) 中 Office 365 群組的到期日。 一旦您為群組設定到期日：
-   群組擁有者會在到期日快到時收到更新群組的通知
-   未更新的群組會遭到刪除
-   群組擁有者或系統管理員可在 30 天內還原已刪除的任何 Office 365 群組

> [!NOTE]
> 若要設定 Office 365 群組的到期日，套用到期日設定的群組成員都要有 Azure AD Premium 授權或 Azure AD Basic EDU 授權。
> 
> 對於有 Azure AD Basic EDU 授權的客戶：若是第一次設定此原則，請使用 Azure Active Directory PowerShell Cmdlet。 在這之後，您可以使用 PowerShell 或 Azure AD 入口網站更新到期設定，透過 Azure AD 租用戶中的使用者帳戶管理員或全域管理員帳戶。

如需如何下載及安裝 Azure AD PowerShell Cmdlet 的資訊，請參閱 [Azure Active Directory PowerShell for Graph - 公開預覽版本 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)。

## <a name="set-group-expiration"></a>設定群組到期日

1. 開啟其帳戶在 Azure AD 租用戶中為全域系統管理員的 [Azure AD 系統管理中心](https://aad.portal.azure.com)。

2. 開啟 Azure AD，選取 [使用者和群組]。

3. 選取 [群組設定]，然後選取 [到期]，以開啟到期設定。
  
  ![[到期] 刀鋒視窗](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. 在 [到期] 刀鋒視窗中，您可以：

  * 設定群組的存留期 (以天為單位)。 您可以選取其中一個預設值或自訂值 (應為 31 天或更多)。 
  * 指定當群組沒有擁有者時應該傳送續訂和到期通知的電子郵件地址。 
  * 選取到期的 Office 365 群組。 您可以針對**所有** Office 365 群組啟用到期、從 Office 365 群組中選擇啟用對象，或選取 [無] 以停用所有群組的到期。
  * 當您完成時，選取 [儲存] 會儲存您的設定。


這種電子郵件通知會在群組到期前 30 天、前 15 天和前 1 天傳送給 Office 365 群組擁有者。

![到期電子郵件通知](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

接著，群組擁有者可選取 [續訂群組] 以續訂 Office 365 群組，或可選取 [移至群組] 以查看成員和關於群組的其他詳細資料。

當群組到期時，會在到期日隔天刪除群組。 這種電子郵件通知會傳送給 Office 365 群組擁有者，通知他們 Office 365 群組的到期日和後續刪除。

![群組刪除電子郵件通知](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

藉由選取 [還原群組] 或使用 PowerShell Cmdlet 可還原群組，如 [在 Azure Active Directory 中還原已刪除的 Office 365 群組] (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal) 中所述。
    
如果您要還原的群組包含文件、SharePoint 網站或其他持續物件，則可能需要 24 小時，才能完全還原群組及其內容。

> [!NOTE]
> * 當您第一次設定到期日時，任何早於到期間隔的群組會設定為 30 後到期。 第一封續訂通知電子郵件會在第一天發出。 
>   例如，400 天前已建立群組 A，而且到期間隔設定為 180 天。 您套用了到期設定，除非擁有者續訂群組 A，不然該群組就會在 30 天後刪除。
> * 刪除並還原動態群組時，會將其視為新群組，並根據規則重新填入。 此流程可能需要多達 24 小時的時間。

## <a name="next-steps"></a>後續步驟
這些文章提供有關 Azure AD 群組的其他資訊。

* [查看現有的群組](active-directory-groups-view-azure-portal.md)
* [管理群組的設定](active-directory-groups-settings-azure-portal.md)
* [管理群組的成員](active-directory-groups-members-azure-portal.md)
* [管理群組的成員資格](active-directory-groups-membership-azure-portal.md)
* [管理群組中使用者的動態規則](active-directory-groups-dynamic-membership-azure-portal.md)
