---
title: "Blob 的 Azure 經常性存取、非經常性存取和封存儲存體 | Microsoft Docs"
description: "Azure Blob 儲存體帳戶的經常性存取、非經常性存取和封存儲存體。"
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/05/2017
ms.author: mihauss
ms.openlocfilehash: 501fc59efb8bacf58fea2825752d3a33c6ea5963
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="azure-blob-storage-hot-cool-and-archive-preview-storage-tiers"></a>Azure Blob 儲存體︰經常性存取、非經常性存取和封存 (封存) 儲存層

## <a name="overview"></a>概觀

Azure 儲存體會為 Blob 物件儲存體提供三個儲存層，所以您可根據資料使用方式，以最符合成本效益的方式進行儲存。 Azure **經常性存取儲存層** 已最佳化，可用於儲存經常存取的資料。 Azure **非經常性存取儲存層**已最佳化，可用於儲存不常存取且至少儲存 30 天的資料。 Azure **封存儲存層** (預覽) 最適合用於儲存很少存取且至少儲存 180 天 (有彈性的延遲需求，大約數小時) 的資料。 封存儲存層只可使用於 Blob 層級，而不可使用於儲存體帳戶層級。 非經常性存取儲存層中的資料可容忍稍微較低的可用性，但仍要求高持久性以及與經常性存取資料類似的存取時間和輸送量特性。 對於非經常性存取資料而言，稍微較低的可用性 SLA 和更高的存取成本 (相較於經常性存取資料) 是可接受的取捨，可讓儲存成本降低。 封存儲存體已離線並提供最低的儲存成本，但存取成本最高。

現今，儲存在雲端的資料迅速成長。 若要管理您不斷擴充的儲存體需求的成本，根據存取頻率和計劃性保留期來組織您的資料，進而將成本最佳化很有幫助。 就資料在存留期內的產生、處理和存取方式而言，儲存在雲端的資料可能不同。 有些資料在其存留期內會積極地存取及修改。 有些資料在其存留期的早期存取頻率很高，但存取頻率隨著資料老化而大幅下滑。 有些資料在雲端維持閒置，而且儲存後就很少存取。

上述每個資料存取案例均受惠於針對特定存取模式最佳化的不同儲存層。 使用經常性存取、非經常性存取和封存儲存層，Azure Blob 儲存體可透過各種價格模式，滿足差異化儲存層的這項需求。

## <a name="blob-storage-accounts"></a>Blob 儲存體帳戶

**Blob 儲存體帳戶** 是特殊的儲存體帳戶，可將非結構化資料儲存為 Azure 儲存體中的 Blob (物件)。 使用 Blob 儲存體帳戶，您現在可以根據存取模式，在帳戶層級選擇經常性存取或非經常性存取儲存層，或在 blob 層級選擇經常性存取、非經常性存取或封存層。 將很少、不常和經常存取的資料分別放在經常性存取、非經常性存取和封存儲存層，可將成本最佳化。 Blob 儲存體帳戶類似於現有的一般用途儲存體帳戶，可共用所有強大的持續性、可用性、延展性以及您現今使用的效能功能，包括區塊 Blob 和附加 Blob 的 100% API 一致性。

> [!NOTE]
> Blob 儲存體帳戶僅支援區塊和附加 Blob，不支援分頁 Blob。

Blob 儲存體帳戶會在帳戶層級公開 [存取層] 屬性，以將預設儲存體帳戶層指定為 [經常性存取] 或 [非經常性存取]。 預設儲存體帳戶層會套用至未在 blob 層級設定明確層的任何 blob。 如果您的資料使用模式有變動，您也可以隨時在這些儲存層之間切換。 **保存層** (預覽) 只能套用在 blob 層級。

