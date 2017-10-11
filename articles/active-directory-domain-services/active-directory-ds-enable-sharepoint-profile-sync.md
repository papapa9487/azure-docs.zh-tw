---
title: "Azure Active Directory Domain Services：啟用對 SharePoint User Profile Service 的支援 | Microsoft Docs"
description: "設定 Azure Active Directory Domain Services 受管理網域以支援 SharePoint Server 的設定檔同步處理"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: c3c923b5c9cd652f0c5b0ec98c1cda740f180122
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>設定受管理網域以支援 SharePoint Server 的設定檔同步處理
SharePoint Server 包含用來同步處理使用者設定檔的 User Profile Service。 若要設定 User Profile Service，必須在 Active Directory 網域授與適當的權限。 如需詳細資訊，請參閱[授與 Active Directory Domain Services 權限以供 SharePoint Server 2013 中的設定檔同步處理使用](https://technet.microsoft.com/library/hh296982.aspx)。

本文說明如何設定 Azure AD Domain Services 受管理網域，以部署「SharePoint Server 使用者設定檔同步處理」服務。

## <a name="the-aad-dc-service-accounts-group"></a>'AAD DC Service Accounts' 群組
受管理網域上的 'Users' 組織單位內有一個名為 '**AAD DC Service Accounts**' 的安全性群組。 您可以在 [Active Directory 使用者和電腦] MMC 嵌入式管理單元中的受管理網域上，看到這個群組。

![AAD DC Service Accounts 安全性群組](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

下列權限會委派給此安全性群組的成員：
- 受管理網域之根 DSE 上的「複寫目錄變更」權限。
- 受管理網域之組態命名內容 (cn=組態容器) 上的「複寫目錄變更」權限。

此安全性群組也是內建群組 **Pre-Windows 2000 Compatible Access** 的成員。

![AAD DC Service Accounts 安全性群組](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>可讓您的受管理網域支援 SharePoint Server 使用者設定檔同步處理
您可以將用於 SharePoint 使用者設定檔同步處理的服務帳戶新增到 **AAD DC Service Accounts** 群組。 如此一來，同步處理帳戶就會獲得可將變更複寫到目錄的適當權限。 此組態步驟可讓 SharePoint Server 使用者設定檔同步處理正確運作。

![AAD DC Service Accounts - 新增成員](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![AAD DC Service Accounts - 新增成員](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>相關內容
* [技術參考 - 授與 Active Directory Domain Services 權限以供 SharePoint Server 2013 中的設定檔同步處理使用](https://technet.microsoft.com/library/hh296982.aspx)
