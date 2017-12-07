---
title: "Azure Container Registry 中的最佳做法"
description: "了解如何依照這些最佳做法來有效地使用 Azure Container Registry。"
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 11/05/2017
ms.author: marsma
ms.openlocfilehash: 5ccbb3022dc38f13eed9b5aa24beb14dfdb3b5b6
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="best-practices-for-azure-container-registry"></a>Azure Container Registry 的最佳做法

藉由依照這些最佳做法進行操作，您將可協助發揮最大效能，以及以符合成本效益的方式在 Azure 中使用您的私人 Docker 登錄。

## <a name="network-close-deployment"></a>網路鄰近部署

在您部署容器的相同 Azure 區域中建立容器登錄。 將登錄放在於網路上鄰近您容器主機的區域有助於降低延遲和成本。

網路鄰近部署是使用私人容器登錄的主要原因之一。 Docker 映像擁有一個龐大的[分層建構](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) \(英文\)，可允許您進行增量部署。 不過，新節點必須提取指定之映象所需的所有層級。 這個初始的 `docker pull` 可快速加總達數 GB。 讓私人登錄鄰近您的部署可將網路延遲降到最低。
此外，所有公用雲端 (包括 Azure) 都實作網路輸出費用。 將映像從一個資料中心提取到另一個資料中心除了增添延遲之外，也會增添網路輸出費用。

## <a name="geo-replicate-multi-region-deployments"></a>異地複寫多區域部署

如果您要將容器部署到多個區域，請使用 Azure Container Registry 的[異地複寫](container-registry-geo-replication.md)功能。 不論您是要從區域資料中心為全球客戶提供服務，還是您的開發小組位於不同位置，您都可以藉由異地複寫登錄來簡化登錄管理並將延遲降到最低。 目前在預覽版中，此功能是[進階](container-registry-skus.md#premium)登錄提供的功能。

若要了解如何使用異地複寫，請參閱由三部分組成的教學課程 [Azure Container Registry 中的異地複寫](container-registry-tutorial-prepare-registry.md)。

## <a name="repository-namespaces"></a>儲存機制命名空間

您可以利用儲存機制命名空間，來允許在組織內的多個群組之間共用單一登錄。 登錄可以在各個部署與小組之間共用。 Azure Container Registry 支援巢狀命名空間，可讓您進行群組隔離。

例如，請思考一下下列容器映像標記。 全公司使用的映象 (例如 `aspnetcore`) 會放在根命名空間中，而「生產」及「行銷」群組所擁有的容器映像則會各自使用自己的命名空間。

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>專用資源群組

由於容器登錄是跨多個容器主機使用的資源，因此登錄應該位於自己的資源群組中。

雖然您可以對特定主機類型 (例如「Azure 容器執行個體」) 進行實驗，但完成後，您可能會想要刪除該容器執行個體。 不過，您也可能會想要保留已推送到 Azure Container Registry 的映像集合。 藉由將登錄放在它自己的資源群組中，即可將刪除容器執行個體資源群組時意外刪除該登錄中映像集合的風險降到最低。

## <a name="authentication"></a>驗證

向 Azure Container Registry 進行驗證時，有兩種主要的案例：個人驗證和服務 (或「遠端控制」) 驗證。 下表提供這些案例的簡要概觀，以及各個案例的建議驗證方法。

| 類型 | 範例案例 | 建議的方法 |
|---|---|---|
| 個人身分識別 | 將映像提取至其開發電腦或從該電腦提取映像的開發人員。 | [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| 遠端控制/服務身分識別 | 未直接涉及使用者的組建和部署管線。 | [服務主體](container-registry-authentication.md#service-principal) |

如需有關 Azure Container Registry 驗證的深入資訊，請參閱[向 Azure Container Registry 進行驗證](container-registry-authentication.md)。

## <a name="next-steps"></a>後續步驟

在數個個別提供不同功能的層 (稱為 SKU) 中都有提供 Azure Container Registry。 如需有關可用 SKU 的詳細資料，請參閱 [Azure Container Registry SKU](container-registry-skus.md)。