> [!NOTE]
> 變更儲存層可能會導致額外的費用。 如需詳細資訊，請參閱[價格和計費](#pricing-and-billing)一節。

### <a name="hot-access-tier"></a>經常性存取層

經常性存取儲存層的儲存成本會高於非經常性存取和封存儲存層，但存取成本最低。 經常性存取儲存層的使用案例範例包括︰

* 使用中或預期會經常存取 (讀取和寫入) 的資料。
* 資料會預備好進行處理且最終移轉至非經常性存取儲存層。

### <a name="cool-access-tier"></a>非經常性存取層

相較於經常性存取儲存層，非經常性儲存層具有較低的儲存成本和較高的存取成本。 這一層適用於將保留在非經常性存取層至少 30 天的資料。 非經常性存取儲存層的使用案例範例包括︰

* 短期備份和災害復原資料集。
* 不再經常檢視，但存取時應立即可用的較舊媒體內容。
* 當收集較多資料以供未來處理時，需要以符合成本效益方式預存的大型資料集。 (例如，科學資料、來自製造工廠的原始遙測資料等長期儲存)

### <a name="archive-access-tier-preview"></a>封存存取層 (預覽)

封存儲存體的儲存成本最低，而相較於經常性存取和非經常性存取儲存體，其資料擷取成本比較高。 這一層適用於可承受擷取延遲數個小時，而且將保留在封存層中至少 180 天的資料。

當 blob 位於封存儲存體時，它已離線且無法加以讀取 (中繼資料除外，其為線上且可用狀態)、複製、覆寫或修改。 您也無法製作封存儲存體中的 blob 快照。 不過，您可以使用現有的作業來刪除、列出、取得 blob 屬性/中繼資料，或變更您的 blob 層。

#### <a name="blob-rehydration"></a>Blob 解除凍結
若要閱讀封存儲存體中的資料，您必須先將 blob 層變更為經常性存取或非經常性存取。 此程序稱為解除凍結，最多可能需要 15 小時才能完成小於 50 GB 的 blob。 較大 blob 所需的額外時間會因 blob 輸送量限制而有所不同。

在解除凍結期間，您可以查看「封存狀態」blob 屬性，以確認是儲存層否已變更。 視目的地層而定，狀態會顯示為 "rehydrate-pending-to-hot" 或 "rehydrate-pending-to-cool"。 完成時，系統會移除 [封存狀態] blob 屬性，而 [存取層] blob 屬性會反映經常性存取或非經常性存取層。  

封存儲存層的使用案例範例包括︰

* 長期備份、封存和災害復原資料集
* 即使已處理成最終可用格式，但還是需要保存的原始資料。 (例如，轉碼成其他格式之後的原始媒體檔案)
* 需要儲存一段時間且幾乎不曾存取的相容性和封存資料。 (例如，保全攝影機錄影畫面、醫療機構的舊 X 光/MRI、金融服務的客服電話錄音和文字記錄)

### <a name="recommendations"></a>建議

如需儲存體帳戶的詳細資訊，請參閱 [關於 Azure 儲存體帳戶](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 。

對於只需要封鎖或附加 Blob 儲存體的應用程式，我們建議使用 Blob 儲存體帳戶，以充分利用分層式儲存的差異化價格模型。 但是，我們了解在某些情況下這可能不可行，因為使用一般用途的儲存體帳戶是最好的選擇，例如︰

* 您需要使用資料表、佇列或檔案，並希望 Blob 儲存在相同的儲存體帳戶中。 將這些 Blob 儲存在相同的帳戶，而非擁有相同的共用金鑰，並沒有任何技術優勢。

* 您仍然需要使用傳統部署模型。 僅可透過 Azure Resource Manager 部署模型取得 Blob 儲存體帳戶。

* 您必須使用分頁 Blob。 Blob 儲存體帳戶不支援分頁 Blob。 我們通常建議使用區塊 Blob，除非您對分頁 Blob 有特定的需求。

* 您使用早於 2014-02-14 的 [儲存體服務 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 版本，或版本低於 4.x 的用戶端程式庫，所以無法升級您的應用程式。

> [!NOTE]
> 目前所有的 Azure 區域都支援 Blob 儲存體帳戶。


## <a name="blob-level-tiering-feature-preview"></a>Blob 層級的階層處理功能 (預覽)

Blob 層級的階層處理可讓您使用稱為[設定 Blob 層](/rest/api/storageservices/set-blob-tier)的單一作業，在物件層級變更您的資料層。 您可以隨著使用模式變更，在經常性存取、非經常性存取或封存層之間輕鬆地變更 blob 的存取層，而不必在帳戶之間移動資料。 所有的存取層變更都會立即發生，但是當 blob 從封存解除凍結時除外。 最後一個 blob 層變更的時間會透過 blob 屬性中的 [存取層變更時間] 屬性公開。 如果 blob 位於封存層中，則可能不會遭到覆寫，因此，在此情況下不允許上傳相同的 blob。 您可以覆寫經常性存取和非經常性存取中的 blob，而在此情況下，新的 blob 會繼承已被覆寫之舊 blob 的儲存層。

三個儲存層中的 blob 都可以共存於相同的帳戶中。 任何沒有明確指派之階層的 blob 都會從帳戶存取層設定推斷階層。 如果存取層是從帳戶推斷而來，您會看到 [推斷的存取層] 屬性設定為"true"，而且 blob [存取層] 屬性符合帳戶層。 在 Azure 入口網站中，推斷的存取層屬性會顯示 blob 存取層 (例如，經常性存取 (推斷) 或非經常性存取 (推斷))。

> [!NOTE]
> 封存儲存體與 Blob 層級階層處理只支援區塊 blob。 您也無法變更具有快照集之區塊 blob 的存取層。

### <a name="blob-level-tiering-billing"></a>Blob 層級的階層處理計費

當 blob 移至存取頻率較低的階層 (經常性存取 -> 非經常性存取、經常性存取 -> 封存，或非經常性存取 -> 封存) 時，此作業會在寫入至目的地階層時計費，而且適用目的地階層的寫入作業 (每 10,000 個) 和資料寫入 (每 GB) 費用。 如果 blob 移至存取頻率較高的階層 (封存 -> 非經常性存取、風存 -> 經常性存取，或非經常性存取 -> 經常性存取)，此作業會在從來源階層讀取時計費，而且適用來源階層的讀取作業 (每 10,000 個) 和資料擷取 (每 GB) 費用。

若要使用處於預覽狀態的這些功能，請遵循 [Azure 封存和 Blob 層級的階層處理部落格公告](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering)中的指示。

以下列出在 blob 層級的階層處理預覽期間套用的一些限制：

* 只有在成功預覽註冊後於美國東部 2、美國東部或美國西部建立的新 Blob 儲存體帳戶支援封存儲存體。

* 只有在成功預覽註冊後於公用區域中建立的新 Blob 儲存體帳戶支援 Blob 層級的階層處理。

* 只有 [LRS] (../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#locally-redundant-storage) 儲存體支援 Blob 層級的階層處理和封存儲存體。 未來將會支援 [GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#geo-redundant-storage) 和 [RA-GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#read-access-geo-redundant-storage)。

* 您無法變更具有快照集之 blob 的階層。

* 您也無法複製封存儲存體中的 blob 或製作其快照集。

## <a name="comparison-of-the-storage-tiers"></a>儲存層的比較

下表顯示經常性存取與非經常性存取儲存層的比較。 封存 blob 層級的階層處於預覽狀態，所以沒有其適用的 SLA。

| | **經常性存取儲存層** | **非經常性存取儲存層** | **封存儲存層**
| ---- | ----- | ----- | ----- |
| **可用性** | 99.9% | 99% | N/A |
| **可用性** <br> **(RA-GRS 讀取)**| 99.99% | 99.9% | N/A |
| **使用費用** | 儲存成本較高，存取和交易成本較低 | 儲存成本較低，存取和交易成本較高 | 儲存成本最低，存取和交易成本最高 |
| **最小物件大小** | N/A | N/A | N/A |
| **最小儲存持續時間** | N/A | N/A | 180 天
| **延遲** <br> **(距第一位元組時間)** | 毫秒 | 毫秒 | < 15 小時
| **擴充和效能目標** | 與一般用途儲存體帳戶相同 | 與一般用途儲存體帳戶相同 | 與一般用途儲存體帳戶相同 |

> [!NOTE]
> Blob 儲存體帳戶支援與一般用途儲存體帳戶相同的效能和延展性目標。 如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (英文)。


## <a name="pricing-and-billing"></a>價格和計費
Blob 儲存體帳戶會對以每個 Blob 層為基礎的 Blob 儲存體使用價格模型。 使用 Blob 儲存體帳戶時，需考量下列計費資訊：

* **儲存成本**︰除了儲存的資料量以外，儲存資料的成本會因儲存層而異。 每 GB 的成本會隨著儲存層存取頻率降低而減少。

* **資料存取成本**：資料存取費用會隨著儲存層存取頻率降低而增加。 對於非經常性存取與封存儲存層中的資料，您需支付讀取的每 GB 資料存取費用。

* **交易成本**︰所有層都有每筆交易的費用，該費用會隨著儲存層存取頻率降低而增加。

* **異地複寫資料傳輸成本**︰這只適用於已設定異地複寫的帳戶，包括 GRS 和 RA-GRS。 異地複寫資料傳輸會產生每 GB 費用。

* **輸出資料傳輸成本**︰輸出資料傳輸 (從 Azure 區域傳出的資料) 會產生每 GB 頻寬使用量費用，與一般用途的儲存體帳戶一致。

* **變更儲存層**︰將帳戶儲存層從非經常性存取變更為經常性存取時，會產生等於讀取儲存體帳戶中所有資料的費用。 不過，將帳戶儲存層從經常性存取變更為非經常性存取時，會產生等於將所有資料寫入非經常性存取層的費用。

> [!NOTE]
> 如需 Blob 儲存體帳戶的價格模型詳細資訊，請參閱 [Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/)頁面。 如需輸出資料傳輸費用的詳細資訊，請參閱[資料傳輸價格詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)頁面。

## <a name="quick-start"></a>快速入門

在這一節中，我們會使用 Azure 入口網站示範下列案例︰

* 如何建立 Blob 儲存體帳戶。
* 如何管理 Blob 儲存體帳戶。

您無法設定要在下列範例中封存的存取層，因為此設定會套用到整個儲存體帳戶。 您只能在特定 blob 上設定封存。

### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>使用 Azure 入口網站建立 Blob 儲存體帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 [中樞] 功能表上，選取 [新增] > [資料+儲存體] > [儲存體帳戶]。

3. 輸入儲存體帳戶的名稱。

    此名稱必須是全域唯一的；它會做為用來存取儲存體帳戶中物件之 URL 的一部分。  

4. 選取 [Resource Manager]  做為部署模型。

    分層式儲存體僅適用於 Resource Manager 儲存體帳戶；這是新資源的建議部署模型。 如需詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。  

5. 在 [帳戶類型] 下拉式清單中，選取 [Blob 儲存體] 。

    這是您用來選取儲存體帳戶類型的地方。 分層式儲存體不適用於一般用途的儲存體；它僅適用於 Blob 儲存體類型帳戶。     

    當您選取此選項，效能層會設定為「標準」。 分層式儲存體不適用於進階效能層。

6. 選取儲存體帳戶的複寫選項︰[LRS]、[GRS] 或 [RA-GRS]。 預設值是 [RA-GRS] 。

    LRS = 本地備援儲存體；GRS = 異地備援儲存體 (2 個區域)；RA-GRS 是讀取權限異地備援儲存體 (2 個區域，具有第二個區域的讀取權限)。

    如需 Azure 儲存體複寫選項的詳細資訊，請參閱 [Azure 儲存體複寫](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

7. 針對您的需求選取正確的儲存層︰將 [存取層] 設定為 [非經常性存取] 或 [經常性存取]。 預設值為 [經常存取] 。

8. 選取您要在其中建立新儲存體帳戶的訂用帳戶。

9. 指定新的資源群組，或選取現有的資源群組。 如需資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。

10. 選取儲存體帳戶的區域。

11. 按一下 [建立]  建立儲存體帳戶。

### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>使用 Azure 入口網站變更 Blob 儲存體帳戶的儲存層

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽至儲存體帳戶、選取 [所有資源]，然後選取您的儲存體帳戶。

3. 在 [設定] 刀鋒視窗中按一下 [組態]  ，以檢視和/或變更帳戶組態。

4. 針對您的需求選取正確的儲存層︰將 [存取層] 設定為 [非經常性存取] 或 [經常性存取]。

5. 按一下刀鋒視窗頂端的 [儲存]。

### <a name="change-the-storage-tier-of-a-blob-using-the-azure-portal"></a>使用 Azure 入口網站變更 Blob 的儲存層

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 若要瀏覽至儲存體帳戶，請選取 [所有資源]，選取您的儲存體帳戶，選取您的容器，然後選取您的 Blob。

3. 在 Blob 屬性刀鋒視窗中，按一下 [存取層] 下拉式功能表以選取 [經常性存取]、[非經常性存取] 或 [封存] 儲存層。

5. 按一下刀鋒視窗頂端的 [儲存]。

> [!NOTE]
> 變更儲存層可能會導致額外的費用。 如需詳細資訊，請參閱[價格和計費](#pricing-and-billing)一節。


## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>評估及移轉至 Blob 儲存體帳戶
這一節的目的是要協助使用者順利地轉換成使用 Blob 儲存體帳戶。 有兩個使用者案例：

* 您有現有的一般用途儲存體帳戶，而且想要評估變更成具有正確儲存層的 Blob 儲存體帳戶。
* 您已決定使用 Blob 儲存體帳戶，或已有一個帳戶並想要評估您應該使用經常性存取還是非經常性存取儲存層。

在這兩種情況下，第一要務是估計儲存和存取在 Blob 儲存體帳戶中所儲存資料的成本，並與您目前的成本進行比較。

## <a name="evaluating-blob-storage-account-tiers"></a>評估 Blob 儲存體帳戶層

若要估計儲存和存取在 Blob 儲存體帳戶中所儲存資料的成本，必須評估您現有的使用模式或接近您預期的使用模式。 一般而言，您想要知道︰

* 您的儲存體使用情況 – 目前儲存多少資料，以及每個月的變化情況為何？

* 您的儲存體存取模式 – 有多少資料正從帳戶讀取和寫入其中 (包括新資料)？ 有多少交易用於資料存取，而它們是何種交易？

## <a name="monitoring-existing-storage-accounts"></a>監視現有的儲存體帳戶

若要監視現有的儲存體帳戶並收集此資料，您可以利用 Azure 儲存體分析來執行記錄及提供儲存體帳戶的度量資料。 儲存體分析可以針對一般用途儲存體帳戶和 Blob 儲存體帳戶，儲存包含與 Blob 儲存體服務要求相關之彙總交易統計資料和容量資料的度量。 此資料會儲存在相同儲存體帳戶的已知資料表中。

如需詳細資訊，請參閱[關於儲存體分析度量](https://msdn.microsoft.com/library/azure/hh343258.aspx)和[儲存體分析度量資料表結構描述](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Blob 儲存體帳戶會公開僅適用於儲存和存取該帳戶度量資料的表格服務端點。

若要監視 Blob 儲存體服務的儲存體使用情況，您必須啟用容量度量。
啟用此度量後，系統會每日記錄儲存體帳戶的 Blob 服務容量資料，而該資料會以資料表項目形式記錄並寫入至相同儲存體帳戶內的 $MetricsCapacityBlob  資料表。

若要監視 Blob 儲存體服務的資料存取模式，您必須在 API 層級啟用每小時交易度量。 啟用此度量後，系統會每小時彙總每筆 API 交易，而該資料會以資料表項目形式記錄並寫入至相同儲存體帳戶內的 $MetricsHourPrimaryTransactionsBlob  資料表。 使用 RA-GRS 儲存體帳戶時，$MetricsHourSecondaryTransactionsBlob  資料表會將交易記錄至次要端點。

> [!NOTE]
> 如果您已有一般用途的儲存體帳戶，並在中儲存分頁 blob 和虛擬機器磁碟以及區塊和附加 blob 資料，就不適用此估計程序。 這是因為您無法只根據針對可移轉至 Blob 儲存體帳戶之區塊和附加 blob 的 blob 類型，區分容量和交易度量。

若要取得資料使用和存取模式的適當近似值，建議您針對代表一般使用情況的度量選擇保留期，並進行推斷。 其中一個選項是保留度量資料 7 天並每週收集資料，以便月底進行分析。 另一個選項是保留最近 30 天的度量資料，並在 30 天期間的結尾收集和分析資料。

如需啟用、收集和檢視度量資料的詳細資訊，請參閱[啟用 Azure 儲存體度量和檢視度量資料](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 就如同一般使用者資料，儲存、存取和下載分析資料也需付費。

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>利用使用度量來估計成本

### <a name="storage-costs"></a>儲存成本

容量度量資料表 $MetricsCapacityBlob 中具有資料列索引鍵 'data' 的最新項目會顯示使用者資料所耗用的儲存體容量。 容量度量資料表 $MetricsCapacityBlob 中具有資料列索引鍵 'analytics' 的最新項目會顯示分析記錄檔所耗用的儲存體容量。

使用者資料和分析記錄檔 (若已啟用) 所耗用的總容量便可用來估計在儲存體帳戶中儲存資料的成本。 相同的方法也可用來估計一般用途儲存體帳戶中區塊和附加 blob 的儲存成本。

### <a name="transaction-costs"></a>交易成本

交易度量資料表中 API 的所有項目的 'TotalBillableRequests' 總和，會指出該特定 API 的交易總數。 例如，在給定期間的 'GetBlob' 交易總數計算方式為將所有包含 'user;GetBlob'列索引鍵的可計費要求總數進行加總。

若要估計 Blob 儲存體帳戶的交易成本，您必須將交易細分成三個群組，因為它們的定價方式不同。

* 寫入 'PutBlob'、'PutBlock'、'PutBlockList'、'AppendBlock'、'ListBlobs'、'ListContainers'、'CreateContainer'、'SnapshotBlob' 和 'CopyBlob' 等交易。
* 刪除 'DeleteBlob' 和 'DeleteContainer' 等交易。
* 所有其他交易

若要估計一般用途儲存體帳戶的交易成本，不論作業/API 為何，您必須彙總所有的交易。

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>資料存取和異地複寫資料傳輸費用

雖然儲存體分析不會提供讀取自和寫入至儲存體帳戶的資料量，但可藉由查看交易度量資料表大致估算。 交易度量資料表中 API 的所有項目的 'TotalIngress'  總和，會指出該特定 API 的輸入資料總量 (以位元組為單位)。 同樣地，'TotalEgress'  的總和會指出輸出資料總數 (以位元組為單位)。

若要估計 Blob 儲存體帳戶的資料存取成本，您必須將交易細分成兩個群組。

* 查看主要 'GetBlob' 和 'CopyBlob' 作業的 'TotalEgress' 總和，可以估計從儲存體帳戶擷取的資料量。

* 查看主要 'PutBlob'、'PutBlock'、'CopyBlob' 和 'AppendBlock' 作業的 'TotalIngress' 總和，可以估計寫入至儲存體帳戶的資料量。

使用 GRS 或 RA-GRS 儲存體帳戶時，使用寫入的資料量估計值，也可以計算 Blob 儲存體帳戶的異地複寫資料傳輸成本。

> [!NOTE]
> 關於計算使用經常性存取或非經常性存取儲存層的成本，如需更詳細的範例，請查看標題為「經常性存取或非經常性存取層是什麼，以及如何判斷要使用哪一個？」的常見問題 於 [Azure 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/)。

## <a name="migrating-existing-data"></a>移轉現有的資料

Blob 儲存體帳戶專門用於儲存區塊和附加 Blob。 現有的一般用途儲存體帳戶 (允許您儲存資料表、佇列、檔案、磁碟以及 Blob) 無法轉換為 Blob 儲存體帳戶。 若要使用儲存層，您必須建立新的 Blob 儲存體帳戶，並將現有的資料移轉至新建立的帳戶。

您可以使用下列方法，將現有的資料從內部部署儲存體裝置、第三方雲端儲存體提供者，或 Azure 中現有的一般用途儲存體帳戶移轉至 Blob 儲存體帳戶：

### <a name="azcopy"></a>AzCopy

AzCopy 為 Windows 命令列公用程式，可以極高效能將資料複製到 Azure 儲存體，以及從 Azure 儲存體複製資料。 您可以使用 AzCopy 將資料從現有一般用途的儲存體帳戶複製到 Blob 儲存體帳戶中，或將資料從內部部署儲存體裝置上傳至 Blob 儲存體帳戶。

如需詳細資訊，請參閱 [使用 AzCopy 命令列公用程式傳輸資料](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="data-movement-library"></a>資料移動程式庫

適用於 .NET 的 Azure 儲存體資料移動程式庫是以支援 AzCopy 的核心資料移動架構為基礎。 此程式庫是針對類似於 AzCopy 的高效能、可靠且簡單的資料傳輸作業而設計的。 這可讓您充分受惠於 AzCopy 在您的應用程式中以原生方式提供的功能，而無需處理 AzCopy 外部執行個體的執行和監視。

如需詳細資訊，請參閱 [適用於 .Net 的 Azure 儲存體資料移動程式庫](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API 或用戶端程式庫

您可以建立自訂應用程式，以使用其中一個 Azure 用戶端程式庫或 Azure 儲存體服務 REST API，將您的資料移轉至 Blob 儲存體帳戶。 Azure 儲存體針對以下多種語言和平台提供豐富的用戶端程式庫：例如 .NET、Java、C++、Node.JS、PHP、Ruby 和 Python。 這些用戶端程式庫提供多種進階功能，例如大重試邏輯、記錄與並行上傳等等。 您也可以直接透過 REST API 開發，它可以透過提出 HTTP/HTTPS 要求的任何語言進行呼叫。

如需詳細資訊，請參閱 [開始使用 Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md)。

> [!NOTE]
> 使用用戶端加密來加密的 Blob 會儲存利用 Blob 儲存的加密相關中繼資料。 任何複製機制絕對要能夠確保保留 Blob 中繼資料 (尤其是加密相關中繼資料)。 如果您複製不含此中繼資料的 Blob，便無法再次擷取 Blob 內容。 如需有關加密相關中繼資料的詳細資訊，請參閱 [Azure 儲存體用戶端加密](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="faq"></a>常見問題集

1. **現有的儲存體帳戶是否仍然可用？**

    是，現有的儲存體帳戶仍然可用，且價格或功能不變。  它們並沒有選擇儲存層的能力，所以未來不會有分層功能。

2. **為何和何時應該開始使用 Blob 儲存體帳戶？**

    Blob 儲存體帳戶專門用於儲存 Blob，並引進以 Blob 為中心的新功能。 從現在開始，Blob 儲存體帳戶是儲存 Blob 的建議方式，因為將會根據此帳戶類型引進未來的功能，例如階層式儲存體和分層功能。 不過，您可以根據您的業務需求決定何時移轉。

3. **是否可以將現有的儲存體帳戶轉換成 Blob 儲存體帳戶？**

    否。 Blob 儲存體帳戶是不同種類的儲存體帳戶，您必須建立新的並如先前所述移轉您的資料。

4. **可以在相同帳戶中的這兩個儲存層中儲存物件嗎？**

    是。 設定於帳戶層級的 [存取層] 屬性是套用至該帳戶中所有物件的預設層 (沒有明確設定的層)。 不過，blob 層級的階層處理 (預覽) 功能可讓您在物件層級上設定存取層，而不管帳戶上設定的存取層為何。 三個儲存層 (經常性存取、非經常性存取或封存) 中的 blob 都可能共存於相同的帳戶中。

5. **是否可以在 Blob 儲存體帳戶上變更儲存層？**

    是，在儲存體帳戶上設定 [存取層] 屬性，即可變更儲存層。 變更儲存層會套用到帳戶中儲存之所有未設定明確儲存層的物件。 將儲存層從經常性存取變更為非經常性存取時，會產生寫入作業 (每 10,000 個) 和資料寫入 (每 GB) 費用 (僅限 Blob 儲存體帳戶)，而從非經常性存取變更為經常性存取時，則會產生讀取作業 (每 10,000 個) 和資料擷取 (每 GB) 費用，以供讀取帳戶中的所有資料。

6. **可以變更 Blob 儲存體帳戶的儲存層的頻率為何？**

    雖然我們不會強制執行可以變更儲存層的頻率限制，但請留意，將儲存層從非經常性存取變更為經常性存取會產生明顯的費用。 不建議經常變更儲存層。

7. **非經常性存取儲存層中的 Blob 與經常性存取儲存層中的 Blob 的行為有所不同嗎？**

    經常性存取儲存層中的 Blob 與一般用途儲存體帳戶中的 Blob 有相同的延遲。 非經常性存取儲存層中的 Blob 與一般用途儲存體帳戶中的 Blob 有類似的延遲 (以毫秒為單位)。 封存儲存層中的 blob 會有好幾個小時的延遲。

    相較於經常性存取儲存層中儲存的 Blob，非經常性存取儲存層中的 Blob 有稍微較低的可用性服務等級 (SLA)。 如需詳細資訊，請參閱 [儲存體的 SLA](https://azure.microsoft.com/support/legal/sla/storage)。

8. **可以將分頁 Blob 和虛擬機器磁碟儲存在 Blob 儲存體帳戶嗎？**

    Blob 儲存體帳戶僅支援區塊和附加 Blob，不支援分頁 Blob。 Azure 虛擬機器磁碟是由分頁 Blob 支援，因此 Blob 儲存體帳戶無法用來儲存虛擬機器磁碟。 不過，可以將虛擬機器磁碟的備份儲存為 Blob 儲存體帳戶中的區塊 Blob。

9. **是否需要將現有的應用程式變更成使用 Blob 儲存體帳戶？**

    對區塊和附加 Blob 而言，Blob 儲存體帳戶與一般用途儲存體帳戶的 API 完全一致。 只要您的應用程式使用區塊 Blob 或附加 Blob，而且您使用 2014-02-14 版或更高版本的[儲存體服務 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx)，您的應用程式就應該能運作。 如果您使用舊版的通訊協定，則必須將應用程式更新成使用新的版本，才能完美地搭配這兩種類型的儲存體帳戶運作。 一般而言，不論您使用哪個儲存體帳戶類型，我們都會建議使用最新版本。

10. **使用者經驗是否有所改變？**

    儲存區塊和附加 Blob 時，Blob 儲存體帳戶非常類似於一般用途的儲存體帳戶，並支援 Azure 儲存體的所有重要功能，包括高持久性和可用性、延展性、效能和安全性。 除了 Blob 儲存體帳戶特有的功能和限制及其上面所列的儲存層以外，其他一切均維持不變。

## <a name="next-steps"></a>後續步驟

### <a name="evaluate-blob-storage-accounts"></a>評估 Blob 儲存體帳戶

[檢查各區域的 Blob 儲存體帳戶可用性](https://azure.microsoft.com/regions/#services)

[啟用 Azure 儲存體計量以評估您目前的儲存體帳戶使用量](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[檢查各區域的 Blob 儲存體價格](https://azure.microsoft.com/pricing/details/storage/)

[檢查資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>開始使用 Blob 儲存體帳戶

[開始使用 Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md)

[從 Azure 儲存體來回移動資料](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[使用 AzCopy 命令列公用程式傳輸資料](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[瀏覽及探索您的儲存體帳戶](http://storageexplorer.com/)
