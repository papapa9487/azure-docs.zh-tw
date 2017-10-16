---
title: "Azure Stack 中的客戶帳務與退款 | Microsoft Docs"
description: "了解如何從 Azure Stack 擷取資源使用量資訊。"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>Azure Stack 中的使用量與計費

使用量代表使用者取用的資源數量。 Azure Stack 會收集每個使用者的使用量資訊，並使用該資訊向使用者收費。 此文章說明如何針對資源使用量向 Azure Stack 使用者收費，以及如何存取帳單資訊以進行分析、退款等作業。

Azure Stack 包含的基礎架構可針對所有資源收集並彙總使用量資料，並將此資料轉送給 Azure 商務系統。 您可以使用帳單配接器存取此資料，並將資料匯出到帳單系統，或將資料匯出到像 Microsoft Power BI 這樣的商務智慧工具。 匯出之後，此帳單資訊就會用於分析，或傳送到退款系統。

![將 Azure Stack 連接至帳單應用程式之帳單配接器的概念模型](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>使用量管線

Azure Stack 中的每個資源提供者都會依據資源使用情況發出使用量資料。 「使用量服務」會定期 (每小時或每天) 彙總此使用量資料，並將其儲存在使用量資料庫中。 Azure Stack 操作員和使用者可以使用使用量 API 來存取此已儲存的使用量資料。 

如果您已[向 Azure 註冊 Azure Stack 執行個體](azure-stack-register.md)，則「使用量橋接器」已設定為將該使用量資料傳送給 Azure 商務系統。 在該資料於 Azure 中可供使用之後，您便可以透過計費入口網站或使用 Azure 使用量 API 來存取它。 若要深入了解會向 Azure 回報哪些使用量資料，請參考[使用量資料回報](azure-stack-usage-reporting.md)主題。 

下圖顯示使用量管線中的主要元件：

![使用量管線](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>我可以找到哪些使用量資訊，以及如何尋找？

Azure Stack 資源提供者 (例如計算、儲存體及網路) 會以每小時為間隔，針對每個訂用帳戶產生使用量資料。 使用量資料包含與所使用資源有關的資訊，例如資源名稱、所使用的訂用帳戶、所使用的數量等。若要深入了解計量識別碼資源，請參閱[使用情況 API 常見問題集](azure-stack-usage-related-faq.md)文章。 

收集到使用量資料之後，就會將資料[回報給 Azure](azure-stack-usage-reporting.md)，以產生可透過 Azure 計費入口網站檢視的帳單。 

> [!NOTE]
> 對「Azure Stack 開發套件」使用者和採用容量授權模型的 Azure Stack 整合系統使用者而言，使用量資料回報並非必要功能。 若要深入了解 Azure Stack 中的授權，請參閱[封裝與定價](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) \(英文\) 資料表。

Azure 計費入口網站只會顯示可收費資源的使用量資料。 除了可收費資源之外，Azure Stack 可擷取更廣泛資源的使用量資料，您可以透過 REST API 或 PowerShell，在 Azure Stack 環境中存取那些資料。 Azure Stack 操作員可以擷取所有使用者訂用帳戶的使用量資料，而使用者則只能取得他們自己的使用量詳細資料。

## <a name="retrieve-usage-information"></a>擷取使用量資訊

若要產生使用量資料，您應該要有正在執行且積極地使用系統的資源，例如作用中的虛擬機器或包含某些資料的儲存體帳戶等。如果不確定您是否有任何資源正在 Azure Stack Marketplace 中執行，請部署虛擬機器 (VM)，並檢查 VM 監控刀鋒視窗以確定它正在執行。 請使用下列 PowerShell Cmdlet 檢視使用量資料：

1. [安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
2. [設定 Azure Stack 使用者](user/azure-stack-powershell-configure-user.md)或 [Azure Stack 操作員](azure-stack-powershell-configure-admin.md) 的 PowerShell 環境 

3. 若要擷取使用量資料，請使用 [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) PowerShell Cmdlet：

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>後續步驟

[向 Azure 回報 Azure Stack 使用量資料](azure-stack-usage-reporting.md)

[提供者資源使用情況 API](azure-stack-provider-resource-api.md)

[租用戶資源使用情況 API](azure-stack-tenant-resource-usage-api.md)

[使用量相關常見問題集](azure-stack-usage-related-faq.md)

