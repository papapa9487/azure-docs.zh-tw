---
title: "Azure Active Directory 報告保留原則 | Microsoft Docs"
description: "Azure Active Directory 中報告資料的保留原則"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/05/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: aa7a69c933abfda3bf4d1ac1a298c4ba684efd7e
ms.contentlocale: zh-tw
ms.lasthandoff: 04/06/2017

---
# <a name="azure-active-directory-report-retention-policies"></a>Azure Active Directory 報告保留原則


本主題為您提供 Azure Active Directory 中各種活動報告的資料保留相關常見問題的解答。 

**問：如何才能開始收集活動資料？**

**答：**

| Azure AD 版本 | 開始收集 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | 當您註冊訂用帳戶時 |
| Azure AD Free | 首次開啟 [Azure Active Directory 刀鋒視窗](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)或使用[報告 API](https://aka.ms/aadreports)  |

---
**問：何時可在 Azure 入口網站中取得您的活動資料？**

**答：**

- **立即** - 如果您已在 Azure 傳統入口網站中使用報告
- **2 小時內** - 如果您尚未在 Azure 傳統入口網站中開啟報告功能

---
**問：如何才能開始收集安全性訊號？**  

**答：**對於安全性訊號，收集程序會在您選擇使用 Identity Protection 中心時啟動。 


---
**問：收集的資料會儲存多久？**

**答：**

**活動報告**    

| 報告                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| 目錄稽核        | 7 天        | 30 天             | 30 天             |
| 登入活動       | 7 天        | 30 天             | 30 天             |

**安全性訊號**

| 報告         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| 有風險的使用者  | 7 天        | 30 天             | 90 天             |
| 有風險的登入 | 7 天        | 30 天             | 90 天             |

---
