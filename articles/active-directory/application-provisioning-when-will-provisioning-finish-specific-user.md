---
title: "查明特定使用者何時將能存取應用程式 | Microsoft Docs"
description: "如何查明非常重要的使用者何時能夠存取您已設定的應用程式，以透過 Azure AD 進行使用者佈建"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: fcefb31904cfb77022db0358e9feee6a0479db81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>查明特定使用者何時將能存取應用程式
透過應用程式使用自動使用者佈建時，Azure AD 會根據像是[使用者和群組指派](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)等事項，在定期排定的時間間隔內 (通常是每隔 10 分鐘)，自動佈建並更新應用程式中的使用者帳戶。

## <a name="how-long-does-it-take"></a>需要多久的時間？

針對要佈建的特定使用者所花費的時間，主要是根據初始的「完整」同步處理是否已經發生而定。

根據 Azure AD 目錄大小和佈建範圍中的使用者數目而定，Azure AD 和應用程式之間的第一次同步處理可能會花費 20 分鐘到數小時。 

初始同步處理之後的後續同步處理會更快 (例如，在 10 分鐘內)，因為佈建服務會儲存浮水印，代表兩個系統在初始同步處理之後的狀態，所以會改善後續同步處理的效能。

## <a name="how-to-check-the-status-of-a-user"></a>如何檢查使用者的狀態

若要查看所選使用者的佈建狀態，請參閱 Azure AD 中的稽核記錄。

您可以在 Azure 入口網站的 [Azure Active Directory]&gt;[企業應用程式]&gt;[\[應用程式名稱\]]&gt;[稽核記錄] 索引標籤中存取佈建稽核記錄。基於 [帳戶佈建] 類別來篩選記錄，只顯示該應用程式的佈建事件。 您可以根據「比對識別碼」來搜尋使用者，此識別碼是在屬性對應中針對使用者所設定。 

例如，如果您設定了「使用者主體名稱」或「電子郵件地址」做為 Azure AD 端的比對屬性，而且未佈建的使用者具有 "audrey@contoso.com" 的值，接著搜尋 “audrey@contoso.com” 的稽核記錄，然後檢閱傳回的項目。

佈建的稽核記錄會記錄佈建服務所執行的所有作業，包括：

* 查詢位於佈建範圍內之已指派使用者的 Azure AD
* 查詢目標應用程式以確定那些使用者是否存在
* 比較系統之間的使用者物件
* 根據比較，在目標系統中新增、更新或停用使用者帳戶

## <a name="next-steps"></a>後續步驟
[自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
