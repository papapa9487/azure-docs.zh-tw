---
title: "使用 Draft 搭配 AKS 和 Azure Container Registry"
description: "使用 Draft 搭配 AKS 和 Azure Container Registry"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: df5614d8a708b49ee1368c4d7983f45d29920fd8
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="use-draft-with-azure-container-service-aks"></a>使用 Draft 搭配 Azure Container Service (AKS)

Draft 是一個開放原始碼工具，可協助在 Kubernetes 叢集中封裝和執行程式碼。 Draft 特別適用於開發反覆週期中的開發程式碼期間，但是在認可版本控制之前。 當程式碼變更時，您可以使用 Draft 將應用程式快速地重新部署到 Kubernetes。 如需有關 Draft 的詳細資訊，請參閱 [Github 上的 Draft 文件](https://github.com/Azure/draft/tree/master/docs)。

本文件會詳細說明如何在 AKS 上使用 Draft 搭配 Kubernetes 叢集。

## <a name="prerequisites"></a>必要條件

本文件中詳述的步驟假設您已建立 AKS 叢集，並建立與叢集的 kubectl 連線。 如果您需要這些項目，請參閱 [AKS 快速入門](./kubernetes-walkthrough.md)。

您在 Azure Container Registry (ACR) 中也需要私人 Docker 登錄。 如需部署 ACR 執行個體的指示，請參閱 [Azure Container Registry 快速入門](../container-registry/container-registry-get-started-azure-cli.md)。

## <a name="install-helm"></a>安裝 Helm

Helm CLI 是在開發系統上執行的用戶端，可讓您啟動、停止和管理具 Helm 圖表的應用程式。

若要在 Mac 上安裝 Helm CLI，請使用 `brew`。 如需其他安裝選項，請參閱[安裝 Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) \(英文\)。

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

## <a name="install-draft"></a>安裝 Draft

Draft CLI 是在開發系統上執行的用戶端，可讓您將程式碼快速部署到 Kubernetes 叢集。

若要在 Mac 上安裝 Draft CLI，請使用 `brew`。 如需其他安裝選項，請參閱 [Draft 安裝指南](https://github.com/Azure/draft/blob/master/docs/install.md) \(英文\)。

```console
brew install draft
```

輸出：

```
==> Installing draft from azure/draft
==> Downloading https://azuredraft.blob.core.windows.net/draft/draft-v0.7.0-darwin-amd64.tar.gz
Already downloaded: /Users/neilpeterson/Library/Caches/Homebrew/draft-0.7.0.tar.gz
==> /usr/local/Cellar/draft/0.7.0/bin/draft init --client-only
🍺  /usr/local/Cellar/draft/0.7.0: 6 files, 61.2MB, built in 1 second
```

## <a name="configure-draft"></a>設定 Draft

設定 Draft 時，必須指定容器登錄。 此範例中使用 Azure Container Registry。

執行下列命令，取得 ACR 執行個體的名稱和登入伺服器名稱。 將命令更新為包含 ACR 執行個體的資源群組名稱。

```console
az acr list --resource-group <resource group> --query "[].{Name:name,LoginServer:loginServer}" --output table
```

也需要 ACR 執行個體密碼。

執行下列命令以傳回 ACR 密碼。 將命令更新為 ACR 執行個體的名稱。

```console
az acr credential show --name <acr name> --query "passwords[0].value" --output table
```

使用 `draft init` 命令初始化 Draft。

```console
draft init
```

在此過程中，系統會提示您提供容器登錄認證。 使用 Azure Container Registry 時，登錄 URL 是 ACR 登入伺服器名稱，使用者名稱是 ACR 執行個體名稱，密碼是 ACR 密碼。

```console
1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): <ACR Login Server>
2. Enter your username: <ACR Name>
3. Enter your password: <ACR Password>
```

完成後，Draft 就會在 Kubernetes 叢集中設定好，並可供使用。

```
Draft has been installed into your Kubernetes Cluster.
Happy Sailing!
```

## <a name="run-an-application"></a>執行應用程式

Draft 存放庫包含幾個可用來示範 Draft 的應用程式範例。 建立存放庫的複製複本。

```console
git clone https://github.com/Azure/draft
```

變更為 Java 範例目錄。

```console
cd draft/examples/java/
```

使用 `draft create` 命令啟動程序。 此命令會建立在 Kubernetes 叢集中用來執行應用程式的成品。 這些項目包含 Dockerfile、Helm 圖表和 `draft.toml` 檔案 (Draft 組態檔)。

```console
draft create
```

輸出：

```
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

若要在 Kubernetes 叢集上執行應用程式，請使用 `draft up` 命令。 此命令會將應用程式程式碼和組態檔上傳至 Kubernetes 叢集。 然後執行 Dockerfile 建立容器映像、將映像推送至容器登錄中，最後執行 Helm 圖表啟動應用程式。

```console
draft up
```

輸出：

```
Draft Up Started: 'open-jaguar'
open-jaguar: Building Docker Image: SUCCESS ⚓  (28.0342s)
open-jaguar: Pushing Docker Image: SUCCESS ⚓  (7.0647s)
open-jaguar: Releasing Application: SUCCESS ⚓  (4.5056s)
open-jaguar: Build ID: 01BW3VVNZYQ5NQ8V1QSDGNVD0S
```

## <a name="test-the-application"></a>測試應用程式

若要測試應用程式，請使用 `draft connect` 命令。 此命令會將連線 Proxy 到允許安全本機連線的 Kubernetes Pod。 完成時，就可以在提供的 URL 存取應用程式。

在某些情況下，可能需要幾分鐘的時間下載容器映像，並啟動應用程式。 如果存取應用程式時收到錯誤，請重試連線。

```console
draft connect
```

輸出：

```
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

測試完應用程式時，請使用 `Control+C` 停止 Proxy 連線。

## <a name="expose-application"></a>公開應用程式

在 Kubernetes 中測試應用程式時，您可以讓應用程式在網際網路上供人使用。 這可以使用 [LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer) \(英文\) 類型的 Kubernetes 服務或 [ingress 控制器](https://kubernetes.io/docs/concepts/services-networking/ingress/) \(英文\) 來進行。 本文件詳細說明如何使用 Kubernetes 服務。


首先，必須更新 Draft 套件，以指定應建立 `LoadBalancer` 類型的服務。 若要進行，請更新 `values.yaml` 檔案中的服務類型。

```console
vi chart/java/values.yaml
```

找出 `service.type` 屬性並將值從 `ClusterIP` 更新為 `LoadBalancer`。

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

執行 `draft up` 以重新執行應用程式。

```console
draft up
```

可能需要幾分鐘的時間，服務才能傳回公用 IP 位址。 若要監視進度，請使用 `kubectl get service` 命令搭配監看式。

```console
kubectl get service -w
```

一開始，服務的 *EXTERNAL-IP* 會顯示為 `pending`。

```
deadly-squid-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

當 EXTERNAL-IP 位址從 `pending` 變為 `IP address` 之後，請使用 `Control+C` 來停止 kubectl 監看式流程。

```
deadly-squid-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

若要查看應用程式，請瀏覽至外部 IP 位址。

```console
curl 52.175.224.118
```

輸出：

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>針對應用程式反覆執行

現在，Draft 已設定好，且應用程式正在 Kubernetes 中執行，您已準備好反覆執行程式碼。 每當您想要測試更新的程式碼時，請執行 `draft up` 命令，以更新執行中的應用程式。

此範例是更新 Java hello world 應用程式。

```console
vi src/main/java/helloworld/Hello.java
```

更新 Hello World 文字。

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java - Draft Rocks!");
    }
}
```

執行 `draft up` 命令以重新部署應用程式。

```console
draft up
```

輸出

```
Draft Up Started: 'deadly-squid'
deadly-squid: Building Docker Image: SUCCESS ⚓  (18.0813s)
deadly-squid: Pushing Docker Image: SUCCESS ⚓  (7.9394s)
deadly-squid: Releasing Application: SUCCESS ⚓  (6.5005s)
deadly-squid: Build ID: 01BWK8C8X922F5C0HCQ8FT12RR
```

最後，檢視應用程式以查看更新。

```console
curl 52.175.224.118
```

輸出：

```
Hello World, I'm Java - Draft Rocks!
```

## <a name="next-steps"></a>後續步驟

如需有關使用 Draft 的詳細資訊，請參閱 GitHub 上的 Draft 文件。

> [!div class="nextstepaction"]
> [Draft 文件](https://github.com/Azure/draft/tree/master/docs)