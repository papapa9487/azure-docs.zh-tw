---
title: Azure Container Registry SKU
description: "Azure Container Registry 中可用之不同服務層之間的比較"
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 10/16/2017
ms.author: stevelas
ms.openlocfilehash: cf7724dd9e3e870cfd7ec0b5d2e4ea1d0694e9de
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

Azure Container Registry (ACR) 具有多個服務層 (稱為SKU)。 這些 SKU 提供可預測的定價，以及幾個選項，讓您指定要如何在 Azure 中使用私用 Docker 登錄。 選擇較高層級的 SKU 可提供更多的效能和延展性。 不過，所有 SKU 都提供相同的程式設計功能，讓開發人員開始使用「基本」，並在登錄使用增加時轉換為「標準」和「進階」。

## <a name="basic"></a>基本
適用於正在學習 Azure Container Registry 之開發人員的成本最佳化進入點。 「基本」登錄具有與「標準」和「進階」相同的程式設計功能 (Azure Active Directory 驗證整合、映像刪除和 Webhook)；不過，它具有大小和使用方式上的條件約束。

## <a name="standard"></a>標準
「標準」登錄提供與「基本」相同的功能，並且提高儲存體限制和映像輸送量。 「標準」登錄應該能滿足大部分實際執行案例的需求。

## <a name="premium"></a>進階
「進階」登錄提供較高的條件約束限制 (例如儲存體和並行作業)，以啟用大量案例。 除了更高的映像輸送量容量之外，「進階」也會新增[異地複寫](container-registry-geo-replication.md)這類功能，來管理多個區域的單一登錄，並維護每個部署的網路關閉登錄。

## <a name="classic"></a>傳統
「傳統」登錄 SKU 已啟用 Azure 中 Azure Container Registry 服務的初始版本。 Azure 在訂用帳戶中建立的儲存體帳戶支援「傳統」登錄，這會限制 ACR 提供更高層級功能的能力，例如提高輸送量和異地複寫。 其功能有限，因此我們計劃在未來取代「傳統 SKU」。

> [!NOTE]
> 因為計劃取代「傳統」登錄 SKU，所以建議所有新登錄都使用「基本」、「標準」或「進階」。 如需轉換現有「傳統」登錄的資訊，請參閱[變更 SKU](#changing-skus)。
>

## <a name="registry-sku-feature-matrix"></a>登錄 SKU 功能矩陣

下表詳述「基本」、「標準」和「進階」服務層的功能和限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="manage-registry-size"></a>管理登錄大小
每個 SKU 的儲存體條件約束都要符合典型案例：「基本」可用於開始使用、「標準」可用於大部分的實際執行應用程式，而「進階」可進行大規模效能和[異地複寫](container-registry-geo-replication.md)。 在整個登錄生命週期，您應該定期刪除未使用的內容來管理其大小。

您可以在 Azure 入口網站的容器登錄 [概觀] 中找到登錄的目前使用量：

![Azure 入口網站中的登錄使用量資訊](media/container-registry-skus/registry-overview-quotas.png)

您可以刪除 Azure 入口網站中的存放庫來管理登錄大小。

在 [服務] 下方，選取 [存放庫]，並以滑鼠右鍵按一下您想要刪除的存放庫，然後選取 [刪除]。

![在 Azure 入口網站中刪除存放庫](media/container-registry-skus/delete-repository-portal.png)

## <a name="changing-skus"></a>變更 SKU

您可以在 Azure 入口網站中變更登錄的 SKU。

在 Azure 入口網站的登錄 [概觀] 中，選取 [更新]，然後從 SKU 下拉式清單中選取新的 **SKU**。

![在 Azure 入口網站中更新容器登錄 SKU](media/container-registry-skus/update-registry-sku.png)

## <a name="changing-from-classic"></a>從傳統變更
當您將「傳統」登錄變更為「基本」、「標準」或「進階」時，Azure 會將現有容器映像從您訂用帳戶中的相關聯儲存體帳戶複製至 Azure 所管理的儲存體帳戶。 此程序需要一些時間。

在轉換期間，`docker pull` 會持續運作；不過，在轉換完成之前，會封鎖 `docker push`。

完成之後，ACR 不會再使用訂用帳戶儲存體帳戶。

### <a name="why-change-from-classic-to-basic-standard-or-premium"></a>為什麼將「傳統」變更為「基本」、「標準」或「進階」？

因為「傳統」登錄的功能有限，所以建議您將「傳統」登錄變更為「基本」、「標準」或「進階」層。 這些較高層級的 SKU 會將登錄更深入整合到 Azure 功能。 其中一些功能包含：

* Azure Active Directory 整合以進行個別驗證 (請參閱 [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login))
* 映像和標記刪除支援
* [異地複寫](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

「傳統」登錄大部分取決於您在建立登錄時，Azure 自動佈建在您 Azure 訂用帳戶中的儲存體帳戶。 相反地，「基本」、「標準」和「進階」SKU 會利用 *Managed 儲存體*。 也就是說，Azure 會明確地管理映像儲存體；在您自己的訂用帳戶中，不會建立不同的儲存體帳戶。

「基本」、「標準」和「進階」登錄所提供之 Managed 儲存體的一些優點：

* 容器映像是進行[待用加密](../storage/common/storage-service-encryption.md)。
* 映像是使用[異地備援儲存體](../storage/common/storage-redundancy.md#geo-redundant-storage)所儲存，並確保使用多區域複寫來備份映像。
* [在 SKU 之間移動](#changing-skus)的能力，並在您選擇較高層級的 SKU 時啟用較高的輸送量。 運用每個 SKU，ACR 即可在您的需求增加時滿足輸送量需求。 ACR 如何達成所需輸送量的基礎實作表示為「意圖」(透過選取較高的 SKU)，而您不需要管理實作的詳細資料。

## <a name="pricing"></a>價格

如需每個 Azure Container Registry SKU 的定價資訊，請參閱 [Container Registry 定價](https://azure.microsoft.com/pricing/details/container-registry/)。

## <a name="next-steps"></a>後續步驟

**Azure Container Registry 藍圖**

請瀏覽 GitHub 上的 [ACR Roadmap](https://aka.ms/acr/roadmap) ACR 藍圖)，以尋找服務中即將推出功能的相關資訊。

**Azure Container Registry UserVoice**

送出並票選 [ACR UserVoice](https://feedback.azure.com/forums/903958-azure-container-registry) 中的新功能建議。