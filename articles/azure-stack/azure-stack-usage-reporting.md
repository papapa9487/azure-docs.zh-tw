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
ms.date: 07/10/2017
ms.author: sngun;AlfredoPizzirani
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 9bc202fe6aad70cb0f10a2da08e37428f78630d1
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---

# <a name="report-azure-stack-usage-data-to-azure"></a>向 Azure 回報 Azure Stack 使用情況資料 

使用情況資料 (亦稱為取用資料) 代表使用的資源量。 在 Azure Stack 中，必須針對計費用途向 Azure 回報使用情況資料。 Azure Stack 雲端系統管理員應該設定其 Azure Stack 執行個體，以向 Azure 回報使用情況資料。

> [!NOTE]
> 使用 Azure Stack 開發套件時，不需要回報使用情況資料，而且我們不會因為使用者取用資源而向其收費。 不過，Azure Stack 雲端系統管理員可以測試此功能並提供意見反應。 當 Azure Stack 多節點公開推出時，所有多節點環境都必須向 Azure 回報使用情況資料。

![計費流程](media/azure-stack-usage-reporting/billing-flow.png)

使用情況資料是從 Azure Stack 透過 Azure Bridge 傳送到 Azure。 在 Azure 中，商務系統會處理使用情況資料並產生帳單。 產生帳單之後，Azure 訂用帳戶擁有者可以從 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)檢視並下載帳單。 若要了解 Azure Stack 授權，請參閱 [Azure Stack 包裝與定價文件](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409) \(英文\)。

## <a name="set-up-usage-data-reporting"></a>設定使用情況資料報告

若要在 Azure Stack 中設定使用情況資料報告，您必須[向 Azure 註冊您的 Azure Stack 執行個體](azure-stack-register.md)。 在註冊程序中，會設定 Azure Stack 以搭配 Azure Bridge 使用，後者會將 Azure Stack 連線到 Azure 並傳送使用情況資料。 下列情況資料會從 Azure Stack 傳送到 Azure：

* **計量識別碼**：所取用之資源的唯一識別碼。
* **數量**：在特定時間範圍內發生的資源使用情況資料數量。
* **位置**：目前 Azure Stack 資源部署所在位置。
* **資源 URI**：將回報其使用狀況之資源的完整 URI。 
* **訂用帳戶識別碼**：Azure Stack 使用者的訂用帳戶識別碼。
* **時間**：使用情況資料的開始與結束時間。 在 Azure Stack 中取用這些資源與向商務系統回報使用情況資料的時間有一些延遲。 Azure Stack 每隔 24 小時會彙總一次使用情況資料，而向 Azure 中的商務管線回報使用情況資料則需要數小時。 因此，在午夜前短暫發生的使用情況可能會在隔天顯示在 Azure 中。

## <a name="test-usage-data-reporting"></a>測試使用情況資料回報 

1. 若要測試使用情況資料回報，請在 Azure Stack 中建立一些資源。 例如，您可以建立[儲存體帳戶](azure-stack-provision-storage-account.md)、[Windows Server VM](azure-stack-provision-vm.md) 與 Linux VM (搭配基本與標準 SKU) 以查看核心使用情況的回報方式。 不同類型資源的使用情況資料會在不同的計量下回報。  

2. 讓您的資源執行幾小時。 系統大約每隔一小時會收集一次使用情況資訊。 收集之後，此資料會傳輸到 Azure 並在 Azure 商務系統中處理。 此程序可能需要最多數小時的時間。  

3. 以 Azure 帳戶系統管理員身分登入 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)然後選取您用來註冊 Azure Stack 的 Azure 訂用帳戶。 您可以檢視 Azure Stack 使用情況資料，以及針對所使用之每個資源收取的費用，如下列影像所示：  
   ![計費流程](media/azure-stack-usage-reporting/pricng-details.png)

針對 Azure Stack 開發套件，我們不會就使用的 Azure Stack 資源收費，因此價格顯示為 $0.00。 當 Azure Stack 多節點公開推出時，您可以看到這些資源中每個資源的實際成本。 

