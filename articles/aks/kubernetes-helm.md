---
title: "在 Azure 上的 Kubernetes 中運用 Helm 部署容器 |Microsoft Docs"
description: "使用 Helm 套件工具，將容器部署在 AKS 的 Kubernetes 叢集中。"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: de52c2aad0f74b59970234872dfa3e4136929915
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2017
---
# <a name="use-helm-with-azure-container-service-aks"></a>使用 Helm 搭配 Azure Container Service (AKS)

[Helm](https://github.com/kubernetes/helm/) 是開放原始碼的封裝工具，可協助您安裝和管理 Kubernetes 應用程式的生命週期。 Helm 類似於 *APT* 和 *Yum* 等 Linux 套件管理工具，可用於管理 Kubernetes 圖表 (即預先設定的 Kubernetes 資源套件)。

本文將逐步引導您在 AKS 的 Kubernetes 叢集中設定及使用 Helm。

## <a name="before-you-begin"></a>開始之前

本文件中詳述的步驟假設您已建立 AKS 叢集，並建立與叢集的 kubectl 連線。 如果您需要這些項目，請參閱 [AKS 快速入門](./kubernetes-walkthrough.md)。

## <a name="install-helm-cli"></a>安裝 Helm CLI

Helm CLI 是在您的開發系統上執行的用戶端，可讓您啟動、停止和管理具有 Helm 圖表的應用程式。

如果您是使用 Azure CloudShell，即已安裝 Helm CLI。 若要在 Mac 上安裝 Helm CLI，請使用 `brew`。 如需其他安裝選項，請參閱 [Installing Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) (安裝 Helm)。

```console
brew install kubernetes-helm
```

輸出：

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="configure-helm"></a>設定 Helm

[helm init](https://docs.helm.sh/helm/#helm-init) 命令是用來在 Kubernetes 叢集中安裝 Helm 元件，並進行用戶端設定。 Helm 是預先安裝在 AKS 叢集中，因此僅需要用戶端設定。 執行下列命令以設定 Helm 用戶端。

```azurecli-interactive
helm init --client-only
```

輸出：

```
$HELM_HOME has been configured at /Users/neilpeterson/.helm.
Not installing Tiller due to 'client-only' flag having been set
Happy Helming!
```

## <a name="find-helm-charts"></a>尋找 Helm 圖表

使用 Helm 圖表將應用程式部署到 Kubernetes 叢集中。 若要搜尋預先建立的 Helm 圖表，請使用 [helm search](https://docs.helm.sh/helm/#helm-search) 命令。

```azurecli-interactive
helm search
```

輸出看起來與下列內容類似，不過多了許多圖表。

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

若要更新圖表清單，請使用 [helm repo update](https://docs.helm.sh/helm/#helm-repo-update) 命令。

```azurecli-interactive
helm repo update
```

輸出：

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>執行 Helm 圖表

若要部署 NGINX 輸入控制器，請使用 [helm install](https://docs.helm.sh/helm/#helm-install) 命令。

```azurecli-interactive
helm install stable/nginx-ingress
```

輸出看起來類似下列內容，但包含如何使用 Kubernetes 部署指示等的其他資訊。

```
NAME:   tufted-ocelot
LAST DEPLOYED: Thu Oct  5 00:48:04 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
tufted-ocelot-nginx-ingress-controller  1     5s

==> v1/Service
NAME                                         CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
tufted-ocelot-nginx-ingress-controller       10.0.140.10  <pending>    80:30486/TCP,443:31358/TCP  5s
tufted-ocelot-nginx-ingress-default-backend  10.0.34.132  <none>       80/TCP                      5s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
tufted-ocelot-nginx-ingress-controller       1        1        1           0          5s
tufted-ocelot-nginx-ingress-default-backend  1        1        1           1          5s
...
```

如需使用 NGINX 輸入控制器與 Kubernetes 的詳細資訊，請參閱 [NGINX Ingress Controller](https://github.com/kubernetes/ingress/tree/master/controllers/nginx) (NGINX 輸入控制器)。

## <a name="list-helm-charts"></a>列出 Helm 圖表

若要查看安裝於叢集上的圖表清單，請使用 [helm list](https://docs.helm.sh/helm/#helm-list) 命令。

```azurecli-interactive
helm list
```

輸出：

```
NAME            REVISION    UPDATED                     STATUS      CHART               NAMESPACE
bilging-ant     1           Thu Oct  5 00:11:11 2017    DEPLOYED    nginx-ingress-0.8.7 default
```

## <a name="next-steps"></a>後續步驟

如需管理 Kubernetes 圖表的詳細資訊，請參閱 Helm 文件。

> [!div class="nextstepaction"]
> [Helm documentation](https://github.com/kubernetes/helm/blob/master/docs/index.md) (Helm 文件)