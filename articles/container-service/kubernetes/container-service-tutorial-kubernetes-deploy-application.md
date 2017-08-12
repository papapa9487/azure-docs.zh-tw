---
title: "Azure Container Service 教學課程 - 部署應用程式 | Microsoft Docs"
description: "Azure Container Service 教學課程 - 部署應用程式"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服務, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 81a6a3d5364642b2da75faf875d64d2f4a1939d4
ms.contentlocale: zh-tw
ms.lasthandoff: 07/25/2017

---

# <a name="run-applications-in-kubernetes"></a>在 Kubernetes 中執行應用程式

在本教學課程 (七個章節的第四部分) 中，已在 Kubernetes 叢集中部署一個應用程式範例。 完成的步驟包括：

> [!div class="checklist"]
> * 下載 Kubernetes 資訊清單檔
> * 在 Kubernetes 中執行應用程式
> * 測試應用程式

在後續的教學課程中，會相應放大、更新此應用程式，且會將 Operations Management Suite 設定為監視 Kubernetes 叢集。

本教學課程假設讀者已了解 Kubernetes 的基本概念，如需 Kubernetes 的詳細資訊，請參閱 [Kubernetes 文件](https://kubernetes.io/docs/home/)。

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已將應用程式封裝成容器映像、將此映像上傳至 Azure Container Registry，並已建立 Kubernetes 叢集。 如果您尚未完成這些步驟，而想要跟著做，請回到[教學課程 1 – 建立容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。 

本教學課程至少需要一個 Kubernetes 叢集。

## <a name="get-manifest-file"></a>取得資訊清單檔

針對此教學課程，會使用 Kubernetes 資訊清單來部署 [Kubernetes 物件](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)。 Kubernetes 資訊清單為 YAML 或 JSON 格式的檔案，包含 Kubernetes 物件部署和設定指示。

Azure Vote 應用程式存放庫中提供了本教學課程中的應用程式資訊清單檔，這是在上一個教學課程中複製的存放庫。 如果您尚未這麼做，請使用下列命令來複製存放庫： 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

在所複製之存放庫的下列目錄底下，即可找到資訊清單檔。

```bash
/azure-voting-app-redis/kubernetes-manifests/
```

## <a name="update-manifest-file"></a>更新資訊清單檔

如果是使用 Azure 容器登錄來儲存容器映像，必須使用 ACR loginServer 名稱來更新資訊清單。

請使用 [az acr list](/cli/azure/acr#list) 命令來取得 ACR 登入伺服器名稱。

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

已使用 microsoft 的存放庫名稱預先建立範例資訊清單。 使用任何文字編輯器開啟檔案，並將 microsoft 值取代為您 ACR 執行個體的登入伺服器名稱。

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

## <a name="deploy-application"></a>部署應用程式

使用 [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) 命令來執行應用程式。 此命令會剖析資訊清單檔，並建立已定義的 Kubernetes 物件。

```azurecli-interactive
kubectl create -f ./azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yaml
```

輸出：

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>測試應用程式

已建立 [Kubernetes 服務](https://kubernetes.io/docs/concepts/services-networking/service/)，會將應用程式公開至網際網路。 此程序需要數分鐘的時間。 

若要監視進度，請使用 [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) 命令搭配 `--watch` 引數。

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

一開始，azure-vote-front 服務的 **EXTERNAL-IP** 會顯示為 pending。 當 EXTERNAL-IP 位址從 *pending* 變成一個「IP 位址」之後，請使用 `CTRL-C` 來停止 kubectl 監看式流程。

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

若要查看應用程式，請瀏覽至外部 IP 位址。

![Azure 上 Kubernetes 叢集的影像](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，是將 Azure 投票應用程式部署到 Azure Container Service Kubernetes 叢集。 完成的工作包括：  

> [!div class="checklist"]
> * 下載 Kubernetes 資訊清單檔
> * 在 Kubernetes 中執行應用程式
> * 測試應用程式

請前往下一個教學課程，以了解如何調整 Kubernetes 應用程式和基礎 Kubernetes 基礎架構。 

> [!div class="nextstepaction"]
> [調整 Kubernetes 應用程式和基礎結構](./container-service-tutorial-kubernetes-scale.md)
