---
title: "使用 Draft 搭配 Azure Container Service 與 Azure Container Registry | Microsoft Docs"
description: "建立 ACS Kubernetes 叢集和 Azure Container Registry，可使用 Draft 在 Azure 中建立第一個應用程式。"
services: container-service
documentationcenter: 
author: squillace
manager: gamonroy
editor: 
tags: draft, helm, acs, azure-container-service
keywords: "Docker, 容器, 微服務, Kubernetes, Draft, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: rasquill
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: b70d2340c0f1286fa355a78a4cd0cb1ce37cbc39
ms.contentlocale: zh-tw
ms.lasthandoff: 07/25/2017

---

# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>使用 Draft 搭配 Azure Container Service 與 Azure Container Registry，可將應用程式建置及部署至 Kubernetes

[Draft](https://aka.ms/draft) 是新的開放原始碼工具，可讓您輕鬆地開發以容器作為基礎的應用程式，並將其部署至 Kubernetes 叢集，而無需深入了解 Docker 和 Kubernetes，或甚至進行安裝。 使用諸如 Draft 等工具可讓您和小組專注於使用 Kubernetes 來建置應用程式，無須投入過多注意力在基礎結構。

您可以使用 Draft 搭配任何 Docker 映像登錄與任何 Kubernetes 叢集，包括本機。 本教學課程會示範如何使用 ACS 搭配 Kubernetes、ACR 和 Azure DNS，使用 Draft 來建立即時的 CI/CD 開發人員管線。


## <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry
您可以輕鬆地[建立新的 Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md)，步驟如下所示：

1. 建立 Azure 資源群組可在 ACS 中管理您的 ACR 登錄和 Kubernetes 叢集。
      ```azurecli
      az group create --name draft --location eastus
      ```

2. 使用 [az acr create](/cli/azure/acr#create) 來建立 ACR 映像登錄
      ```azurecli
      az acr create -g draft -n draftacs --sku Basic --admin-enabled true -l eastus
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>使用 Kubernetes 建立 Azure Container Service

現在您準備好使用 [az acs create](/cli/azure/acs#create)，利用 Kubernetes 作為 `--orchestrator-type` 值來建立 ACS 叢集。
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes
```

> [!NOTE]
> 因為 Kubernetes 不是預設的 Orchestrator 類型，請確定您使用 `--orchestrator-type kubernetes` 參數。

成功時的輸出大致如下所示。

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

現在，您有一個叢集，可以使用 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) 命令將認證匯入。 現在您有叢集的本機組態檔，這是 Helm 和 Draft 完成其工作所需要的項目。

## <a name="install-and-configure-draft"></a>安裝及設定草稿
Draft 的安裝指示位於 [Draft 存放庫](https://github.com/Azure/draft/blob/master/docs/install.md)。 它們相對而言較為簡單，但需要一些設定，因為它取決於 [Helm](https://aka.ms/helm) 來建立 Helm，並加以部署到 Kubernetes 叢集中。

1. [下載並安裝 Helm](https://aka.ms/helm#install)。
2. 使用 Helm 來搜尋及安裝 `stable/traefik`，並輸入控制器以啟用您組建的輸入要求。
    ```bash
    $ helm search traefik
    NAME            VERSION DESCRIPTION
    stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

    $ helm install stable/traefik --name ingress
    ```
    現在，請在 `ingress` 控制站上設定監看，以在部署外部 IP 值時加以擷取。 此 IP 位址會是下一節中[對應到您部署網域](#wire-up-deployment-domain)的 IP 位址。

    ```bash
    kubectl get svc -w
    NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
    kubernetes                    10.0.0.1       <none>          443/TCP                      7h
    ```

    在此案例中，部署網域的外部 IP 是 `13.64.108.240`。 現在您可以將網域對應至該 IP。

## <a name="wire-up-deployment-domain"></a>接通部署網域

Draft 會針對其所建立的每個 Helm 圖表，以及您在使用每個應用程式建立一個版本。 每個版本都會取得一個已產生的名稱，以在您所控制的根_部署網域_上作為_子網域_草稿。 (在此範例中，我們使用 `squillace.io` 作為部署網域。)若要啟用此子網域行為，您必須針對部署網域，在 DNS 項目中建立 `'*'` 的 A 記錄，以便每個產生的子網域會路由傳送至 Kubernetes 叢集的輸入控制器。

您自己的網域提供者都有其各自的方法可指派 DNS 伺服器；若要[將您的 nameservers 網域委派給 Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)，請採取下列步驟：

1. 建立您區域的資源群組。
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. 建立您網域的 DNS 區域。
使用 [az network dns zone create](/cli/azure/network/dns/zone#create) 命令來取得 nameservers，將 DNS 控制項委派給網域的 Azure DNS。
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. 將您所取得的 DNS 伺服器新增至您部署網域的網域提供者，可讓您視需要使用 Azure DNS 重新指向您的網域。
4. 建立部署網域的 A 記錄集項目，從上一節的步驟 2 中對應至 `ingress` IP。
    ```azurecli
    az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*' -g squillace.io -z squillace.io
    ```
輸出看起來會類似於：
    ```json
    {
      "arecords": [
        {
          "ipv4Address": "13.64.108.240"
        }
      ],
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
      "metadata": null,
      "name": "*",
      "resourceGroup": "squillace.io",
      "ttl": 3600,
      "type": "Microsoft.Network/dnszones/A"
    }
    ```

5. 設定 Draft 以使用您的登錄，並針對它所建立的每個 Helm 圖表建立子網域。 若要設定 Draft，您需要：
  - 您的 Azure Container Registry 名稱 (在此範例中為 `draft`)
  - 您的登錄機碼或密碼，從 `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`。
  - 您已設定為對應至 Kubernetes 輸入外部 IP 位址的根部署網域 (這裡為 `squillace.io`)

  呼叫 `draft init`，而設定程序會提示您輸入上述的值。 第一次執行此程序時，它看起來如下所示。
 ```bash
    $ draft init
    Creating pack ruby...
    Creating pack node...
    Creating pack gradle...
    Creating pack maven...
    Creating pack php...
    Creating pack python...
    Creating pack dotnetcore...
    Creating pack golang...
    $DRAFT_HOME has been configured at /Users/ralphsquillace/.draft.

    In order to install Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io, quay.io, myregistry.azurecr.io): draft.azurecr.io
    2. Enter your username: draft
    3. Enter your password:
    4. Enter your org where Draft will push images [draft]: draft
    5. Enter your top-level domain for ingress (e.g. draft.example.com): squillace.io
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
    ```

您現在已準備好要部署應用程式。


## <a name="build-and-deploy-an-application"></a>建置和部署應用程式

在 Draft 存放庫中，有[六個簡單的範例應用程式](https://github.com/Azure/draft/tree/master/examples)。 複製存放庫，讓我們使用 [Python 範例](https://github.com/Azure/draft/tree/master/examples/python)。 變更為範例/Python 目錄，並輸入 `draft create` 可建置應用程式。 它看起來會如下範例所示。
```bash
$ draft create
--> Python app detected
--> Ready to sail
```

輸出包含 Dockerfile 和 Helm 圖表。 若要建置和部署，您只要輸入 `draft up`。 輸出會很廣泛，但會如下列範例開始。
```bash
$ draft up
--> Building Dockerfile
Step 1 : FROM python:onbuild
onbuild: Pulling from library/python
10a267c67f42: Pulling fs layer
fb5937da9414: Pulling fs layer
9021b2326a1e: Pulling fs layer
dbed9b09434e: Pulling fs layer
ea8a37f15161: Pulling fs layer
<snip>
```

且在成功時會以類似下列的範例結束。
```bash
ab68189731eb: Pushed
53c0ab0341bee12d01be3d3c192fbd63562af7f1: digest: sha256:bb0450ec37acf67ed461c1512ef21f58a500ff9326ce3ec623ce1e4427df9765 size: 2841
--> Deploying to Kubernetes
--> Status: DEPLOYED
--> Notes:

  http://gangly-bronco.squillace.io to access your application

Watching local files for changes...
```

無論圖表的名稱為何，您可以現在 `curl http://gangly-bronco.squillace.io` 接收回覆，`Hello World!`。

## <a name="next-steps"></a>後續步驟

您有了 ACS Kubernetes 叢集之後，可以使用 [Azure Container Registry](../../container-registry/container-registry-intro.md) 進行調查，建立更多這種案例與不同的部署。 例如，您可以建立 draft._basedomain.toplevel_ 網域 DNS 記錄集，可針對特定 ACS 部署，控制項目移出更深入的子網域。







