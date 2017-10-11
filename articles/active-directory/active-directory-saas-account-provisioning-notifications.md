---
title: "帳戶佈建通知 | Microsoft Docs"
description: "藉由啟用帳戶佈建通知，了解如何確保您會收到與使用者佈建相關問題的通知要您特別注意。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: a637aac7-f06b-48ef-a66d-639835a8edec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.openlocfilehash: b99037fc28eca1a3ebffefb9e99991e74f52c9a5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="account-provisioning-notifications"></a>帳戶佈建通知
透過使用者佈建，您可以自動化在協力廠商 SaaS 應用程式中管理使用者的程序。 <br>
雖然這是自動化的程序，但是您還是不時需要與此程序互動。 <br>
例如以下這個範例，當您設定與協力廠商 SaaS 應用程式交換資料的帳戶密碼已經到期。 

藉由啟用帳戶佈建通知，您就可以確保您會收到與使用者佈建相關問題的通知要您特別注意。

您在為協力廠商 SaaS 應用程式設定使用者佈建時，就可以啟用或停用帳戶佈建通知。

![使用者佈建][1] 

若要啟用帳戶佈建通知，請在 [確認]  對話方塊頁面上選取相關的核取方塊，然後輸入收件者的電子郵件別名。

![帳戶佈建通知][2]

您可以輸入通訊群組清單做為收件者。不過請務必注意，通知電子郵件所包含的報表連結，只能由 Azure AD 系統管理員存取。

如果您啟用了帳戶佈建通知，您將會收到電子郵件，通知您與使用者佈建相關的重大問題。 不過為了避免電子郵件多載，對於已啟用電子郵件通知的每個 SaaS 應用程式，您每天只會收到一封通知電子郵件。

## <a name="related-articles"></a>相關文章
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [自動化 SaaS 應用程式使用者佈建/解除佈建](active-directory-saas-app-provisioning.md)
* [自訂使用者佈建的屬性對應](active-directory-saas-customizing-attribute-mappings.md)
* [撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [適用於使用者佈建的範圍篩選器](active-directory-saas-scoping-filters.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](active-directory-scim-provisioning.md)
* [如何整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png
