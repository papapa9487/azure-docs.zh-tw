---
title: "Azure Active Directory 入口網站中的登入活動報告 | Microsoft Docs"
description: "介紹 Azure Active Directory 入口網站中的登入活動報告"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: d28def78ef34e7c41e846fc10771992f109efd45
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2017
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的登入活動報告

透過 [Azure 入口網站](https://portal.azure.com)中的 Azure Active Directory (Azure AD) 報告，您可以取得判斷您的環境執行狀況所需的資訊。

Azure Active Directory 中的報告架構包含下列元件：

- **活動** 
    - **登入活動** – 受管理應用程式和使用者登入活動的使用情況相關資訊
    - **稽核記錄** - 關於使用者和群組管理、受管理應用程式和目錄活動的系統活動資訊。
- **安全性** 
    - **有風險的登入** - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。 如需詳細資訊，請參閱＜有風險的登入＞。
    - **標幟為有風險的使用者** - 有風險的使用者表示可能被盜用的使用者帳戶。 如需詳細資訊，請參閱＜標幟為有風險的使用者＞。

本主題提供登入活動的概觀。

## <a name="pre-requisite"></a>必要條件

### <a name="who-can-access-the-data"></a>誰可以存取資料？
* 具有安全性系統管理員或安全性讀取器角色的使用者
* 全域管理員
* 任何使用者 (非系統管理員) 都可以存取自己的登入 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>您需要哪項 Azure AD 授權才能存取登入活動？
* 租用戶必須要有相關聯的 Azure AD Premium 授權，才能查看活動報告中的所有登入


## <a name="signs-in-activities"></a>登入活動

利用使用者登入報告所提供的資訊，您可以找到下列問題的解答︰

* 使用者的登入模式為何？
* 一週內有多少使用者登入？
* 這些登入的狀態為何？

所有登入活動資料的第一個進入點是 **Azure Active** [活動] 區段中的 [登入]。


![登入活動](./media/active-directory-reporting-activity-sign-ins/61.png "登入活動")


稽核記錄的預設清單檢視顯示︰

- 相關的使用者
- 使用者已登入的應用程式
- 登入狀態
- 登入時間

![登入活動](./media/active-directory-reporting-activity-sign-ins/41.png "登入活動")

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

![登入活動](./media/active-directory-reporting-activity-sign-ins/19.png "登入活動")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![登入活動](./media/active-directory-reporting-activity-sign-ins/42.png "登入活動")

按一下清單檢視中的項目，即可取得所有可用的詳細資料。

![登入活動](./media/active-directory-reporting-activity-sign-ins/43.png "登入活動")


## <a name="filtering-sign-in-activities"></a>篩選登入活動

若要將報告的資料縮小至您適用的層級，您可以使用下列欄位篩選登入資料︰

- 時間間隔
- User
- 應用程式
- 用戶端
- 登入狀態

![登入活動](./media/active-directory-reporting-activity-sign-ins/44.png "登入活動")


**時間間隔**篩選條件可讓您定義傳回資料的時間範圍。  
可能的值包括：

- 1 個月
- 7 天
- 24 小時
- 自訂

當您選取自訂時間範圍時，可以設定開始時間和結束時間。

**使用者**篩選條件可讓您指定您關心的使用者之名稱或使用者主體名稱 (UPN)。

**應用程式**應用程式可讓您指定您關心的應用程式之名稱。

**用戶端**篩選條件可讓您指定您關心的裝置之相關資訊。

**登入狀態**篩選條件可讓您選取下列其中一個篩選條件︰

- 全部
- 成功
- 失敗


## <a name="sign-in-activities-shortcuts"></a>登入活動捷徑

除了 Azure Active Directory 之外，Azure 入口網站可提供您登入活動資料的兩個額外進入點︰

- 使用者和群組
- 企業應用程式


### <a name="users-and-groups-sign-ins-activities"></a>使用者和群組登入活動

利用使用者登入報告所提供的資訊，您可以找到下列問題的解答︰

- 使用者的登入模式為何？
- 一週內有多少使用者登入？
- 這些登入的狀態為何？



此資料的進入點是 [使用者和群組] 之下 [概觀] 區段中的使用者登入圖。

![登入活動](./media/active-directory-reporting-activity-sign-ins/45.png "登入活動")

使用者登入圖會顯示在指定的時間週期中所有使用者的每週登入彙總。 時間週期的預設值是 30 天。

![登入活動](./media/active-directory-reporting-activity-sign-ins/46.png "登入活動")

當您按一下登入圖中的某一天時，您會取得當日登入活動的詳細清單。

![登入活動](./media/active-directory-reporting-activity-sign-ins/41.png "登入活動")

登入活動清單中的每一列會提供有關所選登入的詳細資訊，例如︰

* 誰已登入？
* 相關的 UPN 是什麼？
* 哪個應用程式是登入的目標？
* 登入的 IP 位址為何？
* 登入的狀態為何？

[登入] 選項會提供您所有使用者登入的完整概觀。

![登入活動](./media/active-directory-reporting-activity-sign-ins/51.png "登入活動")



## <a name="usage-of-managed-applications"></a>受管理應用程式的使用情況

利用登入資料以應用程式為主的檢視，您可以回答下列問題︰

* 誰在使用我的應用程式？
* 您的組織中排名前 3 個應用程式為何？
* 我最近已推出一個應用程式。 它的情況為何？

此資料的進入點是在 [企業應用程式] 之下 [概觀] 區段中的最近 30 天報告內您的組織中排名前 3 個應用程式。

![登入活動](./media/active-directory-reporting-activity-sign-ins/64.png "登入活動")

應用程式使用圖會顯示在指定的時間週期中排名前 3 個應用程式的每週登入彙總。 時間週期的預設值是 30 天。

![登入活動](./media/active-directory-reporting-activity-sign-ins/47.png "登入活動")

如果您想要，您可以將焦點設在特定的應用程式。


![報告](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "報告")

當您按一下應用程式使用圖中的某一天時，您會取得登入活動的詳細清單。


![登入活動](./media/active-directory-reporting-activity-sign-ins/48.png "登入活動")


[登入]  選項會提供您的應用程式的所有登入事件的完整概觀。

![登入活動](./media/active-directory-reporting-activity-sign-ins/49.png "登入活動")



## <a name="next-steps"></a>後續步驟

如果您想深入了解登入活動錯誤碼，請參閱 [Azure Active Directory 入口網站中的登入活動報告錯誤碼](active-directory-reporting-activity-sign-ins-errors.md)。

