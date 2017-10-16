---
title: "存取 Azure AD 報告 API 的必要條件 | Microsoft Docs"
description: "了解存取 Azure AD 報告 API 的必要條件"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5fafd83c337e3c73260d89cdad7409a01ce5855b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>存取 Azure AD 報告 API 的必要條件

[Azure AD 報告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 透過一組以 REST 為基礎的 API 為您提供資料的程式設計方式存取。 您可以從各種程式設計語言和工具呼叫這些 API。

報告 API 會使用 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 授權存取 Web API。 

若要透過 API 來存取報告資料，您必須已具備下列其中一個指派的角色︰

- 安全性讀取者
- 安全性系統管理員
- 全域管理員


若要準備存取報告 API，您必須︰

1. 註冊應用程式 
2. 授與權限 
3. 收集組態設定 

如有相關疑問、問題或意見，請[提出支援票證](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto)。

## <a name="register-an-azure-active-directory-application"></a>註冊 Azure Active Directory 應用程式

即使您使用指令碼來存取報告 API，也必須註冊應用程式。 註冊可讓您取得**應用程式識別碼**，您必須有此識別碼才能進行授權呼叫，以便讓程式碼獲得權杖。

若要設定您的目錄以存取 Azure AD 報告 API，您必須以 Azure 管理員帳戶登入 Azure 入口網站，而且該帳戶同時也是 Azure AD 租用戶**全域管理員**目錄角色的成員。

> [!IMPORTANT]
> 像這樣使用具有「管理員」權限的認證所執行的應用程式會非常強大，請務必確保應用程式的識別碼/密碼認證的安全性。
> 


**若要註冊 Azure Active Directory 應用程式：**

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在 [Azure Active Directory] 刀鋒視窗中，按一下 [應用程式註冊]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. 在 [應用程式註冊] 刀鋒視窗上，按一下頂端工具列中的 [新增應用程式註冊]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. 在 [建立] 刀鋒視窗上，執行下列步驟︰

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. 在 [名稱] 文字方塊中，輸入 `Reporting API application`。

    b. 對於 [應用程式類型]，選取 [Web 應用程式/API]。

    c. 在 [登入 URL] 文字方塊中，輸入 `https://localhost`。

    d. 按一下 [建立] 。 


## <a name="grant-permissions"></a>授與權限 

此步驟的目標是要對應用程式授與 **Windows Azure Active Directory** API 的**讀取目錄資料**權限。

![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/16.png)
 

**若要對應用程式授與 API 使用權限：**

1. 在 [應用程式註冊] 刀鋒視窗中，按一下應用程式清單中的 [報告 API 應用程式]。

2. 在 [報告 API 應用程式] 刀鋒視窗中，按一下頂端工具列中的 [設定]。 

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. 在 [設定] 刀鋒視窗中，按一下 [必要權限]。 

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. 在 [必要權限] 刀鋒視窗中，按一下 [API] 清單中的 [Windows Azure Active Directory]。 

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. 在 [啟用存取] 刀鋒視窗中，選取 [讀取目錄資料]。 

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. 在頂端工具列中，按一下 [儲存]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

## <a name="gather-configuration-settings"></a>收集組態設定 
本節說明如何從您的目錄取得下列設定︰

* 網域名稱
* 用戶端識別碼
* 用戶端密碼

您在設定報告 API 的呼叫時需要這些值。 

### <a name="get-your-domain-name"></a>取得您的網域名稱

**若要取得網域名稱：**

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在 [Azure Active Directory] 刀鋒視窗中，按一下 [網域名稱]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. 從網域清單中複製網域名稱。


### <a name="get-your-applications-client-id"></a>取得應用程式的用戶端識別碼

**若要取得應用程式的用戶端識別碼：**

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在 [應用程式註冊] 刀鋒視窗中，按一下應用程式清單中的 [報告 API 應用程式]。

3. 在 [報告 API 應用程式] 刀鋒視窗中，按一下 [應用程式識別碼] 中的 [按一下以複製]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>取得應用程式的用戶端祕密
若要取得應用程式的用戶端密碼，您需要建立新的金鑰並在儲存新金鑰時儲存其值，因為稍後不可能再擷取此值。

**若要取得應用程式的用戶端祕密：**

1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在 [應用程式註冊] 刀鋒視窗中，按一下應用程式清單中的 [報告 API 應用程式]。


3. 在 [報告 API 應用程式] 刀鋒視窗中，按一下頂端工具列中的 [設定]。 

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. 在 [設定] 刀鋒視窗中，按一下 [APIR 存取] 區段中的 [金鑰]。 

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. 在 [金鑰] 刀鋒視窗上，執行下列步驟︰

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. 在 [描述] 文字方塊中，輸入 `Reporting API`。

    b.這是另一個 C# 主控台應用程式。 選取 [2 年後] 來作為 [到期]。

    c. 按一下 [儲存] 。

    d. 複製金鑰值。


## <a name="next-steps"></a>後續步驟
* 您要以程式設計方式從 Azure AD 報告 API 存取資料嗎？ 請查看 [開始使用 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md)。
* 如果您想要深入了解 Azure Active Directory 報告，請參閱 [Azure Active Directory 報告指南](active-directory-reporting-guide.md)。  

