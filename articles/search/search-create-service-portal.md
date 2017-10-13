---
title: "在入口網站中建立 Azure 搜尋服務 | Microsoft Docs"
description: "在入口網站中佈建 Azure 搜尋服務。"
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: 58f4eab190e40e16ed261c165ffdfc8155eeb434
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-search-service-in-the-portal"></a>在入口網站中建立 Azure 搜尋服務

本文說明如何在入口網站中建立或佈建 Azure 搜尋服務。 如需 PowerShell 的指示，請參閱[使用 PowerShell 管理 Azure 搜尋服務](search-manage-powershell.md)。

## <a name="subscribe-free-or-paid"></a>訂閱 (免費或付費)

[開啟免費的 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，並使用免費信用額度來試用付費的 Azure 服務。 當您用完信用額度之後，請保留帳戶，並繼續使用免費的 Azure 服務，例如網站。 除非您明確變更您的設定且同意付費，否則我們絕對不會從您的信用卡收取任何費用。

或者，請[啟用 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。 MSDN 訂用帳戶每月會提供您信用額度，讓您可以用於 Azure 付費服務。 

## <a name="find-azure-search"></a>尋找 Azure 搜尋服務
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下左上角的加號 ("+")。
3. 選取 [Web + 行動] > [Azure 搜尋服務]。

![](./media/search-create-service-portal/find-search2.png)

## <a name="name-the-service-and-url-endpoint"></a>為服務和 URL 端點命名

服務名稱是 URL 端點的一部分，API 呼叫是根據此端點所發出。 在 [URL] 欄位中輸入您的服務名稱。 

服務名稱需求：
   * 長度為 2 到 60 個字元
   * 小寫字母、數字或連字號 ("-")
   * 不能使用連字號 ("-") 作為前 2 個字元或最後一個字元
   * 不能是連續的破折號 ("-")

## <a name="select-a-subscription"></a>選取一個訂用帳戶
如果您有一個以上的訂用帳戶，請選擇一個同樣具有資料或檔案儲存體服務的訂用帳戶。 Azure 搜尋服務可以透過「索引子」自動偵測 Azure 資料表和 Blob 儲存體、SQL Database 和 Azure Cosmos DB 以進行索引編製，但只會針對相同訂用帳戶中的服務。

## <a name="select-a-resource-group"></a>選取資源群組
資源群組是一起使用之 Azure 服務和資源的集合。 例如，如果您使用 Azure 搜尋服務來編製 SQL 資料庫的索引，則這兩個服務應該屬於同一個資源群組。

> [!TIP]
> 刪除資源群組也會刪除其中的服務。 針對使用多個服務的原型專案，將它們全部放入同一個資源群組，在專案結束之後就能更容易清除。 

## <a name="select-a-hosting-location"></a>選取裝載位置 
做為 Azure 服務，Azure 搜尋服務可以裝載於世界各地的資料中心。 請注意，各地理位置的[價格可能不同](https://azure.microsoft.com/pricing/details/search/) 。

## <a name="select-a-pricing-tier-sku"></a>選取定價層 (SKU)
[Azure 搜尋服務目前提供多個定價層](https://azure.microsoft.com/pricing/details/search/)︰免費、基本或標準。 每一層都有自己的[容量和限制](search-limits-quotas-capacity.md)。 請參閱[選擇定價層或 SKU](search-sku-tier.md) 以取得指導方針。

在此逐步解說中，我們已為服務選擇標準層。

## <a name="create-your-service"></a>建立您的服務

請記得將您的服務釘選到儀表板，以方便在登入時存取。

![](./media/search-create-service-portal/new-service2.png)

## <a name="scale-your-service"></a>調整您的服務
可能需要幾分鐘的時間來建立服務 (視層級而定，15 分鐘或更多)。 佈建完您的服務之後，您可以調整它以符合您的需求。 由於您為「Azure 搜尋服務」選擇了「標準」層，因此您可以在兩個維度調整服務︰複本和資料分割。 如果您選擇的是「基本」層，則只能新增複本。 如果您佈建的是免費服務，則無法進行調整。

「資料分割」允許您的服務儲存及搜尋更多文件。

「複本」允許服務來處理更高的搜尋查詢負載。

> [!Important]
> 服務必須具有 [2 個唯讀 SLA 的複本和 3 個讀/寫 SLA 的複本](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

1. 在 Azure 入口網站中移至您的搜尋服務刀鋒視窗。
2. 在左導覽窗格中，選取 [設定] > [調整]。
3. 使用滑桿來新增複本或分割區。

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> 關於單一服務中允許的搜尋單位總數 (複本 * 分割區 = 搜尋單位總數)，每一層各有不同的[限制](search-limits-quotas-capacity.md)。

## <a name="when-to-add-a-second-service"></a>新增第二個服務的時機

大部分的客戶都是使用在提供[正確資源平衡](search-sku-tier.md)的層上佈建的單一服務。 單一服務可以裝載多個索引 (數量受限於[所選層的最大限制](search-capacity-planning.md))，且每個索引都互相隔離。 在 Azure 搜尋服務中，要求只能導向到單一索引，以降低意外或刻意從相同服務的其他索引中擷取資料的機會。

雖然大部分的客戶只使用單一服務，但如果操作需求包含下列項目，則可能需要服務備援：

+ 災害復原 (資料中心中斷)。 Azure 搜尋服務在中斷時不會提供即時容錯移轉。 如需建議和指導方針，請參閱[服務管理](search-manage.md)。
+ 您的多租用戶模型調查判斷額外服務為最佳化設計。 如需詳細資訊，請參閱[針對多租用戶設計](search-modeling-multitenant-saas-applications.md)。
+ 針對全球部署的應用程式，您在多個區域可能都需要 Azure 搜尋服務執行個體，以降低應用程式國際流量的延遲。

> [!NOTE]
> 在 Azure 搜尋服務中，您無法區隔索引和查詢工作負載，因此您永遠不會針對區隔的工作負載建立多重服務。 一律是在建立索引的服務上查詢該索引 (您無法在某個服務中建立索引，並將它複製到另一個服務)。
>

不需要第二個服務即可獲得高可用性。 當您在同一個服務中使用 2 個或更多的複本，查詢就會達到高可用性。 複本更新是循序的，這表示當服務更新推出時，至少會有一個複本是可運作的。如需執行時間的詳細資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

## <a name="next-steps"></a>後續步驟
佈建 Azure 搜尋服務之後，您就可以[定義索引](search-what-is-an-index.md)，以便上傳和搜尋您的資料。

若要從程式碼或指令碼存取服務，請提供 URL (*service-name*.search.windows.net) 和金鑰。 系統管理金鑰授與完整存取權；查詢金鑰則授與唯讀存取權。 請參閱[如何在 .NET 中使用 Azure 搜尋服務](search-howto-dotnet-sdk.md)以便開始使用。

請參閱[建置及查詢您的第一個索引](search-get-started-portal.md)，來取得以入口網站為基礎的快速教學課程。

