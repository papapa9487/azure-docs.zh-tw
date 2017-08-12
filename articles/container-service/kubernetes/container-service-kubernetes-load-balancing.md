---
title: "針對 Azure 中的 Kubernetes 容器進行負載平衡 | Microsoft Docs"
description: "在 Azure Container Service 中由外部連接並針對 Kubernetes 叢集內的多個容器進行負載平衡。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "容器, 微服務, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: danlep
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: e2ac0d65eea9cea25a2a9cb1f99a35c689eed976
ms.contentlocale: zh-tw
ms.lasthandoff: 07/25/2017

---
# <a name="load-balance-containers-in-a-kubernetes-cluster-in-azure-container-service"></a>在 Azure Container Service 中針對 Kubernetes 叢集內的容器進行負載平衡 
本文將介紹在 Azure Container Service 中 Kubernetes 叢集內進行負載平衡。 負載平衡提供服務可供外部存取的 IP 位址，並將網路流量分散於代理程式 VM 中執行的 Pod 之間。

您可以設定 Kubernetes 服務使用 [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) 來管理外部網路 (TCP) 流量。 透過其他設定，便可以達成 HTTP 或 HTTPS 流量 (或更進階的案例) 的負載平衡和路由。

## <a name="prerequisites"></a>必要條件
* 在 Azure Container Service 中[部署 Kubernetes 叢集](container-service-kubernetes-walkthrough.md)
* [將您的用戶端連接到](../container-service-connect.md)您的叢集

## <a name="azure-load-balancer"></a>Azure Load Balancer

根據預設，在 Azure Container Service 中部署的 Kubernetes 叢集包含代理程式 VM 的網際網路對應 Azure Load Balancer。 (將針對主要 VM 設定個別的負載平衡器資源)。Azure Load Balancer 是第 4 層負載平衡器。 目前，負載平衡器只支援 Kubernetes 中的 TCP 流量。

建立 Kubernetes 服務時，您可以自動設定 Azure Load Balancer 允許存取服務。 若要設定負載平衡器，請將服務 `type` 設定為 `LoadBalancer`。 負載平衡器會建立一個規則，將連入服務流量的公用 IP 位址和連接埠號碼對應到代理程式 VM 中 Pod 的私人 IP 位址和連接埠號碼 (回應流量反之亦然)。 

 下列兩個範例顯示如何將 Kubernetes 服務 `type` 設定為 `LoadBalancer`。 (嘗試這些範例之後，如果您不再需要它們，請刪除部署)。

### <a name="example-use-the-kubectl-expose-command"></a>範例︰使用 `kubectl expose` 命令 
[Kubernetes 逐步解說](container-service-kubernetes-walkthrough.md)包含如何使用 `kubectl expose` 命令與其 `--type=LoadBalancer` 旗標公開服務的範例。 步驟如下：

1. 啟動新的容器部署。 例如，下列命令會啟動稱為 `mynginx` 的新部署。 部署會包含三個以 Nginx Web 伺服器的 Docker 映像為基礎的容器。

    ```console
    kubectl run mynginx --replicas=3 --image nginx
    ```
2. 確認容器正在執行。 例如，如果您使用 `kubectl get pods` 查詢容器，您會看到類似下列的輸出：

    ![取得 Nginx 容器](./media/container-service-kubernetes-load-balancing/nginx-get-pods.png)

3. 若要設定負載平衡器以接受部署的外部流量，請搭配 `--type=LoadBalancer` 執行 `kubectl expose`。 下列命令會在連接埠 80 上公開 Nginx 伺服器：

    ```console
    kubectl expose deployments mynginx --port=80 --type=LoadBalancer
    ```

4. 輸入 `kubectl get svc` 以查看叢集中服務的狀態。 負載平衡器設定規則時，服務的 `EXTERNAL-IP` 會顯示為 `<pending>`。 幾分鐘之後，外部 IP 位址會完成設定： 

    ![設定 Azure Load Balancer](./media/container-service-kubernetes-load-balancing/nginx-external-ip.png)

5. 確認您可以在外部 IP 位址存取服務。 例如，開啟網頁瀏覽器並前往顯示的 IP 位址。 瀏覽器會顯示 Nginx Web 伺服器正在其中一個容器中執行。 或者，執行 `curl` 或 `wget` 命令。 例如：

    ```
    curl 13.82.93.130
    ```

    您應該會看到如下的輸出：

    ![使用 curl 存取 Nginx](./media/container-service-kubernetes-load-balancing/curl-output.png)

