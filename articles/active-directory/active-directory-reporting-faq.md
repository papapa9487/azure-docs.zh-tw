---
title: "Azure Active Directory 報告常見問題集 | Microsoft Docs"
description: "Azure Active Directory 報告常見問題集。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: accf292f70bf0eafdefc00c3feeaf8e346605401
ms.contentlocale: zh-tw
ms.lasthandoff: 08/23/2017

---
# <a name="azure-active-directory-reporting-faq"></a>Azure Active Directory 報告常見問題集

本文會回答有關 Azure Active Directory 報告的常見問題 (FAQ)。  
如需更多詳細資料，請參閱 [Azure Active Directory 報告](active-directory-reporting-azure-portal.md)。 

**問：Azure 入口網站中活動記錄 (稽核和登入) 的資料保留期是多長？** 

**答：**針對免費客戶，我們提供 7 天的資料，而只要切換到 Azure AD Premium 1 或 Premium 2 授權，您便可以存取最長達 30 天的資料。 如需有關保留期的更多詳細資料，請參閱 [Azure Active Directory 報告保留原則](active-directory-reporting-retention.md)

--- 

**問：在我完成工作之後，要多久的時間才能看見活動資料？**

**答：**稽核活動記錄的延遲時間為 15 分鐘到 1 小時。 登入活動記錄中大多數記錄的延遲時間為 15 分鐘，而少數記錄的延遲時間則最長可達 2 小時。

---

**問：我是否必須是全域系統管理員，才能在 Azure 入口網站中看到活動記錄，或是透過 API 取得資料？**

**答：**否。 您只要是「安全性讀取者」、「安全性系統管理員」或「全域系統管理員」，便能在 Azure 入口網站中看見報告資料，或是透過 API 存取該資料。

---

**問：我是否可以透過 Azure 入口網站取得 Office 365 活動記錄資訊？**

**答：**雖然 Office 365 活動和 Azure AD 活動記錄共用許多目錄資源，但如果您想要完整檢視 Office 365 活動記錄，應該前往「Office 365 系統管理中心」以取得 Office 365 活動記錄資訊。

---


**問：我應該使用哪些 API 來取得 Office 365 活動記錄的相關資訊？**

**答：**請使用「Office 365 管理 API」來存取 [Office 365 活動記錄 (透過 API)](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview)。

---

**問：我可以從 Azure 入口網站下載多少記錄？**

**答：**您可以從 Azure 入口網站最多下載 12 萬筆記錄。 這些記錄會依「時間上最近」方式來排序，而根據預設，您會取得最近的 12 萬筆記錄。 

---

**問：我可以透過活動 API 查詢多少記錄？**

**答：**如果您未使用 top 運算子 (依時間上最近方式來排序記錄)，您最多可以查詢 100 萬筆記錄。 如果您使用 “top” 運算子，則最多可以查詢 50 萬筆記錄。 如果有關如何使用 API 的範例查詢，請參閱[這裡](active-directory-reporting-api-getting-started.md)。

---

**問：我要如何取得進階授權？**

**答：**如需此問題的解答，請參閱[開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)。

---

**問：在取得進階授權之後，我應該多快能看見活動資料？**

**答：**如果您在使用免費授權時即已經有活動資料，您便可以看見相同的資料。 如果您沒有任何資料，則必須花一或兩天的時間。

---

**問：在取得 Azure AD 進階授權之後，我是否會看見上個月的資料？**

**答：**如果您是最近切換到進階版 (包括試用版)，一開始最多可以看見 7 天的資料。 當資料累積之後，您將最多可以看見 30 天的資料。

---

**問：Identity Protection 有風險事件，但是我在所有登入中看不到對應的登入。這是預期行為嗎？**

**答：**是，Identity Protection 會評估所有驗證流程的風險，無論是互動式或非互動式。 不過，所有登入只會報告顯示互動式登入。

---

**問：如何下載 Azure 入口網站中的「使用者標示為風險」報告？**

**答：**：下載「使用者標示為風險」報告的選項即將新增。

---

**問：如何知道為何在 Azure 入口網站中登入或使用者會標示為風險？**

**答**：進階版的客戶可以按一下「使用者標示為風險」中的使用者，或按一下「風險登入」，進一步了解基礎風險事件。 Free 和 Basic 版本的客戶只能看到不含基礎風險事件資訊的風險使用者和登入。

---

**問：如何在登入和有風險的登入報告中計算 IP 位址？**

**答：**IP 位址的發出方式如下：IP 位址與該位址實際所在的電腦之間沒有任何明確的連線。 有些因素會使這種情況變復雜，例如行動提供者和從中央集區發出 IP 位址的 VPN 通常距離實際使用用戶端裝置的位置非常遠。 基於上述的假設，根據追蹤、登錄資料、反向查詢和其他資訊，將 IP 位址轉換為實體位置的效果最佳。 

---

