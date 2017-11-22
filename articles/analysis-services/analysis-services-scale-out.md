---
title: "Azure Analysis Services 擴充 | Microsoft Docs"
description: "使用擴充功能複寫 Azure Analysis Services 伺服器"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: owend
ms.openlocfilehash: a97f9648efef7f07659110d720c200dcd0a241a9
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services 擴充

透過擴充，可在查詢集區中的多個查詢複本之間散發用戶端查詢，縮短查詢工作高度負載期間的回應時間。 您也可以將處理作業從查詢集區中區隔出來，確保用戶端查詢不會受到處理作業的不良影響。 可以利用 Azure 入口網站或 Analysis Services REST API 設定擴充。

## <a name="how-it-works"></a>運作方式

在一般伺服器部署中，一部伺服器會作為處理伺服器和查詢伺服器使用。 如果伺服器上對應模型的用戶端查詢數量超過伺服器方案的查詢處理器 (QPU)，或是模型的處理作業與高度的查詢工作負載同時發生，則效能可能會降低。 

透過擴充，可以建立一個高達七個額外查詢複本的查詢集區 (加上您的伺服器總共八個)。 您可以調整查詢複本的數目，以符合關鍵時刻的 QPU 需求，並且可以隨時將處理伺服器與查詢集區分開。 

無論您在查詢集區中擁有多少個查詢複本，處理工作負載都不會分散在各查詢複本之間。 單一伺服器可作為處理伺服器使用。 查詢複本僅針對查詢集區中每個複本之間同步的模型提供查詢。 

處理作業完成之後，必須在處理伺服器與查詢複本伺服器之間執行同步處理。 自動化處理作業時，請務必在成功完成處理作業後設定同步作業。 透過入口網站中或使用 PowerShell 或 REST API，可以手動執行同步。

> [!NOTE]
> 標準定價層中的伺服器可以使用擴充。 每個查詢複本會以您伺服器的相同費率計費。

> [!NOTE]
> 擴充不會增加您伺服器的可用記憶體量。 若要增加記憶體則需升級方案。

## <a name="monitor-qpu-usage"></a>監視 QPU 使用量

 若要判斷您的伺服器是否需要擴充，請使用計量在 Azure 入口網站中監視您的伺服器。 如果您的 QPU 經常超出最大值，表示對應模型的查詢數目超出方案的 QPU 限制。 查詢執行緒集區佇列中的查詢數目超過可用 QPU 時，查詢集區作業佇列長度計量也會增加。 若要深入了解，請參閱[監視伺服器計量](analysis-services-monitor.md)。

## <a name="configure-scale-out"></a>設定擴充

### <a name="in-azure-portal"></a>Azure 入口網站

1. 在入口網站中，按一下 [擴充]。使用滑桿選取查詢複本伺服器的數目。 所選擇的複本數目不包括現有的伺服器。

2. 在 [Separate the processing server from the querying pool]\(區隔處理伺服器與查詢集區\) 中，選取 [是] 以將處理伺服器從查詢伺服器排除。

   ![擴充滑桿](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. 按一下 [儲存] 以佈建您的新查詢複本伺服器。 

主要伺服器上的表格式模型會與複本伺服器同步。 同步處理完成時，查詢集區即會開始分散複本伺服器之間的傳入查詢。 


## <a name="synchronization"></a>同步處理 

當您佈建新查詢複本時，Azure Analysis Services 會自動在所有複本上複寫您的模型。 您也可以使用入口網站或 REST API 來執行手動同步。 當您處理模型時，應該先執行同步處理，以便在您的查詢複本之間同步更新。

### <a name="in-azure-portal"></a>Azure 入口網站

在 [概觀] > [模型] > [同步處理模型] 中。

![擴充滑桿](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API
使用**同步**作業。

#### <a name="synchronize-a-model"></a>同步處理模型   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>取得同步處理狀態  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
若要從 PowerShell 執行同步處理，請[更新至最新](https://github.com/Azure/azure-powershell/releases) 5.01 版或更高版本的 AzureRM 模組。 使用 [Sync-AzureAnalysisServicesInstance](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance)。

## <a name="connections"></a>連線

在伺服器的 [概觀] 頁面中，有兩個伺服器名稱。 如果您還沒有為伺服器設定擴充，則這兩個伺服器名稱會以相同方式運作。 一旦設定了伺服器擴充，即必須根據連線類型指定適當的伺服器名稱。 

若為使用者用戶端連線 (如 Power BI Desktop、Excel 以及自訂應用程式)，請使用**伺服器名稱**。 

若為 SSMS、SSDT，以及 PowerShell、Azure 函數應用程式和 AMO 中的連接字串，請使用**管理伺服器名稱**。 管理伺服器名稱包含特殊 `:rw` (讀寫) 限定詞。 所有的處理作業皆發生在管理伺服器上。

![伺服器名稱](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>相關資訊

[監視伺服器計量](analysis-services-monitor.md)   
[管理 Azure Analysis Services](analysis-services-manage.md) 

