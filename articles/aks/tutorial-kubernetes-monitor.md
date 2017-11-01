---
title: "Azure 上的 Kubernertes 教學課程 - 監視 Kubernetes | Microsoft Docs"
description: "AKS 教學課程 - 使用 Microsoft Operations Management Suite (OMS) 監視 Kubernetes"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, 容器, 微服務, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 199779d47b6b13ac1d426e64364b4b24fe5db3d5
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-azure-container-service-aks"></a>監視 Azure Container Service (AKS)

監視 Kubernetes 叢集和容器很重要，尤其在您使用多個應用程式大規模管理生產叢集時。 

您可以利用來自 Microsoft 或其他提供者的數個 Kubernetes 監視解決方案。 在本教學課程中，您會使用 [Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) 中的 Containers 解決方案 (Microsoft 的雲端式 IT 管理解決方案) 來監視 Kubernetes 叢集。 (OMS Containers 解決方案處於預覽狀態。)

本教學課程 (八個章節的第七部分) 涵蓋下列工作：

> [!div class="checklist"]
> * 取得 OMS 工作區設定
> * 在 Kubernetes 節點上設定 OMS 代理程式
> * 在 OMS 入口網站或 Azure 入口網站中存取監視資訊

## <a name="before-you-begin"></a>開始之前

在上一個教學課程中，應用程式已封裝成容器映像、這些映像已上傳至 Azure Container Registry，並已建立 Kubernetes 叢集。 

如果您尚未完成這些步驟，而想要跟著做，請回到[教學課程 1 – 建立容器映像](./tutorial-kubernetes-prepare-app.md)。 

## <a name="get-workspace-settings"></a>取得工作區設定

可以存取 [OMS 入口網站](https://mms.microsoft.com)時，請移至 [設定] >  [已連線的服務] >  [Linux 伺服器]。 您可以在那裡找到「工作區識別碼」和主要或次要「工作區金鑰」。 請記下這些值，您在叢集上設定 OMS 代理程式時需要用到這些值。

## <a name="set-up-oms-agents"></a>設定 OMS 代理程式

以下是 YAML 檔案，用來在 Linux 叢集節點上設定 OMS 代理程式。 它會建立 Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)，以在每個叢集節點上執行單一相同的 pod。 DaemonSet 資源非常適合用於部署監視代理程式。 

將下列文字儲存到名為 `oms-daemonset.yaml` 的檔案，並以您的 OMS 工作區識別碼和金鑰取代 myWorkspaceID 和 myWorkspaceKey 的預留位置值。 (在生產環境中，您可以將這些值編碼為秘密)。

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: v1.3.4-127
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: myWorkspaceID
       - name: KEY 
         value: myWorkspaceKey
       - name: DOMAIN
         value: opinsights.azure.com
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
```

使用下列命令建立 DaemonSet：

```azurecli
kubectl create -f oms-daemonset.yaml
```

若要查看是否已建立 DaemonSet，請執行：

```azurecli
kubectl get daemonset
```

輸出大致如下：

```azurecli
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

執行代理程式之後，OMS 需要幾分鐘的時間來擷取及處理資料。

## <a name="access-monitoring-data"></a>存取監視資料

在 OMS 入口網站或 Azure 入口網站中，使用 [Container 解決方案](../log-analytics/log-analytics-containers.md)來檢視和分析 OMS 容器監視資料。 

若要使用 [OMS 入口網站](https://mms.microsoft.com)安裝 Container 解決方案，請移至**方案庫**。 然後新增 [Container 解決方案]。 或者，從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview) 新增 Containers 解決方案。

在 OMS 入口網站中，尋找 OMS 儀表板上的 [Containers] 摘要圖格。 按一下此圖格以取得下列詳細資訊：容器事件、錯誤、狀態、映像清查，以及 CPU 和記憶體使用量。 如需更細微的資訊，請按一下任何圖格上的資料列，或執行[記錄搜尋](../log-analytics/log-analytics-log-searches.md)。

![OMS 入口網站中的 Containers 儀表板](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

同樣地，在 Azure 入口網站中，移至 [Log Analytics] 並選取您的工作區名稱。 若要查看 [Containers] 摘要圖格，請按一下 [解決方案] > [Containers]。 若要查看詳細資料，按一下圖格。

請參閱 [Azure Log Analytics 文件](../log-analytics/index.yml)，以取得查詢及分析監視資料的詳細指引。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 OMS 監視 Kubernetes 叢集。 涵蓋的工作包括：

> [!div class="checklist"]
> * 取得 OMS 工作區設定
> * 在 Kubernetes 節點上設定 OMS 代理程式
> * 在 OMS 入口網站或 Azure 入口網站中存取監視資訊


請前往下一個教學課程，以了解如何將 Kubernetes 升級為新版本。

> [!div class="nextstepaction"]
> [升級 Kubernetes](./tutorial-kubernetes-upgrade-cluster.md)
