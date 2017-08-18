---
title: "避免意外的成本、管理計費 - Azure | Microsoft Docs"
description: "了解如何避免 Azure 帳單上的意外費用。 使用 Microsoft Azure 訂用帳戶的成本追蹤與管理功能。"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/10/2017
ms.author: tonguyen
experimental_id: a2b2579c-cd2e-41
ms.translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 539a3e628dfec1088f683652bc9b743b87875d36
ms.contentlocale: zh-tw
ms.lasthandoff: 04/26/2017

---

# <a name="prevent-unexpected-costs-with-azure-billing-and-cost-management"></a>使用 Azure 計費與成本管理避免非預期的成本

當您註冊 Azure 時，為了深入了解您的支出，有幾件事您可以做。 在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中，選取訂用帳戶之後，您可以看到目前的成本細分和完工速率。 您也可以[下載之前的發票與詳細使用量檔案](billing-download-azure-invoice-daily-usage-date.md)。 如果您想要將不同專案或小組所使用的資源成本分組，請查看[資源標記](../azure-resource-manager/resource-group-using-tags.md)。 如果您的組織有您偏好使用的報告系統，請查看[計費 API](billing-usage-rate-card-overview.md)。 

如需每日使用量的詳細資訊，請參閱[了解 Microsoft Azure 帳單](billing-understand-your-bill.md)。

