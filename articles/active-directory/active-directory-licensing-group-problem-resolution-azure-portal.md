---
title: "解決 Azure Active Directory 中群組的授權問題 | Microsoft Docs"
description: "當您使用 Azure Active Directory 以群組為基礎的授權時，如何識別及解決授權指派問題"
services: active-directory
keywords: "Azure AD 授權"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 955efc9e6b209195935d1f7c13f96c6a42536b2a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/27/2017

---

# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>識別及解決 Azure Active Directory 中群組的授權指派問題

Azure Active Directory (Azure AD) 中以群組為基礎的授權會介紹使用者授權錯誤狀態的概念。 在本文章中，我們會說明使用者最後都處於此狀態的原因。

當您將授權直接指派給個別使用者，而不使用以群組為基礎的授權時，指派作業會失敗。 例如，當您對使用者執行 PowerShell Cmdlet `Set-MsolUserLicense` 時，此 Cmdlet 可能會因為一些商務邏輯相關原因而失敗。 例如，可能是授權數量不足，或無法同時指派兩個服務方案的衝突。 系統會立即向您回報此問題。

當您使用以群組為基礎的授權時，可能會發生相同錯誤，但是當 Azure AD 服務指派授權時會在背景中發生。 基於這個原因，無法立即向您通知這些錯誤。 而是會記錄在使用者物件上，然後透過系統管理入口網站報告。 請注意，授權使用者的原始目的永遠不會遺失，但是會針對未來的調查和解決記錄於錯誤狀態。

## <a name="how-to-find-license-assignment-errors"></a>如何找出授權指派錯誤

1. 若要在特定群組中尋找處於錯誤狀態的使用者，請開啟該群組的刀鋒視窗。 [授權] 底下會有一份通知，顯示是否有任何使用者處於錯誤狀態。

![群組，錯誤通知](media/active-directory-licensing-group-problem-resolution-azure-portal/group-error-notification.png)

2. 按一下這份通知，以開啟所有受影響的使用者清單。 您可以分別按一下每個使用者以查看詳細資料。

![群組，處於錯誤狀態的使用者清單](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-users-with-errors.png)

3. 若要尋找包含至少一個錯誤的所有群組，在 [Azure Active Directory] 刀鋒視窗上選取 [授權]，然後選取 [概觀]。 某些群組需要您注意時，會顯示資訊方塊。

![概觀，處於錯誤狀態的群組相關資訊](media/active-directory-licensing-group-problem-resolution-azure-portal/group-errors-widget.png)

4. 按一下方塊以查看具有錯誤的所有群組清單。 您可以按一下每個群組以取得詳細資訊。

![概觀，具有錯誤的群組清單](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-groups-with-errors.png)


以下是每個潛在問題及其解決方法的說明。

## <a name="not-enough-licenses"></a>沒有足夠的授權

**問題：**群組中指定的其中一項產品沒有足夠的可用授權。 您需要為產品購買更多授權，或從其他使用者或群組釋放未使用的授權。

若要查看有多少授權可用，請移至 [Azure Active Directory] > [授權] > [所有產品]。

若要查看哪些使用者及群組在取用授權，請按一下某項產品。 在 [已授權的使用者] 底下，您會看到其授權已直接指派或透過一或多個群組指派的所有使用者。 在 [已授權的群組] 底下，您會看到已被指派該產品的所有群組。

**PowerShell：**PowerShell Cmdlet 會將此錯誤報告為 _CountViolation_。

## <a name="conflicting-service-plans"></a>衝突的服務方案

**問題：**群組中指定的其中一個產品包含服務方案，與已透過不同產品指派給使用者的另一個服務方案相衝突。 某些服務方案設定的方式不同，所以無法將它們指派給另一個相關服務方案的相同使用者。

請思考一下下列範例。 使用者擁有直接指派的 Office 365 企業版 **E1** 授權，所有方案皆啟用。 使用者已新增至獲得 Office 365 企業版 **E3** 產品指派的群組。 本產品包含的服務方案不能與 E1 包含的方案重疊，因此群組授權指派將會失敗，且具有「衝突的服務方案」錯誤。 在此範例中，衝突的服務方案是︰

