---
title: "Azure Container Instances 地區與資源可用性"
description: "探索哪些 Azure 地區支援部署容器執行個體，以及這些執行個體的 CPU 和記憶體限制。"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 08/31/2017
ms.author: marsma
ms.openlocfilehash: ace4eb6b284f2c1b2caeb54c1d686e68cacb1725
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Azure Container Instances 的地區可用性

在預覽期間，我們會在下列地區發行具有指定 CPU 和記憶體限制的 Azure Container Instances。

| 位置 | 作業系統 | CPU | 記憶體 (GB) |
| -------- | -- | :---: | :-----------: |
| 西歐、美國西部、美國東部 | Linux | 2 | 7 |
| 西歐、美國西部、美國東部 | Windows | 2 | 3.5 |

## <a name="resource-availability"></a>資源可用性

在這些資源限制下建立的容器執行個體都會受限於部署地區的可用性。 地區負載過重時，您在部署執行個體時可能會失敗。

若要減少這類的部署失敗，請嘗試部署 CPU 和記憶體設定較低階的執行個體，或過一段時間再部署。

## <a name="next-steps"></a>後續步驟

如需有關為容器執行個體部署疑難排解的詳細資訊，請參閱[為 Azure Container Instances 的部署問題疑難排解](container-instances-troubleshooting.md)。