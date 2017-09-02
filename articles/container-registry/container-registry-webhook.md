---
title: Azure Container Registry Webhook | Microsoft Docs
description: Azure Container Registry Webhook
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acr, azure-container-registry
keywords: "Docker、容器、ACR"
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: d0190f5725671c320d92b897f0dcef7a526a86e3
ms.contentlocale: zh-tw
ms.lasthandoff: 07/12/2017

---

# <a name="using-azure-container-registry-webhooks---azure-portal"></a>使用 Azure Container Registry Webhook - Azure 入口網站

Azure Container Registry 會儲存和管理私人 Docker 容器映像，其方式類似於 Docker 中樞儲存公用 Docker 映像。 在其中一個登錄存放庫發生特定動作時，您要使用 Webhook 來觸發事件。 Webhook 可以回應登錄層級的事件，或可將範圍縮小至特定的存放庫標記。 

如需相關背景和概念，請參閱[概觀](./container-registry-intro.md)。

## <a name="prerequisites"></a>必要條件 

- Azure 容器管理的登錄 - 在 Azure 訂用帳戶中建立受管理的容器登錄。 例如，使用 Azure 入口網站或 Azure CLI 2.0。 
- Docker CLI - 若要將您的本機電腦設定為 Docker 主機並存取 Docker CLI 命令，請安裝 Docker 引擎。 

## <a name="create-webhook-azure-portal"></a>建立 Webhook Azure 入口網站

1. 登入 Azure 入口網站並瀏覽至您要在當中建立 Webhook 的登錄。 

2. 在 [容器] 刀鋒視窗中，選取 [Webhook] 索引標籤。 

3. 從 [Webhook] 刀鋒視窗工具列上選取 [新增]。 

4. 填入下列資訊以完成 [建立 Webhook] 表單：

| 值 | 描述 |
|---|---|
| 名稱 | 您想要提供給 Webhook 的名稱。 它只能包含小寫字母和數字，且必須為 5-50 個字元之間。 |
| 服務 URI | Webhook 需在當中傳送 POST 通知的 URI。 |
| 自訂標頭 | 您想要與 POST 要求一起傳遞的標頭。 它們應該為「金鑰：值」的格式。 |
| 觸發程序動作 | 觸發 Webhook 的動作。 目前可藉由推送及/或刪除至映像的動作來觸發 Webhook。 |
| 狀態 | Webhook 建立之後的狀態。 此選項預設為啟用狀態。 |
| Scope | Webhook 的運作範圍。 根據預設，範圍是登錄中的所有事件。 可以使用「存放庫：標記」格式，針對存放庫或標記加以指定。 |

範例 Webhook 表單：

![DCOS UI](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>建立 Webhook Azure CLI

若要使用 Azure CLI 建立 Webhook，請使用 [az acr webhook create](/cli/azure/acr/webhook#create) 命令。

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>測試 Webhook

### <a name="azure-portal"></a>Azure 入口網站

在容器映像上使用 Webhook 推送和刪除動作之前，可以使用 [偵測] 按鈕進行測試。 使用時，[偵測] 會將泛型 POST 要求傳送至指定的端點，並記錄回應。 這對於確認 Webhook 是否已設定正確是很有幫助的。

1. 選取您想要測試的 Webhook。 
2. 在頂端工具列中，選取 [偵測] 動作。 
3. 檢查要求和回應。

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 測試 ACR Webhook，請使用 [az acr webhook ping](/cli/azure/acr/webhook#ping) 命令。

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

若要查看結果，請使用 [az acr webhook list-events](/cli/azure/acr/webhook#list-events) 命令。 

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>刪除 Webhook

### <a name="azure-portal"></a>Azure 入口網站

依序選取 [Webhook] 以及 Azure 入口網站上的 [刪除] 按鈕，就可以將每個 Webhook 刪除。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```
