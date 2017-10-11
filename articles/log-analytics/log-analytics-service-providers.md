---
title: "服務提供者的 Log Analytics 功能 | Microsoft Docs"
description: "Log Analytics 可協助管理服務提供者 (MSP)、大型企業、獨立軟體廠商 (ISV) 和主機服務提供者管理和監視客戶的內部部署或雲端基礎結構中的伺服器。"
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: richrund
ms.openlocfilehash: 8a67d9a9d345682e9e6c8f5c7779204a038f5f6a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="log-analytics-features-for-service-providers"></a>服務提供者的 Log Analytics 功能
Log Analytics 可協助管理服務提供者 (MSP)、大型企業、獨立軟體廠商 (ISV) 和主機服務提供者管理和監視客戶的內部部署或雲端基礎結構中的伺服器。 

大型企業與服務提供者有許多相似之處，特別是當有集中式的 IT 團隊負責管理許多不同業務單位的 IT 時。 為了簡單起見，本文件會使用「服務提供者」這個詞彙，但是相同的功能也適用於企業或其他客戶。

## <a name="cloud-solution-provider"></a>雲端解決方案提供者
對於身為[雲端解決方案提供者 (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) 方案一部分的合作夥伴和服務提供者，Log Analytics 是 CSP 訂用帳戶上的其中一個可用 Azure 服務。 

對於 Log Analytics，下列功能在「雲端解決方案提供者」訂用帳戶中啟用。

身為「雲端解決方案提供者」，您可以：

* 在租用戶 (客戶) 訂用帳戶中建立 Log Analytics 工作區。
* 存取租用戶建立的工作區。 
* 使用 Azure 使用者管理，新增和移除使用者對工作區的存取權。 在 OMS 入口網站中租用戶的工作區時，[設定] 底下的使用者管理頁面無法使用
  * Log Analytics 尚未支援以角色為基礎的存取 - 在 Azure 入口網站中授與使用者 `reader` 權限可讓他們在 OMS 入口網站中進行組態變更

若要登入租用戶的訂用帳戶，您需要指定租用戶識別碼。 租用戶識別碼通常是您用來登入的電子郵件地址的最後一部分。

* 在 OMS 入口網站中，針對入口網站在 URL 新增 `?tenant=contoso.com`。 例如，`mms.microsoft.com/?tenant=contoso.com`
* 在 PowerShell 中，使用 `Add-AzureRmAccount` Cmdlet 時使用 `-Tenant contoso.com` 參數
* 當您從 Azure 入口網站使用 `OMS portal` 連結，來開啟並登入所選取工作區的 OMS 入口網站時，會自動新增租用戶識別碼

身為「雲端解決方案提供者」的「客戶」，您可以：

* 在 CSP 訂用帳戶中建立 Log Analytics 工作區
* 存取 CSP 建立的工作區
  * 從 Azure 入口網站使用 `OMS portal` 連結，來開啟並登入所選取工作區的 OMS 入口網站
* 檢視及使用 OMS 入口網站中 [設定] 底下的使用者管理頁面

> [!NOTE]
> Log Analytics 內含的備份和 Site Recovery 解決方案無法連接至復原服務保存庫，且無法在 CSP 訂用帳戶中設定。 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>使用 Log Analytics 管理多個客戶
建議您為每個您管理的客戶建立 Log Analytics 工作區。 Log Analytics 工作區提供：

* 儲存資料的地理位置。 
* 計費的細微度 
* 資料隔離 
* 唯一的組態

藉由建立每個客戶的工作區，就能夠區隔每個客戶的資料，也可以追蹤每個客戶的使用方式。

[管理對 Log Analytics 的存取](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need)中會說明建立多個工作區之時機和原因的其他詳細資料。

建立及設定客戶的工作區可以使用 [PowerShell](log-analytics-powershell-workspace-configuration.md)、[Resource Manager 範本](log-analytics-template-workspace-configuration.md)，或使用 [REST API](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/) 進行自動化。

使用工作區組態的 Resource Manager 範本，可讓您具有主要組態，用來建立和設定工作區。 您可以確信為客戶建立工作區時，會自動針對您的需求進行設定。 當您更新您的需求時，範本會更新，然後重新套用現有的工作區。 此程序可確保現有的工作區符合新的標準。    

在管理多個 Log Analytics 工作區時，我們建議使用[警示](log-analytics-alerts.md)功能，整合每個工作區與現有的票證系統/Operations 主控台。 藉由與您現有的系統整合，支援人員可以繼續依照其熟悉的程序。 Log Analytics 會定期針對您指定的警示準則檢查每個工作區，並且在需要採取動作時產生警示。

若要建立個人化的資料檢視，請使用 Azure 入口網站中的[儀表板](../azure-portal/azure-portal-dashboards.md)功能。  

對於摘要跨工作區資料的執行層級報告，您可以使用 Log Analytics 與 [PowerBI](log-analytics-powerbi.md) 之間的整合。 如果您需要與其他報告系統整合，您可以使用搜尋 API (透過 PowerShell 或 [REST](log-analytics-log-search-api.md)) 來執行查詢，並且匯出搜尋結果。

## <a name="next-steps"></a>後續步驟
* 使用 [Resource Manager 範本](log-analytics-template-workspace-configuration.md)建立和設定工作區
* 使用 [PowerShell](log-analytics-powershell-workspace-configuration.md)自動建立工作區 
* 使用[警示](log-analytics-alerts.md)與現有的系統整合
* 使用 [PowerBI](log-analytics-powerbi.md)產生摘要報告