6. 若要查看 Azure Load Balancer 的組態，請移至 [Azure 入口網站](https://portal.azure.com)。

7. 瀏覽您容器服務叢集的資源群組，並選取代理程式 VM 的負載平衡器。 其名稱應該與容器服務相同。 (請勿選擇主要節點的負載平衡器。主要節點就是名稱包含 **master-lb** 的節點)。 

    ![資源群組中的負載平衡器](./media/container-service-kubernetes-load-balancing/container-resource-group-portal.png)

8. 若要查看負載平衡器組態的詳細資訊，請按一下 [負載平衡規則] 和已設定規則的名稱。

    ![負載平衡器規則](./media/container-service-kubernetes-load-balancing/load-balancing-rules.png) 

### <a name="example-specify-type-loadbalancer-in-the-service-configuration-file"></a>範例︰在服務組態檔中指定 `type: LoadBalancer`

如果您從 YAML 或 JSON [服務組態檔 (英文)](https://kubernetes.io/docs/user-guide/services/operations/#service-configuration-file)部署 Kubernetes 容器應用程式，請透過將下列一行加入至服務規格中，以指定外部負載平衡器：

```YAML
 "type": "LoadBalancer"
``` 



下列步驟使用 Kubernetes [訪客留言範例 (英文)](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook)。 此範例是以 Redis 和 PHP Docker 映像為基礎的多層式 Web 應用程式。 您可以在服務組態檔中指定前端 PHP 伺服器使用 Azure Load Balancer。

1. 從 [GitHub (英文)](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook/all-in-one) 下載檔案 `guestbook-all-in-one.yaml`。 
2. 瀏覽 `frontend` 服務的 `spec`。
3. 取消註解行 `type: LoadBalancer`。

    ![服務組態中的負載平衡器](./media/container-service-kubernetes-load-balancing/guestbook-frontend-loadbalance.png)

4. 儲存檔案，並執行下列命令以部署應用程式︰

    ```
    kubectl create -f guestbook-all-in-one.yaml
    ```

5. 輸入 `kubectl get svc` 以查看叢集中服務的狀態。 負載平衡器設定規則時，`frontend` 服務的 `EXTERNAL-IP` 會顯示為 `<pending>`。 幾分鐘之後，外部 IP 位址會完成設定： 

    ![設定 Azure Load Balancer](./media/container-service-kubernetes-load-balancing/guestbook-external-ip.png)

6. 確認您可以在外部 IP 位址存取服務。 例如，您可以開啟網頁瀏覽器並前往服務的外部 IP 位址。

    ![外部存取訪客留言](./media/container-service-kubernetes-load-balancing/guestbook-web.png)

    您可以加入訪客留言項目。

7. 若要查看 Azure Load Balancer 的組態，請瀏覽 [Azure 入口網站](https://portal.azure.com)中叢集的負載平衡器資源。 請參閱前一個範例中的步驟。

### <a name="considerations"></a>考量

* 負載平衡器規則會以非同步的方式建立，且已佈建之平衡器的相關資訊會在服務的 `status.loadBalancer` 欄位中發行。
* 每個服務都會在負載平衡器中被自動指定屬於自己的虛擬 IP 位址。
* 如果您想要透過 DNS 名稱來連絡負載平衡器，請與您的網域服務提供者合作建立規則之 IP 位址的 DNS 名稱。

## <a name="http-or-https-traffic"></a>HTTP 或 HTTPS 流量

若要將 HTTP 或 HTTPS 流量負載平衡到容器 Web 應用程式，並管理傳輸層安全性 (TLS) 的憑證，您可以使用 Kubernetes [輸入 (英文)](https://kubernetes.io/docs/user-guide/ingress/) 資源。 輸入是允許傳入連線連絡叢集服務的規則集合。 若要使輸入資源順利運作，Kubernetes 叢集必須具備執行中的[輸入控制器 (英文)](https://kubernetes.io/docs/user-guide/ingress/#ingress-controllers)。

Azure Container Service 不會自動實作 Kubernetes 輸入控制器。 有數個控制器實作可供使用。 目前，建議您使用 [Nginx 輸入控制器 (英文)](https://github.com/kubernetes/ingress/tree/master/examples/deployment/nginx) 來設定輸入規則，並針對 HTTP 與 HTTPS 流量進行負載平衡。 

如需詳細資訊，請參閱 [Nginx 輸入控制器文件集 (英文)](https://github.com/kubernetes/ingress/tree/master/controllers/nginx/README.md)。

> [!IMPORTANT]
> 在 Azure Container Service 中使用 Nginx 輸入控制器時，您必須使用 `type: LoadBalancer` 將控制器部署公開為服務。 這會設定 Azure Load Balancer 將流量路由傳送到控制器。 如需詳細資訊，請參閱上一節。


## <a name="next-steps"></a>後續步驟

* 請參閱 [Kubernetes LoadBalancer 文件集 (英文)](https://kubernetes.io/docs/user-guide/load-balancer/)
* 深入了解 [Kubernetes 輸入和輸入控制器 (英文)](https://kubernetes.io/docs/user-guide/ingress/)
* 請參閱 [Kubernetes 範例 (英文)](https://github.com/kubernetes/kubernetes/tree/master/examples)