-   SharePoint Online (方案 2) 與 SharePoint Online (方案 1) 衝突。
-   Exchange Online (方案 2) 與 Exchange Online (方案 1) 衝突。

若要解決此衝突，您必須停用這兩個方案，不是在直接指派給使用者的 E1 授權上停用。 就是，必須在 E3 授權中修改整個群組授權指派並停用方案。 或者，如果在 E3 授權內容中是多餘的，您可能會決定從使用者移除 E1 授權。

如何解決產品授權的衝突一律屬於系統管理員的決策。 Azure AD 不會自動解決授權衝突。

**PowerShell：**PowerShell Cmdlet 會將此錯誤報告為 _MutuallyExclusiveViolation_。

## <a name="other-products-depend-on-this-license"></a>其他產品相依於此授權

**問題：**群組中指定的其中一個產品包含服務方案，必須在另一個產品中針對另一個服務方案啟用，才能夠運作。 當 Azure AD 嘗試移除基礎服務方案時會發生此錯誤。 例如，因為使用者從群組中移除而造成此問題。

若要解決這個問題，您必須確定所需的方案仍透過其他方法指派給使用者，或已停用這些使用者的相依服務。 之後，您可以適當地移除這些使用者的群組授權。

**PowerShell：**PowerShell Cmdlet 會將此錯誤報告為 _DependencyViolation_。

## <a name="usage-location-isnt-allowed"></a>不允許使用位置

**問題：**由於當地法律和法規，無法在所有位置使用某些 Microsoft 服務。 您必須為使用者指定「使用位置」屬性，才可以將授權指派給使用者。 您可以在 Azure 入口網站中 [使用者] > [設定檔] > [設定] 區段之下執行此作業。

當 Azure AD 嘗試將群組授權指派給不支援其使用位置的使用者時，將會發生失敗並將此錯誤記錄於該使用者。

若要解決這個問題，請從授權群組不支援的位置中移除使用者。 或者，如果目前的使用位置值不代表實際使用者的位置，您可以進行修改，以便下一次正確指派授權 (如果支援新的位置)。

**PowerShell：**PowerShell Cmdlet 會將此錯誤報告為 _ProhibitedInUsageLocationViolation_。

> [!NOTE]
> 當 Azure AD 指派群組授權時，未指定使用位置的任何使用者會繼承目錄的位置。 我們建議系統管理員先為使用者設定正確的使用位置值，再使用以群組為基礎的授權，以符合當地法規。

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>當群組中有一個以上的產品授權時，會發生什麼事？

您可以將一個以上的產品授權指派給群組。 例如，您可以將 Office 365 企業版 E3 和 Enterprise Mobility + Security 指派給群組，以便輕鬆地為使用者啟用所有已納入的服務。

Azure AD 會嘗試將群組中指定的所有授權指派給每位使用者。 如果 Azure AD 因為商務邏輯問題而無法指派其中一個產品 (例如，沒有足夠授權供所有人使用，或與使用者已啟用的其他服務衝突)，我們也無法在群組中指派其他授權。

您可以查看哪些使用者的指派失敗，以及檢查哪些產品受到影響。

## <a name="how-to-manage-licenses-for-products-with-prerequisites"></a>如何使用必要條件管理產品的授權？

您可能擁有的部分 Microsoft Online 產品是「附加元件」，也就是說，它們需要使用者或群組啟用必要條件服務方案，才能獲得指派。 系統會透過群組型授權，要求必要條件和附加元件服務方案存在相同的群組上。 這是為了確保新增至群組的所有使用者都可以收到完整有效的產品。 讓我們思考一下以下的範例：

「Microsoft 工作場所分析」是一個附加元件產品。 它包含具有相同名稱的單一服務方案。 只有在同時指派下列其中一個必要條件時，才能將此服務方案指派給使用者或群組：
- *Exchange Online (方案 1)*
- 或 *Exchange Online (方案 2)*

