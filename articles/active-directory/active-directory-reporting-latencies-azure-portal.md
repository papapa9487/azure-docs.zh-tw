---
title: "Azure Active Directory 報告延遲 | Microsoft Docs"
description: "深入了解在您的 Azure 入口網站中針對顯示報告事件所花費的時間長度"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi;dhanyahk
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 93cb0baeab8f13f81257ed1bd32ed08561c54b72
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory 報告延遲

透過 Azure Active Directory 中的[報告](active-directory-preview-explainer.md)，您可取得判斷您的環境執行狀況所需的所有資訊。 Azure 入口網站中針對顯示報告資料所花費的時間長度也稱為延遲。 

本主題列出 Azure 入口網站中所有報告類別的延遲資訊。 


## <a name="activity-reports"></a>活動報告

有兩個活動報告的區域︰

- **登入活動** – 受管理應用程式和使用者登入活動的使用情況相關資訊
- **稽核記錄檔** - 使用者和群組管理、受管理應用程式和目錄活動的相關系統活動資訊

下表列出活動報告的延遲資訊。

| 報告 | 最小值 | 平均值 | 最大值 |
| :-- | --- | --- | --- |
| 稽核記錄檔             | 30 分鐘  | 45 分鐘 | 1 小時     |
| 登入               | 15 Minuten  | 15 Minuten | 2 小時*   |

>[!NOTE]
> 對於某些來自舊版 Office 應用程式的登入活動資料，可能需要 8 小時才會顯示報告資料。 


## <a name="security-reports"></a>安全性報告

有兩個安全性報告的區域︰

- **有風險的登入** - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。 
- **標幟為有風險的使用者** - 有風險的使用者表示可能被盜用的使用者帳戶。 

下表列出安全性報告的延遲資訊。

| 報告 | 最小值 | 平均值 | 最大值 |
| :-- | --- | --- | --- |
| 有風險的使用者          | 5 分鐘   | 15 Minuten  | 2 小時  |
| 有風險的登入         | 5 分鐘   | 15 Minuten  | 2 小時  |

## <a name="risk-events"></a>風險事件

Azure Active Directory 使用調適性機器學習服務演算法和啟發學習法，來偵測與您使用者帳戶相關的可疑動作。 偵測到的每個可疑動作都會儲存在名為風險事件的記錄中。

下表列出風險事件的延遲資訊。

| 報告 | 最小值 | 平均值 | 最大值 |
| :-- | --- | --- | --- |
| 從匿名 IP 位址登入 |5 分鐘 |15 分鐘 |2 小時 |
| 從不熟悉的位置登入 |5 分鐘 |15 分鐘 |2 小時 |
| 認證外洩的使用者 |2 小時 |4 小時 |8 小時 |
| 不可能到達非典型位置的移動 |5 分鐘 |1 小時 |8 小時  |
| 從受感染的裝置登入 |2 小時 |4 小時 |8 小時  |
| 從具有可疑活動的 IP 位址登入 |2 小時 |4 小時 |8 小時  |



## <a name="next-steps"></a>後續步驟

如果您想要深入了解 Azure 入口網站中的活動報告，請參閱︰

- [Azure Active Directory 入口網站中的登入活動報告](active-directory-reporting-activity-sign-ins.md)
- [Azure Active Directory 入口網站中的稽核活動報告](active-directory-reporting-activity-audit-logs.md)

如果您想要深入了解 Azure 入口網站中的安全性報告，請參閱︰

- [Azure Active Directory 入口網站中具有風險的使用者安全性報告](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory 入口網站中的風險登入報告](active-directory-reporting-security-risky-sign-ins.md)

如果您想要深入了解風險事件，請參閱 [Azure Active Directory 風險事件](active-directory-reporting-risk-events.md)。

