---
title: "Azure MFA 的存取及使用報告 | Microsoft Docs"
description: "說明如何使用 Azure Multi-Factor Authentication 功能 - 報告。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 42a87adef740cc2c1d77c9f02eef8aaa5f207258
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 中的報告
Azure Multi-Factor Authentication 提供數個供您和貴組織使用的報告。 這些報告可透過 Multi-Factor Authentication 管理入口網站來存取。 以下是可用報告的清單：

| 報告 | 說明 |
|:--- |:--- |
| 使用量 |使用量報告顯示有關整體使用量、使用者摘要和使用者詳細資料等資訊。 |
| 伺服器狀態 |此報告會顯示與帳戶相關聯之 Multi-Factor Authentication Server 的狀態。 |
| 已封鎖的使用者歷程記錄 |這些報告會顯示要求封鎖或解除封鎖使用者之申請的歷程記錄。 |
| 已略過的使用者歷程記錄 |顯示要求略過使用者電話號碼之 Multi-Factor Authentication 的申請歷程記錄。 |
| 詐騙警示 |顯示在指定日期範圍內提交之詐騙警示的歷程記錄。 |
| 已排入佇列 |列出已排入佇列並等候處理的報告和其狀態。 當報告完成時，系統會提供下載或檢視報告的連結。 |

## <a name="view-reports"></a>檢視報告
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 在左側選取 [Active Directory]。
3. 根據您是否使用驗證提供者來遵循下列兩個選項的其中一個︰
   * **選項 1**：按一下 [多因素驗證提供者] 索引標籤。選取您的 MFA 提供者，然後按一下底部的 [管理] 按鈕。
   * **選項 2**：選取您的目錄，然後移至 [設定] 索引標籤。在 [Multi-Factor Authentication] 區段底下，選取 [管理服務設定] 。 在 [MFA 服務設定] 頁面底部，按一下 [移至入口網站] 連結。
4. 在 Azure Multi-Factor Authentication 管理入口網站的左側導覽中，從 [檢視報告] 區段中選取您要使用的報告類型。

<center>![雲端](./media/multi-factor-authentication-manage-reports/report.png)</center>


**其他資源**

* [適用於使用者](end-user/multi-factor-authentication-end-user.md)
* [MSDN 上的 Azure Multi-Factor Authentication](https://msdn.microsoft.com/library/azure/dn249471.aspx)