## <a name="which-azure-stack-instances-are-charged"></a>哪個 Azure Stack 執行個體被收費？
針對 Azure Stack 開發套件執行個體，資源的使用是免費的。 

在公開推出時，我們會就 Azure Stack 多節點系統收費，但開發套件環境仍為免費。 針對多節點系統，工作負載 VM、儲存體服務與應用程式服務需收費。 

## <a name="are-users-charged-for-the-infrastructure-vms"></a>使用基礎結構 VM 是否需要付費？
否，Azure Stack 基礎結構 VM (在部署期間所建立) 的使用情況資料會被回報給 Azure，但我們並不會就這些 VM 收費。 基礎結構 VM 包括由 Azure Stack 部署指令碼所建立的 VM，以及執行 Microsoft 第一方資源提供者 (例如計算、儲存體、SQL) 的 VM。

## <a name="what-azure-meters-are-used-when-reporting-usage-data"></a>回報使用情況資料時會使用哪些 Azure 計量？
下列是會在使用情況資料回報程序中使用的兩組計量：  

* **完整價格計量**：用於與使用者工作負載關聯的資源。  
* **系統管理計量**：用於基礎結構資源。 這些計量的價格為零。

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>會針對哪個訂用帳戶就所取用的資源收費？
我們會對[向 Azure 註冊 Azure Stack](azure-stack-register.md) 時提供的訂用帳戶收費。

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>哪些類型的訂用帳戶支援使用情況資料回報？
針對 Azure Stack 開發套件，Enterprise 合約 (EA)、即用即付與 MSDN 訂用帳戶支援使用情況資料回報。 

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>使用情況資料回報在主權雲端中是否能運作？
在 Azure Stack 開發套件中，使用情況資料回報需要在全球 Azure 系統中建立的訂用帳戶。 在其中一個主權雲端 (Azure Government、Azure 德國與 Azure 中國雲端) 中建立的訂用帳戶無法向 Azure 註冊，因此它們不支援使用情況資料回報。 

## <a name="can-an-administrator-test-usage-data-reporting-before-ga"></a>在全面推出之前，系統管理員是否可以測試使用情況資料回報？
是，Azure Stack 系統管理員可以透過向 Azure [註冊](azure-stack-register.md)開發套件執行個體，以測試使用情況資料回報。 註冊之後，使用情況資料會開始從您的 Azure Stack 執行個體傳送到您的 Azure 訂用帳戶。 

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>使用者如何在 Azure 計費入口網站中識別 Azure Stack 使用情況資料？
使用者可以在使用情況詳細資料檔案中看到 Azure Stack 使用情況資料。 若要了解如何取得使用情況詳細資料檔案，請參閱[從 Azure 帳戶中心下載使用情況檔案](../billing/billing-download-azure-invoice-daily-usage-date.md#download-usage-from-the-account-center-csv)文章。 使用情況詳細資料檔案包含可識別 Azure Stack 儲存體與 VM 的 Azure Stack 計量。 在 Azure Stack 中使用的所有資源都會在名為 “Azure Stack” 的區域下回報。

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>為何在 Azure Stack 中回報的使用情況與從 Azure 帳戶中心產生的報告不符？
在 Azure Stack 中產生使用情況資料的時間與該資料提交到 Azure 商務系統的時間之間有一些延遲。 此延遲是將使用情況資料從 Azure Stack 上傳到 Azure 商務系統的時間。 由於此延遲，在午夜前短暫發生的使用情況可能會在隔天顯示在 Azure 中。 若使用 [Azure Stack 使用情況 API](azure-stack-provider-resource-api.md)，並將結果與在 Azure 計費入口網站中回報的使用情況比較，您可以看到差異。

## <a name="next-steps"></a>後續步驟

* [提供者使用量 API](azure-stack-provider-resource-api.md)  
* [租用戶使用量 API](azure-stack-tenant-resource-usage-api.md)
* [使用狀況常見問題集](azure-stack-usage-related-faq.md)
