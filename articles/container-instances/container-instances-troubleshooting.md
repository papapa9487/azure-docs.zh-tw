---
title: "針對 Azure Container Instances 進行疑難排解"
description: "了解如何使用 Azure Container Instances 進行問題的疑難排解"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 11/18/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 6d8fbddc2f26fe739dd725f417961d7b3d7f77e6
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>使用 Azure Container Instances 進行部署問題的疑難排解

本文說明如何在將容器部署至 Azure Container Instances 時進行問題的疑難排解。 此外，也會說明一些您可能會碰到的常見問題。

## <a name="get-diagnostic-events"></a>取得診斷事件

若要在容器內檢視應用程式程式碼中的記錄，您可以使用 [az container logs](/cli/azure/container#logs) 命令。 但如果容器的部署並未成功，您就需要檢閱由 Azure Container Instances 資源提供者所提供的診斷資訊。 若要檢視容器的事件，請執行下列命令：

```azurecli-interactive
az container show -n mycontainername -g myresourcegroup
```

輸出中會包含容器的核心屬性以及部署事件：

```bash
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...

      "events": [
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:52+00:00",
        "lastTimestamp": "2017-08-03T22:12:52+00:00",
        "message": "Pulling: pulling image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Pulled: Successfully pulled image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Created: Created container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Started: Started container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      }
    ],
    "name": "helloworld",
      "ports": [
        {
          "port": 80
        }
      ],
    ...
  ]
}
```

## <a name="common-deployment-issues"></a>常見部署問題

部署時所發生的大部分錯誤都可歸咎於幾個常見問題。

## <a name="unable-to-pull-image"></a>無法提取映像

如果 Azure Container Instances 一開始無法提取您的映像，它會先重試一段時間，最後才會失敗。 如果它無法提取映像，系統便會顯示如下所示的事件：

```bash
"events": [
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:31+00:00",
    "lastTimestamp": "2017-08-03T22:19:31+00:00",
    "message": "Pulling: pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:32+00:00",
    "lastTimestamp": "2017-08-03T22:19:32+00:00",
    "message": "Failed: Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image microsoft/aci-hellowrld:latest not found",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:33+00:00",
    "lastTimestamp": "2017-08-03T22:19:33+00:00",
    "message": "BackOff: Back-off pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  }
]
```

若要解決，請刪除容器並重試部署，特別注意您所輸入的映像名稱是否正確。

## <a name="container-continually-exits-and-restarts"></a>容器不斷結束又重新啟動

如果您的容器執行到完成又自動重新啟動，可能需要設定 **OnFailure** 或 **Never** 的 [restart policy](container-instances-restart-policy.md) (重新啟動原則)。 如果指定 **OnFailure** 後仍持續重新啟動，可能是容器中執行的應用程式或指令碼的問題。

容器執行個體 API 有一個 `restartCount` 屬性。 若要檢查容器的重新啟動次數，可以在 Azure CLI 2.0 中使用 [az container show](/cli/azure/container#az_container_show) 命令。 在下列範例輸出中 (為簡潔起見已截斷畫面)，您可以在輸出的結尾看到 `restartCount` 屬性。

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux 散發套件的大部分容器映像都會設定殼層 (例如 bash) 來作為預設命令。 因為殼層本身不是長時間執行的服務，當設定為預設的 **Always** 重新啟動原則時，這些容器會立即結束並落入重新啟動迴圈。

## <a name="container-takes-a-long-time-to-start"></a>容器要等很久才會啟動

如果您的容器要等很久才會啟動，但最終還是會啟動成功，請先看看您的容器映像大小。 因為 Azure Container Instances 會視需要來提取您的容器映像，因此啟動時間的長短會與其大小直接相關。

您可以使用 Docker CLI 來檢視容器映像大小：

```bash
docker images
```

輸出：

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

讓映像不會變得太大的關鍵在於，確保最終的映像不會包含執行階段所不需要的任何項目。 若要做到這一點，有一種方式是使用[多階段建置](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)。 多階段建置可讓您輕鬆地確保最終映像只包含應用程式所需的構件，而不會包含建置階段所需的任何額外內容。

另一種可在容器啟動階段降低對於映像提取作業影響的方式，是在您想要使用 Azure Container Instances 的相同區域中，使用 Azure Container Registry 來裝載容器映像。 這種方式會縮短容器映像需要經過的網路路徑，從而大幅縮短下載時間。

## <a name="resource-not-available-error"></a>資源無法使用錯誤

Azure 中有各種不同的地區資源負載，因此您在嘗試部署容器執行個體時，可能會收到下列錯誤訊息：

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

此錯誤訊息表示您嘗試執行部署產品的地區負載過重，因此當時無法配置您為容器指定的資源。 執行下列一或多個風險降低步驟有助於解決問題。

* 確認容器部署設定是否位於 [Azure Container Instances 地區可用性](container-instances-region-availability.md)所定義的參數範圍內
* 為容器指定較低階的 CPU 和記憶體設定
* 部署至其他 Azure 地區
* 過一段時間再部署