如果您的訂用帳戶是透過 Enterprise 合約 (EA)、雲端解決方案提供者 (CSP) 或 Azure 贊助取得，那麼此文章中所述的許多功能並不適用於您。 反之，我們有不同的工具組，可供您用於成本管理。 請參閱[適用於 EA、CSP 和贊助的其他資源](#other-offers)。

如果您的訂用帳戶是免費試用、[Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)、Azure in Open (AIO) 或 BizSpark，請了解[消費限制](#spending-limit)以避免您的訂用帳戶非預期地被停用。 

## <a name="day-0-before-you-add-azure-services"></a>第 0 天︰新增 Azure 服務之前

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>使用價格計算機在線上評估成本

查看[價格計算機](https://azure.microsoft.com/pricing/calculator/)和[擁有權總成本計算機 (英文)](https://aka.ms/azure-tco-calculator) 即可取得您感興趣的服務每月成本評估。 例如，如果您讓 A1 Windows 虛擬機器 (VM) 持續運作，則運算時間的預估成本為每月 $66.96 美元：

![價格計算機的螢幕擷取畫面，顯示 A1 Windows 虛擬機器的每月預估成本為 $66.96 美元](./media/billing-getting-started/pricing-calcVM.png)

如需詳細資訊，請參閱[ 購買常見問題集](https://azure.microsoft.com/pricing/faq/)。 或者，如果您想要與服務人員聯絡，請撥打 1-800-867-1389。

### <a name="check-your-subscription-and-access"></a>檢查訂用帳戶和存取權

檢視成本需要[訂用帳戶層級的計費資訊存取權](billing-manage-access.md)，但只有帳戶管理員可以存取[帳戶中心](https://account.windowsazure.com/Home/Index)、變更計費資訊，以及管理訂用帳戶。 帳戶管理員是完成註冊程序的人員。 如需詳細資訊，請參閱[新增或變更管理訂用帳戶或服務的 Azure 系統管理員角色](billing-add-change-azure-subscription-administrator.md)。

若要查看您是否為帳戶管理員，請前往 [Azure 入口網站中的訂用帳戶刀鋒視窗](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)並查看您具有存取權的訂用帳戶清單。 查看 [我的角色] 底下。 如果它顯示*帳戶管理員*，那麼您就是帳戶管理員。 如果它顯示諸如*擁有者*之類的其他角色，則表示您沒有完整權限。

![Azure 入口網站的訂用帳戶檢視中您的角色的螢幕擷取畫面](./media/billing-getting-started/sub-blade-view.PNG)

如果您不是帳戶管理員，則表示有人可能透過 [Azure Active Directory 角色型存取控制](../active-directory/role-based-access-control-configure.md) (RBAC) 授與您部分存取權。 若要管理訂用帳戶及變更帳單資訊，請[找到帳戶管理員](billing-subscription-transfer.md#whoisaa)，並要求他們執行工作或[將訂用帳戶轉移給您](billing-subscription-transfer.md)。

如果您的帳戶系統管理員已不在您組織，而您需要管理帳單，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 

### <a name="spending-limit"></a> 檢查您是否開啟消費限制

如果您有使用信用額度的訂用帳戶，那麼您的消費限制依預設是開啟的。 如此一來，當您花光您的信用額度時，就不會針對您的信用卡收費。 請參閱 [Azure 優惠完整清單及消費限制可用性](https://azure.microsoft.com/support/legal/offer-details/)。

不過，如果您達到消費限制，就會停用您的服務。 這表示您的 VM 會被解除配置。 若要避免服務停機，您必須關閉消費限制。 任何超額部分都會用您在檔案上的信用卡收費。 

若要查看您是否已開啟消費限制，請移至[帳戶中心的訂用帳戶檢視](https://account.windowsazure.com/Subscriptions)。 如果您的消費限制是開啟的，則會出現橫幅︰

![顯示帳戶中心已開啟消費限制的警告的螢幕擷取畫面](./media/billing-getting-started/spending-limit-banner.PNG)

按一下橫幅並遵循提示，即可移除消費限制。 如果您在註冊時未輸入信用卡資訊，則必須輸入信用卡資訊，才能移除消費限制。 如需詳細資訊，請參閱 [Azure 消費限制 - 運作方式以及啟用或移除方法](https://azure.microsoft.com/pricing/spending-limits/)。

### <a name="set-up-billing-alerts"></a>設定帳務警示

設定帳務警示，以便在使用成本超過您指定的金額時，收到電子郵件通知。 如果您有每月信用額度，請依照您設定指定金額時的額度設定警示。 如需詳細資訊，請參閱[為您的 Microsoft Azure 訂用帳戶設定帳單通知](billing-set-up-alerts.md)。

![帳務警示電子郵件的螢幕擷取畫面](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> 這項功能是仍處於預覽狀態，因此您應該定期檢查您的使用量。

您可能需要使用價格計算機的成本估計當作您第一個警示的指導方針。

### <a name="understand-limits-and-quotas-for-your-subscription"></a>了解您訂用帳戶的限制和配額

每個訂用帳戶都有針對各種項目 (例如 CPU 核心數目和 IP 位址) 的預設限制。 務必留意這些限制。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../azure-subscription-service-limits.md)。 您可以 [連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，要求提高您的限制或配額。

## <a name="day-1-as-you-add-services"></a>第 1 天：當您新增服務時

### <a name="review-the-estimated-cost-in-the-portal"></a>在入口網站中檢閱預估成本

一般而言，當您在 Azure 入口網站中新增服務時，會有一個檢視向您顯示類似的每月預估成本。 例如，當您選擇 Windows VM 的大小時，會看到計算時數的預估每月成本：

![範例︰A1 Windows VM 預估每月成本為 $66.96 美元](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="tags"></a> 將標記新增至您的資源以便將計費資料分組

針對支援的服務，您可以使用標記來將您的計費資料分組。 例如，如果您有不同小組執行數個 VM，您可以使用標記，依照成本中心 (HR、行銷、財務) 或環境 (生產環境、預先生產環境、測試) 來將成本分類。 

![顯示在入口網站中設定標籤的螢幕擷取畫面](./media/billing-getting-started/tags.PNG)

這些標記會顯示在不同的成本報告檢視中。 例如，它們會立即顯示您的[成本分析檢視](#costs)，並在第一次計費期間之後，顯示[詳細使用量.csv](#invoice-and-usage)。

如需詳細資訊，請參閱 [使用標記組織您的 Azure 資源](../azure-resource-manager/resource-group-using-tags.md)。

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>請考慮啟用成本削減功能，例如 VM 自動關機

根據您的案例，您可以在 Azure 入口網站中設定 VM 自動關機。 如需詳細資訊，請參閱[使用 Azure Resource Manager 的 VM 自動關機 (英文)](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)。

![在入口網站的自動關機選項的螢幕擷取畫面](./media/billing-getting-started/auto-shutdown.PNG)

自動關機與您在 VM 中使用電源選項關閉不一樣。 自動關機會停止並解除配置您的 VM，以便停止額外的使用費用。 如需詳細資訊，請參閱 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 的定價常見問題，以及 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)有關 VM 的狀態。

如需更多針對開發和測試環境的成本削減功能，請參閱 [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/)。

## <a name="cost-reporting"></a> 第 2 天以後：使用服務之後，檢視使用量

### <a name="costs"></a>定期查看入口網站以了解成本細分和完工速率

在您的服務開始執行之後，請定期檢查它們花了您多少錢。 您可以在 Azure 入口網站中看到目前的花費和完工速率。 

1. 請瀏覽 [Azure 入口網站中的訂用帳戶刀鋒視窗](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。

2. 選取您要檢視的訂用帳戶。 您可能只有一個訂用帳戶可選取。

3. 您應該會在快顯刀鋒視窗中看到成本細分和完工速率。 您的優惠可能不受支援 (靠近頂端處會顯示警告)。 新增服務之後，請等候 24 小時以便資料填入。
    
    ![Azure 入口網站中完工速率和成本細分的螢幕擷取畫面](./media/billing-getting-started/burn-rate.PNG)

4. 您可以將檢視釘選到您的儀表板。

    ![將檢視釘選到儀表板的螢幕擷取畫面](./media/billing-getting-started/pin.PNG)

5. 按一下左側清單中的 [成本分析]，可依照資源檢視成本細分。

    ![Azure 入口網站中成本分析檢視的螢幕擷取畫面](./media/billing-getting-started/cost-analysis.PNG)

6. 您可以依照不同屬性來篩選，例如[標記](#tags)資源群組和時間範圍。 按一下 [套用] 確認篩選器，按一下 [下載] 可將檢視匯出至逗號分隔值 (.csv) 檔案。

7. 按一下資源可查看消費歷程記錄，以及它每天花您多少錢。

    ![Azure 入口網站中消費歷程記錄檢視的螢幕擷取畫面](./media/billing-getting-started/costhistory.PNG)

我們建議您將您看到的成本與您在選取服務時看到的預估進行核對。 如果成本和預估的差異很大，請再次檢查您為您的資源選取的定價方案 (例如 A1 與 A0 VM)。 

#### <a name="view-costs-for-all-your-subscriptions-in-the-billing-blade"></a>在帳單刀鋒視窗中檢視所有訂用帳戶的成本

如果您以帳戶系統管理員身分管理多個訂用帳戶，您可以在[帳單刀鋒視窗](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade)中看到您所有訂用帳戶的彙總帳單金額和細分。 

<!-- Add screenshots of multiple subs each with billed usage -->

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>開啟並查看 Azure 建議程式的建議

[Azure 建議程式](../advisor/advisor-overview.md)是一項預覽功能，可協助您藉由找出低使用率的資源，來降低成本。 在 Azure 入口網站中將它開啟：

![Azure 入口網站中 Azure 建議程式按鈕的螢幕擷取畫面](./media/billing-getting-started/advisor-button.PNG)

然後，您可以從 [建議程式] 儀表板中的 [成本] 索引標籤取得可操作的建議：

![建議程式的成本建議範例的螢幕擷取畫面](./media/billing-getting-started/advisor-action.PNG)

如需詳細資訊，請參閱[建議程式成本建議](../advisor/advisor-cost-recommendations.md)。

### <a name="invoice-and-usage"></a>在您第一次計費期間之後取發票和詳細使用量

在第一次計費期間之後，您可以下載您的可攜式文件格式 (.pdf) 發票和逗號分隔值 (.csv) 使用量詳細資料。 您也可以選擇加入以便透過電子郵件收取發票。 這些檔案可協助了解您在稅金、折扣和信用額度之後的最終計費是多少。 如果您的訂用帳戶沒有連結付款方法，則您可能無法使用這些檔案。 如需詳細資訊，請參閱[如何取得您的 Azure 帳單寄送發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)和[了解 Microsoft Azure 帳單](billing-understand-your-bill.md)。

![.pdf 發票的螢幕擷取畫面](./media/billing-getting-started/invoice.png)

您先前設定的標記會顯示在詳細使用量 .csv 檔案中︰

![在使用量 .csv 中顯示標記的螢幕擷取畫面](./media/billing-getting-started/csv.png)

### <a name="billing-api"></a>計費 API

使用我們的計費 API 可以用程式設計方式取得使用量資料。 RateCard API 與使用情況 API 一起使用即可取得您的計費使用量。 如需詳細資訊，請參閱[深入瞭解 Microsoft Azure 資源耗用量](billing-usage-rate-card-overview.md)。

## <a name="other-offers"></a> 適用於 EA、CSP 和贊助的其他資源

若要開始使用，請連絡帳戶管理員或 Azure 。

| 提供項目 | 資源 |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise 合約 (EA) | [EA 入口網站](https://ea.azure.com/)、[協助文件](https://ea.azure.com/helpdocs)和 [Power BI 報告](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| 雲端解決方案提供者 (CSP) | 與您的提供者討論 |
| Azure 贊助 | [贊助入口網站 (英文)](https://www.microsoftazuresponsorships.com/) |

如果您管理的是大型組織的 IT，我們建議您閱讀 [Azure 企業 Scaffold](../azure-resource-manager/resource-manager-subscription-governance.md) 和[企業 IT 技術白皮書](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf 下載，僅提供英文版)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

