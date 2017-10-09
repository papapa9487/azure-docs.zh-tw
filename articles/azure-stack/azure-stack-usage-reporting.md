---
title: "向 Azure 回報 Azure Stack 使用情況資料 | Microsoft Docs"
description: "了解如何在 Azure Stack 中設定使用情況資料報告。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: sngun;AlfredoPizzirani
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 695b93a818d3c0e615bb79e0a2861e134b2f5c89
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="report-azure-stack-usage-data-to-azure"></a>向 Azure 回報 Azure Stack 使用情況資料 

使用量資料 (也稱為取用資料) 代表所使用的資源量。 使用取用型計費模型的 Azure Stack 整合系統應該向 Azure 回報使用量資料。 此使用量資料是供計費用途使用。 Azure Stack 操作員需負責為其 Azure Stack 環境設定使用量資料報告功能。


> [!NOTE]
> 對採用依使用量付費授權模型的 Azure Stack 整合系統使用者而言，使用量資料回報是必要功能。 不過，對採用容量授權模型的客戶而言，則為選用功能。 就「Azure Stack 開發套件」而言，雲端操作員可以回報使用量資料並測試此功能，不過，使用者並無須支付操作員所引發的任何使用量費用。 若要深入了解 Azure Stack 中的定價，請參閱[如何購買 Azure Stack](https://azure.microsoft.com/overview/azure-stack/how-to-buy/) 頁面。

## <a name="usage-data-reporting-flow"></a>使用量資料回報流程

使用情況資料是從 Azure Stack 透過 Azure Bridge 傳送到 Azure。 在 Azure 中，商務系統會處理使用情況資料並產生帳單。 產生帳單之後，Azure 訂用帳戶擁有者可以從 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)檢視並下載帳單。 若要了解 Azure Stack 授權，請參閱 [Azure Stack 包裝與定價文件](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409) \(英文\)。 

下圖以圖形方式呈現 Azure Stack 中的使用量資料流程：

![計費流程](media/azure-stack-usage-reporting/billing-flow.png)

## <a name="set-up-usage-data-reporting"></a>設定使用情況資料報告

若要在 Azure Stack 中設定使用情況資料報告，您必須[向 Azure 註冊您的 Azure Stack 執行個體](azure-stack-register.md)。 在註冊程序中，會設定 Azure Stack 的 Azure Bridge 元件以將 Azure Stack 連線到 Azure。 設定好 Azure Bridge 之後，它會將下列使用量資料傳送給 Azure： 

* **計量識別碼**：所取用之資源的唯一識別碼。
* **數量** – 資源使用量資料的總數。
* **位置**：目前 Azure Stack 資源部署所在位置。
* **資源 URI**：將回報其使用狀況之資源的完整 URI。 
* **訂用帳戶 ID**：Azure Stack 使用者的訂用帳戶 ID。
* **時間**：使用情況資料的開始與結束時間。 在 Azure Stack 中取用這些資源與向商務系統回報使用量資料的時間之間，有一些延遲。 Azure Stack 每隔 24 小時會彙總一次使用情況資料，而向 Azure 中的商務管線回報使用情況資料則需要數小時。 因此，在午夜前短暫發生的使用情況可能會在隔天顯示在 Azure 中。

## <a name="test-usage-data-reporting"></a>測試使用情況資料回報 

1. 若要測試使用情況資料回報，請在 Azure Stack 中建立一些資源。 例如，您可以建立[儲存體帳戶](azure-stack-provision-storage-account.md)、[Windows Server VM](azure-stack-provision-vm.md) 與 Linux VM (搭配基本與標準 SKU) 以查看核心使用情況的回報方式。 不同類型資源的使用情況資料會在不同的計量下回報。  

2. 讓您的資源執行幾小時。 系統大約每隔一小時會收集一次使用情況資訊。 收集之後，此資料會傳輸到 Azure 並在 Azure 商務系統中處理。 此程序可能需要最多數小時的時間。  

3. 以 Azure 帳戶系統管理員身分登入 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)然後選取您用來註冊 Azure Stack 的 Azure 訂用帳戶。 您可以檢視 Azure Stack 使用情況資料，以及針對所使用之每個資源收取的費用，如下列影像所示：  

   ![計費流程](media/azure-stack-usage-reporting/pricing-details.png)