如果我們嘗試將此產品本身指派給群組，入口網站將會傳回一個錯誤。按一下該錯誤通知可顯示下列詳細資料：

![群組，缺少必要條件](media/active-directory-licensing-group-problem-resolution-azure-portal/group-prerequisite-required.png)

按一下詳細資料可顯示下列錯誤訊息：

*授權作業失敗。請先確定群組具備必要服務，再新增或移除相依的服務。**服務 Microsoft 工作地點分析需要一併啟用 Exchange Online (方案 2)。***

若要將此附加元件授權指派給群組，我們必須確定群組中也包含必要條件服務方案。 例如，我們可以更新已經包含完整 *Office 365 E3* 產品的現有群組，並在其中新增附加元件產品。

您也可以建立僅包含產品最低需求的獨立群組，讓附加元件得以運作，也就是說，此群組可以用於僅授權附加元件產品的所選使用者。 在此範例中，我們已經將下列產品指派給相同的群組：
- *Office 365 企業版 E3*，僅啟用 *Exchange Online (方案 2)* 服務方案
- *Microsoft 工作地點分析*

![群組，包含必要條件](media/active-directory-licensing-group-problem-resolution-azure-portal/group-addon-with-prerequisite.png)

從現在起，新增至此群組的任何使用者都將耗用 E3 產品的 1 個授權，以及工作場所分析產品的 1 個授權。 同時，這些使用者可以是提供他們完整 E3 產品的另一個群組的成員，而且他們仍將僅耗用該產品的 1 個授權。

> [!TIP]
> 您可以針對每個必要條件服務方案，建立多個群組。 例如，如果您同時為使用者使用 *Office 365 企業版 **E1*** 和 *Office 365 企業版 **E3***，則可以建立兩個群組來授權 *Microsoft 工作場所分析*，亦即，一個使用 E1 作為必要條件，另一個使用 E3 作為必要條件。 這可讓您將附加元件散發給 E1 和 E3 的使用者，而不需要耗用額外的授權。

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>使用者的授權指派會因為 Exchange Online 中 Proxy 位址重複而無訊息的失敗

如果您是使用 Exchange Online，租用戶中有些使用者可能會使用相同的 Proxy 位址值導致設定錯誤。 當群組型授權嘗試將授權指派給此類使用者時，它將會失敗且不會記錄錯誤 (不同於上述的其他錯誤情況) - 這是此功能預覽版本的限制，我們會在*上市*之前解決。

> [!TIP]
> 如果您發現某些使用者未收到授權，且未對這些使用者記錄錯誤，請先檢查他們是否有重複的 Proxy 位址。
> 可以藉由針對 Exchange Online 執行下列 PowerShell Cmdlet 來完成這項操作：
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> [這篇文章](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online)包含更多關於此問題的詳細資料，包括[如何使用遠端 PowerShell 連線至 Exchange Online](https://technet.microsoft.com/library/jj984289.aspx)。

為受影響的使用者解決 Proxy 位址問題之後，請確定在群組上強制執行授權處理，以確保現在可以再次套用授權。

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>如何強制處理群組中的授權來解決錯誤？

根據您為了解決錯誤所採取的步驟，可能需要手動觸發群組的處理以更新使用者狀態。

例如，如果您藉由移除使用者的直接授權指派來釋出一些授權，您必須觸發先前失敗之群組的處理，才能完整授權所有使用者成員。 若要這樣做，請尋找 [群組] 刀鋒視窗，開啟 [授權]，然後選取工具列中的 [重新處理] 按鈕。

## <a name="next-steps"></a>後續步驟

若要深入了解透過群組管理授權的其他案例，請閱讀下列各項：

* [將授權指派給 Azure Active Directory 中的群組](active-directory-licensing-group-assignment-azure-portal.md)
* [什麼是 Azure Active Directory 中以群組為基礎的授權？](active-directory-licensing-whatis-azure-portal.md)
* [如何將個別授權使用者移轉至 Azure Active Directory 中以群組為基礎的授權](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 群組型授權其他案例 (英文)](active-directory-licensing-group-advanced.md)

