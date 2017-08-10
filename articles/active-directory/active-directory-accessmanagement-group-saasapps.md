---
title: "使用群組管理 SaaS 應用程式的存取權 | Microsoft Docs"
description: "如何使用 Azure Active Directory Premium 或 Basic 中的群組指派與 Azure Active Directory 整合的 SaaS 應用程式存取權。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: ab8dee63-bedc-46ca-8852-234f5c16ae98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro;oldportal
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: d350011ee9fc5ced9ddb16993f68d3c840a645a5
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>使用群組管理 SaaS 應用程式的存取權
使用 Azure Active Directory (Azure AD) 搭配 Azure AD Premium 或 Azure AD Basic 授權，您可以使用群組指派與 Azure AD 整合之 SaaS 應用程式的存取權。 例如，如果您想要指派行銷部門使用五個不同 SaaS 應用程式的存取權，可以建立一個包含行銷部門中的使用者的群組，然後將該群組指派給行銷部門需要的這五個 SaaS 應用程式。 如此一來，您可以在同一個地方管理行銷部門的成員資格以節省時間。 接著當使用者新增為行銷群組的成員時，會將這些使用者指派給應用程式，並在使用者從行銷群組中移除時，從應用程式中移除其指派。

> [!IMPORTANT]
> Microsoft 建議您使用 Azure 入口網站中的 [Azure AD 系統管理中心](https://aad.portal.azure.com)來管理 Azure AD，而不要使用本文所提及的 Azure 傳統入口網站。 

此功能可以搭配您能夠從 Azure AD 應用程式庫中新增的數百種應用程式使用。

**將群組存取權指派給 SaaS 應用程式**

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，於左側導覽列上選取 [Active Directory]。
2. 選取 [目錄]  索引標籤，然後開啟您要將群組存取權指派給 Saas 應用程式所在的目錄。
3. 選取 [應用程式]  索引標籤。 選取您從「應用程式資源庫」中新增的應用程式，然後按一下 [使用者和群組] 索引標籤。
4. 在 [使用者和群組] 索引標籤的 [開頭為] 欄位中，輸入您要為其指派存取權的群組名稱，然後選取右上方的核取記號。 您只需要輸入群組名稱的第一個部分。
5. 選取群組，然後選取 [指派存取權]  按鈕。 在看到確認訊息時選取 [是]  。 目前對應用程式的群組式指派並不支援巢狀群組成員資格。
6. 您也可以直接或透過群組中的成員資格，查看哪些使用者指派給應用程式。 若要這樣做，請將 [顯示下拉式清單] 從 [群組] 變更為 [所有使用者]。 此清單會顯示目錄中的使用者，以及每個使用者是否指派給應用程式。 此清單也會顯示獲指派的使用者是直接 (指派類型顯示為 [直接])，還是透過群組成員資格 (指派類型顯示為 [繼承]) 指派給應用程式。

> [!NOTE]
> 只有在啟用 Azure AD Premium 或 Azure AD Basic 之後，您才可以看到 [使用者和群組] 索引標籤。
>
>

### <a name="next-steps"></a>後續步驟
這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [設定群組設定的 Azure Active Directory Cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [什麼是 Azure Active Directory？](active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

