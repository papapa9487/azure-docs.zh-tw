---
title: "Azure 上的 Kubernertes 教學課程 - 監視 Kubernetes"
description: "AKS 教學課程 - 使用 Microsoft Operations Management Suite (OMS) 監視 Kubernetes"
services: container-service
documentationcenter: 
author: neilpeterson
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
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a41f699291a65129906680cbb6719c2478c0d830
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="monitor-azure-container-service-aks"></a>監視 Azure Container Service (AKS)

監視 Kubernetes 叢集和容器很重要，尤其在您使用多個應用程式大規模執行生產叢集時。

在本教學課程中，您會使用 [Log Analytics 的容器解決方案](../log-analytics/log-analytics-containers.md)來設定監視 AKS 叢集。

本教學課程 (八個章節的第七部分) 涵蓋下列工作：

> [!div class="checklist"]
> * 設定容器監視解決方案
> * 設定監視代理程式
> * 在 Azure 入口網站中存取監視資訊

## <a name="before-you-begin"></a>開始之前

在上一個教學課程中，應用程式已封裝成容器映像、這些映像已上傳至 Azure Container Registry，並已建立 Kubernetes 叢集。

如果您尚未完成這些步驟，而想要跟著做，請回到[教學課程 1 – 建立容器映像](./tutorial-kubernetes-prepare-app.md)。

## <a name="configure-the-monitoring-solution"></a>設定監視解決方案

在 Azure 入口網站中，選取 [新增]，然後搜尋 `Container Monitoring Solution`。 找到之後，選取 [建立]。

![新增解決方案](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

建立新的 OMS 工作區，或選取現有的工作區。 OMS 工作區表單會引導您完成此程序。

在建立工作區時，選取 [釘選到儀表板] 以方便擷取。

![OMS 工作區](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

完成時，選取 [確認]。 驗證完成之後，請選取 [建立] 以建立容器監視解決方案。

一旦建立工作區，就會在 Azure 入口網站中為您顯示。

## <a name="get-workspace-settings"></a>取得工作區設定

需要 Log Analytics 工作區識別碼和索引鍵，以便在 Kubernetes 節點上設定解決方案代理程式。

若要擷取這些值，請從容器解決方案左側功能表選取 [OMS 工作區]。 選取 [進階設定] 並記下**工作區識別碼**和**主索引鍵**。

## <a name="configure-monitoring-agents"></a>設定監視代理程式

下列 Kubernetes 資訊清單檔案可用於在 Kubernetes 叢集上設定容器監視代理程式。 它會建立 Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)，以在每個叢集節點上執行單一 pod。

將下列文字儲存到名為 `oms-daemonset.yaml` 的檔案，並以 Log Analytics 工作區識別碼和索引鍵取代 `WSID` 和 `KEY` 的預留位置值。

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
    agentVersion: 1.4.0-12
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: <WSID>
       - name: KEY
         value: <KEY>
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
        - mountPath: /var/opt/microsoft/omsagent/state/containerhostname
          name: container-hostname
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
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: container-hostname
      hostPath:
       path: /etc/hostname
    - name: host-log
      hostPath:
       path: /var/log
```

使用下列命令建立 DaemonSet：

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

若要查看是否已建立 DaemonSet，請執行：

```azurecli-interactive
kubectl get daemonset
```

輸出大致如下：

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

執行代理程式之後，OMS 需要幾分鐘的時間來擷取及處理資料。

## <a name="access-monitoring-data"></a>存取監視資料

在 Azure 入口網站中，選取已釘選到入口網站儀表板的 Log Analytics 工作區。 按一下 [容器監視解決方案] 圖格。 您可以在這裡找到 AKS 叢集和叢集中容器的相關資訊。

![儀表板](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

請參閱 [Azure Log Analytics 文件](../log-analytics/index.yml)，以取得查詢及分析監視資料的詳細指引。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 OMS 監視 Kubernetes 叢集。 涵蓋的工作包括：

> [!div class="checklist"]
> * 設定容器監視解決方案
> * 設定監視代理程式
> * 在 Azure 入口網站中存取監視資訊

請前往下一個教學課程，以了解如何將 Kubernetes 升級為新版本。

> [!div class="nextstepaction"]
> [升級 Kubernetes](./tutorial-kubernetes-upgrade-cluster.md)