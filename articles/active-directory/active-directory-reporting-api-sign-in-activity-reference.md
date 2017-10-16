---
title: "Azure Active Directory 登入活動報告 API 參考 | Microsoft Docs"
description: "Azure Active Directory 登入活動報告 API 的參考"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ddcd9ae0-f6b7-4f13-a5e1-6cbf51a25634
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: d83f1a899ba38dab2c1c1661adede87db6f88c20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Azure Active Directory 登入活動報告 API 參考
本主題是 Azure Active Directory 報告 API 相關主題集合的一部分。  
Azure AD 報告提供的 API 可讓您使用程式碼或相關工具來存取登入活動報告資料。
本主題的範疇是為您提供有關 **登入活動報告 API**的參考資訊。

請參閱：

* [登入活動](active-directory-reporting-azure-portal.md#activity-reports) 以取得詳細概念資訊
* [開始使用 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md) 以取得報告 API 的詳細資訊。


## <a name="who-can-access-the-api-data"></a>誰可以存取 API 資料？
* 具有安全性系統管理員或安全性讀取者角色的使用者和服務主體
* 全域管理員
* 任何獲得授權存取 API 的應用程式 (只可以根據全域管理員的權限來設定應用程式授權)

若要設定應用程式存取權以存取安全性 API (例如登入事件)，請使用下列 PowerShell 將應用程式服務主體新增至安全性讀取者角色

```PowerShell
Connect-MsolService
$servicePrincipal = Get-MsolServicePrincipal -AppPrincipalId "<app client id>"
$role = Get-MsolRole | ? Name -eq "Security Reader"
Add-MsolRoleMember -RoleObjectId $role.ObjectId -RoleMemberType ServicePrincipal -RoleMemberObjectId $servicePrincipal.ObjectId
```

## <a name="prerequisites"></a>必要條件
若要透過報告 API 來存取此報告，您必須具有︰

* [Azure Active Directory Premium P1 或 P2 版本](active-directory-editions.md)
* 了解 [存取 Azure AD 報告 API 的必要條件](active-directory-reporting-api-prerequisites.md)。 

## <a name="accessing-the-api"></a>存取 API
您可以透過 [Graph 總管](https://graphexplorer2.cloudapp.net) 或以程式設計方式使用 PowerShell 來存取此 API。 為了讓 PowerShell 正確地解譯 AAD Graph REST 呼叫中使用的 OData 篩選語法，您必須使用倒單引號 (也稱為︰抑音符號) 字元來「逸出」$ 字元。 倒單引號字元可做為 [PowerShell 的逸出字元](https://technet.microsoft.com/library/hh847755.aspx)，讓 PowerShell 進行 $ 字元的常值解譯，並且避免將它與 PowerShell 變數名稱 (例如︰$filter) 搞混。

本主題的重點在於 Graph 總管。 如需 PowerShell 範例，請參閱此 [PowerShell 指令碼](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script)。

## <a name="api-endpoint"></a>API 端點
您可以使用下列基底 URI 來存取此 API︰  

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



由於資料量的緣故，此 API 有一百萬筆傳回記錄的限制。 

此呼叫會分批傳回資料。 每個批次的上限為 1000 筆記錄。  
若要取得下一批記錄，請使用 [下一個] 連結。 從第一組傳回的資料取得 [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) 資訊。 略過 Token 位於結果集的結尾。  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>支援的篩選器
您可以篩選形式縮小 API 呼叫所傳回的記錄筆數。  
對於登入 API 相關資料，支援下列篩選︰

* **$top=\<傳回的記錄筆數\>** - 限制傳回的記錄筆數。 這是一項昂貴的作業。 如果您想要傳回數千個物件，則不應該使用此篩選器。  
* **$filter=\<您的篩選陳述式\>** - 根據支援的篩選欄位，指定您關心的記錄類型

## <a name="supported-filter-fields-and-operators"></a>支援的篩選欄位和運算子
若要指定您關心的記錄類型，您可以建立一個篩選陳述式，該陳述式可包含下列其中一個篩選欄位或其組合︰

* [signinDateTime](#signindatetime) - 定義日期或日期範圍
* [userId](#userid) - 根據使用者的識別碼定義特定使用者。
* [userPrincipalName](#userprincipalname) - 根據使用者的使用者主體名稱 (UPN) 定義特定使用者
* [appId](#appid) - 根據應用程式的識別碼定義特定應用程式
* [appDisplayName](#appdisplayname) - 根據應用程式的顯示名稱定義特定應用程式
* [loginStatus](#loginStatus) - 定義登入狀態 (成功 / 失敗)

> [!NOTE]
> 使用 Graph 總管時，您需要針對篩選欄位中的字母使用正確的大小寫。
> 
> 

若要縮小傳回的資料範圍，您可以建立支援的篩選和篩選欄位組合。 例如，下列陳述式會傳回 2016 年 7 月 1 日與2016 年 7 月 6 日之間的前 10 筆記錄︰

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


- - -
### <a name="signindatetime"></a>signinDateTime
**支援的運算子**：eq、ge、le、gt、lt

**範例**：

使用特定日期

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z    



使用日期範圍    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**注意**：

datetime 參數應採用 UTC 格式 

- - -
### <a name="userid"></a>userId
**支援的運算子**：eq

**範例**：

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**注意**：

userId 的值是字串值

- - -
### <a name="userprincipalname"></a>userPrincipalName
**支援的運算子**：eq

**範例**：

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**注意**：

userPrincipalName 的值是字串值

- - -
### <a name="appid"></a>appId
**支援的運算子**：eq

**範例**：

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**注意**：

appId 的值是字串值

- - -
### <a name="appdisplayname"></a>appDisplayName
**支援的運算子**：eq

**範例**：

    $filter=appDisplayName+eq+'Azure+Portal' 


**注意**：

appDisplayName 的值是字串值

- - -
### <a name="loginstatus"></a>loginStatus
**支援的運算子**：eq

**範例**：

    $filter=loginStatus+eq+'1'  


**注意**：

loginStatus 有兩個選項：0 - 成功、1 - 失敗

- - -
## <a name="next-steps"></a>後續步驟
* 您想要查看篩選過的登入活動範例嗎？ 請查看 [Azure Active Directory 登入活動報告 API 範例](active-directory-reporting-api-sign-in-activity-samples.md)。
* 您想要深入了解 Azure AD 報告 API 嗎？ 請參閱 [開始使用 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md)。

