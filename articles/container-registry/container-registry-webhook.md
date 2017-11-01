---
title: Azure Container Registry Webhook
description: "了解如何在您的登錄存放庫發生特定動作時，使用 Webhook 來觸發事件。"
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
ms.date: 10/08/2017
ms.author: nepeters
ms.openlocfilehash: 5a9dab91aafb92f944b473f05144242143e36477
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2017
---
# <a name="using-azure-container-registry-webhooks"></a>使用 Azure Container Registry Webhook

Azure Container Registry 會儲存和管理私人 Docker 容器映像，其方式類似於 Docker 中樞儲存公用 Docker 映像。 在其中一個登錄存放庫發生特定動作時，您要使用 Webhook 來觸發事件。 Webhook 可以回應登錄層級的事件，或可將範圍縮小至特定的存放庫標記。

如需詳細背景和概念，請參閱[什麼是 Azure Container Registry？](./container-registry-intro.md)。

## <a name="prerequisites"></a>必要條件

- Azure 容器管理的登錄 - 在 Azure 訂用帳戶中建立受管理的容器登錄。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
- Docker CLI - 若要將您的本機電腦設定為 Docker 主機並存取 Docker CLI 命令，請安裝 [Docker 引擎](https://docs.docker.com/engine/installation/)。

## <a name="create-webhook-azure-portal"></a>建立 Webhook Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)並瀏覽至您要在當中建立 Webhook 的登錄。

2. 在 [容器] 刀鋒視窗中，選取 [服務] 底下的 [Webhook] 索引標籤。

3. 選取 [Webhook] 刀鋒視窗工具列中的 [新增]。

4. 填入下列資訊以完成 [建立 Webhook] 表單：

| 值 | 描述 |
|---|---|
| 名稱 | 您想要提供給 Webhook 的名稱。 它只能包含小寫字母和數字，且長度必須介於 5-50 個字元。 |
| 服務 URI | Webhook 需在當中傳送 POST 通知的 URI。 |
| 自訂標頭 | 您想要與 POST 要求一起傳遞的標頭。 它們應該為「金鑰：值」的格式。 |
| 觸發程序動作 | 觸發 Webhook 的動作。 可藉由推送映像及/或刪除動作來觸發 Webhook。 |
| 狀態 | Webhook 建立之後的狀態。 此選項預設為啟用狀態。 |
| Scope | Webhook 的運作範圍。 根據預設，範圍是登錄中的所有事件。 可以使用「存放庫：標記」格式，針對存放庫或標記加以指定。 |

範例 Webhook 表單：

![在 Azure 入口網站中建立 ACR Webhook](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>建立 Webhook Azure CLI

若要使用 Azure CLI 建立 Webhook，請使用 [az acr webhook create](/cli/azure/acr/webhook#create) 命令。

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>測試 Webhook

### <a name="azure-portal"></a>Azure 入口網站

在容器映像上使用 Webhook 推送和刪除動作之前，可以使用 [Ping] 按鈕測試它。 Ping 會將泛型 POST 要求傳送至指定的端點，並記錄回應。 這可協助您確認您已正確設定 Webhook。

1. 選取您想要測試的 Webhook。
2. 在頂端工具列中，選取 [Ping]。
3. 檢查 HTTP STATUS 資料行中的端點回應。

![在 Azure 入口網站中建立 ACR Webhook](./media/container-registry-webhook/webhook-02.png)

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

依序選取 Azure 入口網站上的 [Webhook] 和 [刪除] 按鈕，就可以將每個 Webhook 刪除。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```
