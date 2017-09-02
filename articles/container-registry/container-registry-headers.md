---
title: "Azure 容器登錄存放庫 | Microsoft Docs"
description: "如何使用 Docker 映像的 Azure 容器登錄存放庫"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: dd4feff057269ed7106990bb63eed7fcffa2dbec
ms.contentlocale: zh-tw
ms.lasthandoff: 06/10/2017

---
# <a name="azure-container-registry-repositories"></a>Azure 容器登錄存放庫

Azure Container Registry 與許多服務和 Orchestrator 相容。 為了更輕鬆地追蹤在其中使用 ACR 的來源服務及代理程式，我們已開始在 Docker.config 檔案中使用 Docker 標頭欄位。



## <a name="viewing-repositories-in-the-portal"></a>在入口網站中檢視存放庫

ACR 標頭遵循下列格式：
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* 雲端：Azure、Azure Stack 或是其他政府或國家/地區特定的 Azure 雲端。 雖然目前不支援 Azure Stack 和政府雲端，此參數未來可允許支援。
* 服務：服務的名稱。
* Optionalservicename：具有子服務的服務選擇性參數，或指定 SKU (例如：Web 應用程式與 Azure/app-service/web-apps 對應)。

建議夥伴服務和 Orchestrator 使用特定的標頭值，以協助我們的遙測。 使用者也可以修改傳遞至標頭的值 (如需要)。

我們需要 ACR 夥伴用來填入 "X-Meta-Source-Client" 欄位的值如下所示：

| 服務名稱              | 頁首                                |
| ------------------------- | ------------------------------------- |
| Azure Container Service   | azure/compute/azure-container-service |
| App Service - Web Apps    | azure/app-service/web-apps            |
| App Service - Logic Apps  | azure/app-service/logic-apps          |
| 批次                     | azure/compute/batch                   |
| 雲端主控台             | azure/cloud-console                   |
| Functions                 | azure/compute/functions               |
| 物聯網 - 中樞  | azure/iot/hub                         |
| HDInsight                 | azure/data/hdinsight                  |
| Jenkins                   | azure/jenkins                         |
| 機器學習服務          | azure/data/machile-learning           |
| Service Fabric            | azure/compute/service-fabric          |
| VSTS                      | azure/vsts                            |


## <a name="next-steps"></a>後續步驟
[深入了解登錄和支援的服務與 Orchestrator](container-registry-intro.md)

