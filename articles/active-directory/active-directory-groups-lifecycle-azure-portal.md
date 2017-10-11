---
title: "在 Azure Active Directory 中預覽 Office 365 群組到期日 | Microsoft Docs"
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
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 8a43df84fd050d7b4bd8d937b8c55e744cb805d3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="configure-office-365-groups-expiration-preview"></a>設定 Office 365 群組到期日 (預覽)

您現在可以藉由設定到期日，對您選取任何 Office 365 群組管理 Office 365 群組的生命週期。 一旦設定此到期日，如果這些群組的擁有者仍需要群組，則會要求他們續訂。 未續訂的任何 Office 365 群組將會予以刪除。 群組擁有者或系統管理員可在 30 天內還原已刪除的任何 Office 365 群組。  


> [!NOTE]
> 您可以僅針對 Office 365 群組設定到期日。
>
> 設定已指派 Azure AD Premium 授權之 O365 群組所需要的到期日
>   - 為租用戶設定到期設定的系統管理員
>   - 為此設定選取之群組的所有成員

## <a name="set-office-365-groups-expiration"></a>設定 Office 365 群組到期日

1. 開啟其帳戶在 Azure AD 租用戶中為全域系統管理員的 [Azure AD 系統管理中心](https://aad.portal.azure.com)。

2. 開啟 Azure AD，選取 [使用者和群組]。

3. 選取 [群組設定]，然後選取 [到期]，以開啟到期設定。
  
  ![[到期] 刀鋒視窗](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. 在 [到期] 刀鋒視窗中，您可以：

  * 設定群組的存留期 (以天為單位)。 您可以選取其中一個預設值或自訂值 (應為 31 天或更多)。 
  * 指定當群組沒有擁有者時應該傳送續訂和到期通知的電子郵件地址。 
  * 選取到期的 Office 365 群組。 您可以針對**所有** Office 365 群組啟用到期、從 Office 365 群組中選擇啟用對象，或選取 [無] 以停用所有群組的到期。
  * 當您完成時，選取 [儲存] 會儲存您的設定。

如需如何下載及安裝 Microsoft PowerShell 模組以透過 PowerShell 設定 Office 365 群組之到期日的指示，請參閱 [Azure Active Directory V2 PowerShell 模組 - 公開預覽版本 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)。

這種電子郵件通知會在群組到期前 30 天、前 15 天和前 1 天傳送給 Office 365 群組擁有者。

![到期電子郵件通知](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

接著，群組擁有者可選取 [續訂群組] 以續訂 Office 365 群組，或可選取 [移至群組] 以查看成員和關於群組的其他詳細資料。

當群組到期時，會在到期日隔天刪除群組。 這種電子郵件通知會傳送給 Office 365 群組擁有者，通知他們 Office 365 群組的到期日和後續刪除。

![群組刪除電子郵件通知](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

藉由選取 [還原群組] 或使用 PowerShell Cmdlet 可還原群組，如 [在 Azure Active Directory 中還原已刪除的 Office 365 群組] (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal) 中所述。
    
如果您要還原的群組包含文件、SharePoint 網站或其他持續物件，則可能需要 24 小時，才能完全還原群組及其內容。

> [!NOTE]
> * 部署到期設定時，可能會有一些群組已超過到期時間。 這些群組不會立即刪除，但會設定為 30 天後到期。 第一封續訂通知電子郵件會在第一天發出。 例如，400 天前已建立群組 A，而且到期間隔設定為 180 天。 您套用了到期設定，除非擁有者續訂群組 A，不然該群組就會在 30 天後刪除。
> * 刪除並還原動態群組時，會將其視為新群組，並根據規則重新填入。 此流程可能需要多達 24 小時的時間。

## <a name="next-steps"></a>後續步驟
這些文章提供有關 Azure AD 群組的其他資訊。

* [查看現有的群組](active-directory-groups-view-azure-portal.md)
* [管理群組的設定](active-directory-groups-settings-azure-portal.md)
* [管理群組的成員](active-directory-groups-members-azure-portal.md)
* [管理群組的成員資格](active-directory-groups-membership-azure-portal.md)
* [管理群組中使用者的動態規則](active-directory-groups-dynamic-membership-azure-portal.md)