如果是「Azure Stack 開發套件」，由於並不收取資源費用，因此價格會顯示為 $0.00。 如果是 Azure Stack 整合系統，則會顯示這些資源中每一項的實際費用。

## <a name="are-users-charged-for-the-infrastructure-virtual-machines"></a>是否會向使用者收取基礎結構虛擬機器的費用？
否，在部署期間建立的一些 Azure Stack 資源提供者虛擬機器和基礎結構虛擬機器的使用量資料會回報給 Azure，但使用者並無須支付這些項目的費用。 

使用者只需針對在使用者訂用帳戶底下建立的虛擬機器支付費用。 因此，必須在使用者訂用帳戶底下部署所有工作負載，才符合 Azure Stack 授權條款。

## <a name="how-do-i-use-my-existing-windows-server-licenses-in-azure-stack"></a>我要如何在 Azure Stack 中使用現有的 Windows Server 授權？ 
您可以在 Azure Stack 中使用現有的 Windows Server 授權，此模型稱為 BYOL (自備授權)。 使用您已經擁有的授權可避免產生額外的使用量計量。 若要使用您現有的授權，您必須依照[適用於 Windows Server 授權的混合式權益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)主題中所述的方式，部署 Windows Server 虛擬機器。 

## <a name="what-azure-meters-are-used-when-reporting-usage-data-in-integrated-systems"></a>回報整合系統中的使用量資料時會使用哪些 Azure 計量？
* **完整價格計量**：用於與使用者工作負載關聯的資源。  
* **系統管理計量**：用於基礎結構資源。 這些計量的價格為零。

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>會針對哪個訂用帳戶就所取用的資源收費？
我們會對[向 Azure 註冊 Azure Stack](azure-stack-register.md) 時提供的訂用帳戶收費。

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>哪些類型的訂用帳戶支援使用情況資料回報？
針對 Azure Stack 整合系統，支援「Enterprise 合約」(EA) 和 CSP 訂用帳戶。 

針對「Azure Stack 開發套件」，「Enterprise 合約」(EA)、隨用隨付、CSP 及 MSDN 訂用帳戶支援使用量資料回報。

## <a name="which-sovereign-clouds-support-usage-data-reporting"></a>哪些主權雲端支援使用量資料回報？
若要使用「Azure Stack 開發套件」中的使用量資料回報功能，必須在全球 Azure 系統中建立訂用帳戶。 在其中一個主權雲端 (Azure Government、Azure 德國與 Azure 中國雲端) 中建立的訂用帳戶無法向 Azure 註冊，因此它們不支援使用情況資料回報。 

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>使用者如何在 Azure 計費入口網站中識別 Azure Stack 使用情況資料？
使用者可以在使用情況詳細資料檔案中看到 Azure Stack 使用情況資料。 若要了解如何取得使用情況詳細資料檔案，請參閱[從 Azure 帳戶中心下載使用情況檔案](../billing/billing-download-azure-invoice-daily-usage-date.md#download-usage-from-the-account-center-csv)文章。 使用情況詳細資料檔案包含可識別 Azure Stack 儲存體與 VM 的 Azure Stack 計量。 在 Azure Stack 中使用的所有資源都會在名為 “Azure Stack” 的區域下回報。

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>為何在 Azure Stack 中回報的使用情況與從 Azure 帳戶中心產生的報告不符？
在 Azure Stack 使用量 API 所回報的使用量資料與「Azure 帳戶中心」所回報的使用量資料之間會有延遲。 此延遲是將使用量資料從 Azure Stack 上傳到 Azure 商務系統所需的時間。 由於此延遲，在午夜前短暫發生的使用情況可能會在隔天顯示在 Azure 中。 如果您使用 [Azure Stack 使用量 API](azure-stack-provider-resource-api.md)，並將結果與在 Azure 計費入口網站中回報的使用量做比較，就可以看到差異。

## <a name="next-steps"></a>後續步驟

* [提供者使用量 API](azure-stack-provider-resource-api.md)  
* [租用戶使用量 API](azure-stack-tenant-resource-usage-api.md)
* [使用狀況常見問題集](azure-stack-usage-related-faq.md)
