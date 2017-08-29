---
title: "報告︰Azure AD SSPR | Microsoft Docs"
description: "報告 Azure AD 自助式密碼重設事件"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 4d7f05d626e6f718ca3597dff00715af4055f046
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017

---
# <a name="reporting-options-for-azure-ad-password-management"></a>Azure AD 密碼管理的報告選項

部署後，許多組織想要知道如何或是否真的使用 SSPR。 Azure AD 會提供報告功能，協助您使用現成的報告回答問題，而如果您已獲得適當授權，則可讓您建立自訂查詢。

存在於 [Azure 入口網站] (https://portal.azure.com/) 中的報告可以回答下列問題。

> [!NOTE]
> 您必須是[全域管理員](active-directory-assign-admin-roles.md#assign-or-remove-administrator-roles)且至少必須已瀏覽一次報告索引標籤或稽核記錄，以決定代表您的組織蒐集此資料。 在此之前，不會為您的組織收集資料。

* 有多少人已註冊密碼重設？
* 有誰已註冊密碼重設？
* 什麼資料是人員註冊？
* 有多少人在過去 7 天內重設密碼？
* 使用者或系統管理員用來重設其密碼最常見的方法是什麼？
* 當使用者或系統管理員嘗試使用密碼重設時所面臨的常見問題是什麼？
* 哪些系統管理員經常重設自己的密碼？
* 密碼重設時是否有任何可疑的活動？

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>如何在 Azure 入口網站中檢視密碼管理報告

從 Azure 入口網站體驗中，我們找到更好的方式來檢視密碼重設和密碼重設註冊活動。  請遵循下列步驟，尋找密碼重設和密碼重設註冊事件：

1. 瀏覽至 [**portal.azure.com**](https://portal.azure.com)
2. 在 Azure 入口網站的左側主要導覽中，按一下 [更多服務] 功能表
3. 在服務清單中搜尋並選取 **Azure Active Directory**
4. 從 Azure Active Directory 導覽功能表中，按一下 [使用者和群組]
5. 從 [使用者和群組] 導覽功能表中，按一下 [稽核記錄] 導覽項目。 這會顯示目錄中所有使用者發生的稽核事件。 您也可以篩選此檢視，查看密碼相關的所有事件。
6. 若要篩選此檢視而只顯示密碼重設相關事件，請按一下刀鋒視窗頂端的 [篩選] 按鈕。
7. 從 [篩選] 功能表中，選取 [類別] 下拉式清單中，然後變更為 [自助式密碼管理] 類別類型。
8. 另可選擇您有興趣的特定 [活動]，以進一步篩選清單

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>如何從 Azure AD 報告和事件 API 擷取密碼管理事件

Azure AD 報告和事件 API 支援擷取密碼重設和密碼重設註冊報告中包含的所有資訊。 您可以使用此 API 下載個別的密碼重設和密碼重設註冊事件，以便與您選擇的報告技術整合。

### <a name="how-to-get-started-with-the-reporting-api"></a>如何開始使用報告 API

若要存取此資料，您必須撰寫小型的應用程式或指令碼，以從我們的伺服器擷取資料。 [了解如何開始使用 Azure AD Reporting API](active-directory-reporting-api-getting-started.md)。

一旦您擁有有效的指令碼，您接下來要檢查您可以擷取密碼重設和註冊事件以滿足您的案例。

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)：列出密碼重設事件可用的資料行
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent)：列出密碼重設註冊事件可用的資料行

### <a name="reporting-api-data-retrieval-limitations"></a>報告 API 資料擷取限制

目前，Azure AD 報告和事件 API 會從**過去 30 天**內，擷取 [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) 和 [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) 類型最多 **75,000 個個別事件**。

如果您需要擷取或儲存比這個期間更早的資料，建議將資料保存在外部資料庫，並利用 API 來查詢產生的差異。 我們的建議是在組織中開始使用 SSPR 時，就開始擷取此資料，並保存在外部，然後從這一點開始持續追蹤差異。

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>如何使用 PowerShell 快速下載密碼重設註冊事件

除了直接使用 Azure AD 報告和事件 API，您也可以使用下列 PowerShell 指令碼，擷取目錄中最近的註冊事件。 如果您想要查看最近是誰註冊，或想要確保密碼重設如預期般展開，這非常有用。

* [Azure AD SSPR 註冊活動 PowerShell 指令碼](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

### <a name="description-of-report-columns-in-azure-portal"></a>Azure 入口網站中報告資料行的說明

下列清單詳細說明每個報告資料行：

* **使用者** – 嘗試進行密碼重設註冊作業的使用者。
* **角色** – 使用者在目錄中的角色。
* **日期和時間** – 嘗試的日期和時間。
* **已註冊資料** – 使用者在密碼重設註冊期間提供哪些驗證資料。

### <a name="description-of-report-values-in-azure-portal"></a>Azure 入口網站中報告值的說明

下表描述每個資料行允許的不同值：

| 欄 | 允許的值及其意義 |
| --- | --- |
| 已註冊資料 |**備用電子郵件** – 使用者用來驗證的備用電子郵件或驗證電子郵件<p><p>**辦公室電話** – 使用者用來驗證的辦公室電話<p>**行動電話** – 使用者用來驗證的行動電話或驗證電話<p>**安全性問題** – 使用者用來驗證的安全性問題<p>**屬於上述任何組合 (例如替代電子郵件 + 行動電話)** – 當指定 2 個閘道原則時會發生這種情況，且會顯示使用者用來驗證其密碼重設要求的兩種方法。 |

## <a name="view-password-reset-activity-in-the-classic-portal"></a>在傳統入口網站中檢視密碼重設活動

此報告會顯示您的組織中發生的所有密碼重設嘗試。

* **時間範圍上限**：30 天
* **資料列數目上限**：75,000
* **可下載**：是，透過 CSV 檔案

### <a name="description-of-report-columns-in-azure-classic-portal"></a>Azure 傳統入口網站中報告資料行的說明

下列清單詳細說明每個報告資料行：

1. **使用者** – 嘗試密碼重設作業的使用者 (根據使用者重設密碼時提供的 [使用者識別碼] 欄位)。
2. **角色** – 使用者在目錄中的角色。
3. **日期和時間** – 嘗試的日期和時間。
4. **使用的方法** – 使用者用於此重設作業的驗證方法。
5. **結果** – 密碼重設作業的結果。
6. **詳細資料** – 為什麼密碼重設會導致所產生的值的詳細資料。  也包含任何防範措施，您可能會採取以解決未預期的錯誤。

### <a name="description-of-report-values-in-azure-classic-portal"></a>Azure 傳統入口網站中報告值的說明

下表描述每個資料行允許的不同值：

| 欄 | 允許的值及其意義 |
| --- | --- |
| 使用方法 |**備用電子郵件** – 使用者用來驗證的備用電子郵件或驗證電子郵件<p>**辦公室電話** – 使用者用來驗證的辦公室電話<p>**行動電話** – 使用者用來驗證的行動電話或驗證電話<p>**安全性問題** – 使用者用來驗證的安全性問題<p>**屬於上述任何組合 (例如替代電子郵件 + 行動電話)** – 當指定 2 個閘道原則時會發生這種情況，且會顯示使用者用來驗證其密碼重設要求的兩種方法。 |
| 結果 |**已放棄** – 使用者開始重設密碼，但是又中途停止而未完成<p>**已封鎖** – 由於在 24 小時期間內嘗試使用密碼重設頁面或單一密碼重設閘道太多次，因此已防止使用者帳戶使用密碼重設<p>**已取消** – 使用者開始重設密碼，但是隨後按一下 [取消] 按鈕，在中途取消工作階段 <p>**已聯絡管理員** – 使用者在其工作階段期間有無法解決的問題，所以使用者按一下 [請連絡您的系統管理員] 連結，而不是完成密碼重設流程<p>**失敗** – 使用者無法重設密碼，可能是因為未設定讓使用者使用該功能 (例如，沒有授權、遺失驗證資訊、在內部部署環境管理密碼但已關閉回寫)。<p>**成功** – 密碼重設成功。 |
| 詳細資料 |請參閱下表 |

### <a name="allowed-values-for-details-column"></a>允許的詳細資料資料行的值

以下是使用密碼重設活動報告時，您預期的結果類型清單：

| 詳細資料 | 結果類型 |
| --- | --- |
| 在完成電子郵件驗證選項之後使用者已放棄 |Abandoned |
| 在完成行動 SMS 驗證選項之後使用者已放棄 |Abandoned |
| 在完成行動語音通話驗證選項之後使用者已放棄 |Abandoned |
| 在完成辦公室語音通話驗證選項之後使用者已放棄 |Abandoned |
| 在完成安全性問題選項之後使用者已放棄 |Abandoned |
| 在輸入其使用者識別碼之後使用者已放棄 |Abandoned |
| 在開始電子郵件驗證選項之後使用者已放棄 |Abandoned |
| 在開始行動 SMS 驗證選項之後使用者已放棄 |Abandoned |
| 在開始行動語音通話驗證選項之後使用者已放棄 |Abandoned |
| 在開始辦公室語音通話驗證選項之後使用者已放棄 |Abandoned |
| 在開始安全性問題選項之後使用者已放棄 |Abandoned |
| 選取新密碼之前使用者已放棄 |Abandoned |
| 選取新密碼之際使用者已放棄 |Abandoned |
| 使用者輸入太多無效的 SMS 驗證碼，因此封鎖 24 小時 |Blocked |
| 使用者嘗試行動電話語音驗證太多次，因此封鎖 24 小時 |Blocked |
| 使用者嘗試辦公室電話語音驗證太多次，因此封鎖 24 小時 |Blocked |
| 使用者嘗試回答安全性問題太多次，因此封鎖 24 小時 |Blocked |
| 使用者嘗試驗證電話號碼太多次，因此封鎖 24 小時 |Blocked |
| 在傳遞必要的驗證方法之前使用者已取消 |Canceled |
| 在提交新密碼之前使用者已取消 |Canceled |
| 在嘗試電子郵件驗證選項之後使用者連絡系統管理員 |Contacted admin |
| 在嘗試行動 SMS 驗證選項之後使用者連絡系統管理員 |Contacted admin |
| 在嘗試行動語音通話驗證選項之後使用者連絡系統管理員 |Contacted admin |
| 在嘗試辦公室語音通話驗證選項之後使用者連絡系統管理員 |Contacted admin |
| 在嘗試安全性問題驗證選項之後使用者連絡系統管理員 |Contacted admin |
| 這位使用者未啟用密碼重設。 在 [設定] 索引標籤底下啟用密碼重設以解決此問題 |Failed |
| 使用者沒有授權。 您可以新增授權給使用者以解決此問題 |Failed |
| 使用者嘗試從未啟用 cookie 的裝置重設 |Failed |
| 使用者的帳戶已定義驗證方法不足。 新增驗證資訊以解決此問題 |Failed |
| 使用者的密碼是在內部部署進行管理。 您可以啟用密碼回寫以解決此問題 |Failed |
| 我們無法取得您的內部部署密碼重設服務。 請檢查您的同步處理電腦的事件記錄檔 |Failed |
| 我們在重設使用者的內部部署密碼時發現問題。 請檢查您的同步處理電腦的事件記錄檔 |Failed |
| 此使用者不是密碼重設使用者群組的成員。 將此使用者加入至該群組中以解決此問題。 |Failed |
| 已針對此租用戶完全停用密碼重設。 若要解決這個問題，請參閱 [這裡](http://aka.ms/ssprtroubleshoot) 。 |Failed |
| 使用者成功重設密碼 |Succeeded |

## <a name="self-service-password-management-activity-types"></a>自助式密碼管理活動類型

下列活動類型會出現在 [自助式密碼管理] 稽核事件類別中。  以下每一項的說明。

* [**封鎖自助式密碼重設**](#activity-type-blocked-from-self-service-password-reset) - 指出使用者在 24 小時內嘗試重設密碼、使用特定的閘道或驗證電話號碼，總計 5 次以上。
* [**變更密碼 (自助式)**](#activity-type-change-password-self-service) - 指出使用者自願或被迫 (因為到期) 執行密碼變更。
* [**重設密碼 (由系統管理員)**](#activity-type-reset-password-by-admin) - 指出系統管理員從 Azure 入口網站代表使用者執行密碼重設。
* [**重設密碼 (自助式)**](#activity-type-reset-password-self-service) - 指出使用者已從 [Azure AD 密碼重設入口網站](https://passwordreset.microsoftonline.com)成功重設其密碼。
* [**自助式密碼重設流程活動進度**](#activity-type-self-serve-password-reset-flow-activity-progress) - 指出使用者在密碼重設過程中經過的每個特定步驟 (例如，通過特定的密碼重設驗證關卡)。
* [**解除鎖定使用者帳戶 (自助式)**](#activity-type-unlock-user-account-self-service) - 指出使用者已使用 AD 帳戶解除鎖定而不重設功能，成功解除鎖定其 Active Directory 帳戶，而沒有從 [Azure AD 密碼重設入口網站](https://passwordreset.microsoftonline.com)重設其密碼。
* [**使用者已註冊自助式密碼重設**](#activity-type-user-registered-for-self-service-password-reset) - 指出使用者已根據目前指定的租用戶密碼重設原則，註冊所有必要的資訊，以便能夠重設其密碼。

### <a name="activity-type-blocked-from-self-service-password-reset"></a>活動類型︰封鎖自助式密碼重設

下列清單詳細說明此活動︰

* **活動描述** - 指出使用者在 24 小時內嘗試重設密碼、使用特定的閘道或驗證電話號碼，總計 5 次以上。
* **活動執行者** - 已受節流控制而無法執行其他重設作業的使用者。 可能是使用者或系統管理員。
* **活動目標** - 已受節流控制而無法執行其他重設作業的使用者。 可能是使用者或系統管理員。
* **允許的活動狀態**
  * _成功_ - 指出使用者已受節流控制，在接下來 24 小時內無法執行任何額外的重設、嘗試任何額外的驗證方法，或驗證任何額外的電話號碼。
* **活動狀態失敗原因** - 不適用

### <a name="activity-type-change-password-self-service"></a>活動類型：變更密碼 (自助式)

下列清單詳細說明此活動︰

* **活動描述** – 指出使用者自願或被迫 (因為到期) 執行密碼變更。
* **活動執行者** - 已變更其密碼的使用者。 可能是使用者或系統管理員。
* **活動目標** - 已變更其密碼的使用者。 可能是使用者或系統管理員。
* **允許的活動狀態**
  * _成功_ - 指出使用者已成功變更其密碼
  * _失敗_ - 指出使用者變更其密碼失敗。 按一下此資料列可讓您查看 [活動狀態原因] 類別，以深入了解失敗發生的原因。
* **活動狀態失敗原因** - 
  * _FuzzyPolicyViolationInvalidPassword_ - 使用者選取的密碼自動被禁用，因為 Microsoft 的禁用密碼偵測功能發現此密碼太普通或太弱。

### <a name="activity-type-reset-password-by-admin"></a>活動類型：重設密碼 (由系統管理員)

下列清單詳細說明此活動︰

* **活動描述** - 指出系統管理員從 Azure 入口網站代表使用者執行密碼重設。
* **活動執行者** - 代表另一個使用者或系統管理員來執行密碼重設的系統管理員。 必須是全域管理員、密碼管理員、使用者管理員或技術服務管理員。
* **活動目標** - 已重設密碼的使用者。 可能是使用者或不同的系統管理員。
* **允許的活動狀態**
  * _成功_ - 指出系統管理員已成功重設使用者的密碼
  * _失敗_ - 指出系統管理員變更使用者的密碼失敗。 按一下此資料列可讓您查看 [活動狀態原因] 類別，以深入了解失敗發生的原因。

### <a name="activity-type-reset-password-self-service"></a>活動類型：重設密碼 (自助式)

下列清單詳細說明此活動︰

* 活動描述 - 指出使用者已從 [Azure AD 密碼重設入口網站](https://passwordreset.microsoftonline.com)成功重設其密碼。
* **活動執行者** - 已重設其密碼的使用者。 可能是使用者或系統管理員。
* **活動目標** - 已重設其密碼的使用者。 可能是使用者或系統管理員。
* **允許的活動狀態**
  * _成功_ - 指出使用者已成功重設其密碼
  * _失敗_ - 指出使用者重設其密碼失敗。 按一下此資料列可讓您查看 [活動狀態原因] 類別，以深入了解失敗發生的原因。
* **活動狀態失敗原因** -
  * _FuzzyPolicyViolationInvalidPassword_ - 系統管理員選取的密碼自動被禁用，因為 Microsoft 的禁用密碼偵測功能發現此密碼太普通或太弱。

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>活動類型：自助式密碼重設流程活動進度

下列清單詳細說明此活動︰

* **活動描述** - 指出使用者在密碼重設過程中經過的每個特定步驟 (例如，通過特定的密碼重設驗證關卡)。
* **活動執行者** - 執行部分密碼重設流程的使用者。 可能是使用者或系統管理員。
* **活動目標** - 執行部分密碼重設流程的使用者。 可能是使用者或系統管理員。
* **允許的活動狀態**
  * _成功_ - 指出使用者已成功完成密碼重設流程的特定步驟。
  * _失敗_ - 指出密碼重設流程的特定步驟失敗。 按一下此資料列可讓您查看 [活動狀態原因] 類別，以深入了解失敗發生的原因。
* **允許的活動狀態原因**
  * 請參閱下表中的[所有允許的重設活動狀態原因](#allowed-values-for-details-column)

### <a name="activity-type-unlock-user-account-self-service"></a>活動類型：解除鎖定使用者帳戶 (自助式)

下列清單詳細說明此活動︰

* 活動描述 - 指出使用者已使用 AD 帳戶解除鎖定而不重設功能，成功解除鎖定其 Active Directory 帳戶，而沒有從 [Azure AD 密碼重設入口網站](https://passwordreset.microsoftonline.com)重設其密碼。
* **活動執行者** - 已解除鎖定帳戶但未重設其密碼的使用者。 可能是使用者或系統管理員。
* **活動目標** - 已解除鎖定帳戶但未重設其密碼的使用者。 可能是使用者或系統管理員。
* **允許的活動狀態**
  * _成功_ - 指出使用者已成功解除鎖定其帳戶
  * _失敗_ - 指出使用者解除鎖定帳戶失敗。 按一下此資料列可讓您查看 [活動狀態原因] 類別，以深入了解失敗發生的原因。

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>活動類型︰使用者已註冊自助式密碼重設

下列清單詳細說明此活動︰

* **活動描述** - 指出使用者已根據目前指定的租用戶密碼重設原則，註冊所有必要的資訊，以便能夠重設其密碼。 
* **活動執行者** - 已註冊密碼重設的使用者。 可能是使用者或系統管理員。
* **活動目標** - 已註冊密碼重設的使用者。 可能是使用者或系統管理員。
* **允許的活動狀態**
  * _成功_ - 指出使用者已根據目前的原則成功註冊密碼重設。 
  * _失敗_ - 指出使用者註冊密碼重設失敗。 按一下此資料列可讓您查看 [活動狀態原因] 類別，以深入了解失敗發生的原因。 注意 - 這不表示使用者無法重設自己的密碼，只表示他們未完成註冊程序。 如果帳戶上有未驗證但正確的資料 (例如未驗證的電話號碼)，即使此電話號碼尚未驗證，仍然可用來重設密碼。 如需詳細資訊，請參閱[當使用者註冊時會發生什麼事？](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)

## <a name="next-steps"></a>後續步驟

下列連結提供有關使用 Azure AD 重設密碼的其他資訊

* [使用者管理稽核記錄的捷徑](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Audit) - 直接移至您租用戶的使用者管理稽核記錄
* [**快速入門**](active-directory-passwords-getting-started.md) - 開始執行 Azure AD 自助式密碼管理 
* [**授權**](active-directory-passwords-licensing.md) - 設定 Azure AD 授權
* [**資料**](active-directory-passwords-data.md) -了解所需的資料以及如何將它使用於密碼管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此處提供的指引來規劃 SSPR 並部署給使用者
* [**自訂**](active-directory-passwords-customize.md) - 為您的公司自訂 SSPR 體驗的外觀與風格。
* [**技術性深入探討**](active-directory-passwords-how-it-works.md) - 深入探索以了解其運作方式
* [**常見問題集**](active-directory-passwords-faq.md) - 如何？ 原因為何？ 何事？ 何地？ 何人？ 何時？ - 您一直想要詢問之問題的答案
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何解決我們看到的 SSPR 常見問題
* [**原則**](active-directory-passwords-policy.md) - 了解並設定 Azure AD 密碼原則

