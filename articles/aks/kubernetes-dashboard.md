---
title: "使用 Web UI 管理 Azure Kubernetes 叢集"
description: "使用 AKS 中的 Kubernetes 儀表板"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 75ee9cc7c4b40e9a724c8ce98d770a18ae654096
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Kubernetes 儀表板和 Azure Container Service (AKS)

您可以使用 Azure CLI 啟動 Kubernetes 儀表板。 本文件會逐步示範以 Azure CLI 啟動 Kubernetes 儀表板，以及逐步說明某些基本的儀表板作業。 如需 Kubernetes 儀表板的詳細資訊，請參閱 [Kubernetes Web UI 儀表板](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)。

## <a name="before-you-begin"></a>開始之前

本文件中詳述的步驟假設您已建立 AKS 叢集，並建立與叢集的 kubectl 連線。 如果您需要這些項目，請參閱 [AKS 快速入門](./kubernetes-walkthrough.md)。

您也必須安裝和設定 Azure CLI 版本 2.0.21 或更新版本。 執行 az --version 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="start-kubernetes-dashboard"></a>啟動 Kubernetes 儀表板

使用 `az aks browse` 命令啟動 Kubernetes 儀表板。 執行此命令時，會取代資源群組與叢集名稱。

```azurecli
az aks browse --resource-group myResourceGroup --name myK8SCluster
```

此命令會在您的開發系統與 Kubernetes API 之間建立 Proxy，並開啟 Kubernetes 儀表板的網頁瀏覽器。

## <a name="run-an-application"></a>執行應用程式

在 Kubernetes 儀表板中，按一下右上方視窗中的 [建立] 按鈕。 將部署命名為 `nginx`，並針對映像名稱輸入 `nginx:latest`。 在 [服務] 下選取 [外部]，針對連接埠和目標連接埠輸入 `80`。

準備就緒時，按一下 [部署] 建立部署。

![Kubernetes 服務建立對話方塊](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>檢視應用程式

在 Kubernetes 儀表板上可以看到應用程式的狀態。 只要應用程式在執行中，每個元件旁就會有綠色的核取方塊。

![Kubernetes Pods](./media/container-service-kubernetes-ui/complete-deployment.png)

若要查看應用程式 Pod 的詳細資訊，請按一下左側功能表中的 **Pod**，然後選取 **NGINX** Pod。 這裡可以看到 Pod 的特定資訊，例如資源耗用量。

![Kubernetes 資源](./media/container-service-kubernetes-ui/running-pods.png)

若要尋找應用程式的 IP 位址，請按一下左側功能表中的 [服務]，然後選取 **NGINX** 服務。

![nginx 檢視](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>編輯應用程式

除建立和檢視應用程式之外，Kubernetes 儀表板還可用來編輯和更新應用程式部署。

若要編輯部署，請按一下左側功能表中的 [部署]，然後選取 **NGINX** 部署。 最後，選取右上方導覽列中的 [編輯]。

![Kubernetes 編輯](./media/container-service-kubernetes-ui/view-deployment.png)

找出 `spec.replica` 值，此值應為 1，請將它變更為 3。 如此做，NGINX 部署的複本計數會從 1 增加至 3。

就緒後請選取 [更新]。

![Kubernetes 編輯](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>後續步驟

如需 Kubernetes 儀表板的詳細資訊，請參閱 Kubernetes 文件。

> [!div class="nextstepaction"]
> [Kubernetes Web UI 儀表板](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)