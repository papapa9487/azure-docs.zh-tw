---
title: "針對 Azure Container Instances 進行疑難排解"
description: "了解如何使用 Azure Container Instances 進行問題的疑難排解"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: ff6da0ce95d0405714602c3872da34a2bff344d3
ms.contentlocale: zh-tw
ms.lasthandoff: 09/01/2017

---

# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>使用 Azure Container Instances 進行部署問題的疑難排解

本文說明如何在將容器部署至 Azure Container Instances 時進行問題的疑難排解。 此外，也會說明一些您可能會碰到的常見問題。

## <a name="getting-diagnostic-events"></a>取得診斷事件

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

### <a name="unable-to-pull-image"></a>無法提取映像

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

### <a name="container-continually-exits-and-restarts"></a>容器不斷結束又重新啟動

Azure Container Instances 目前僅支援長時間執行的服務。 如果您的容器執行完畢並結束，該容器會自動重新啟動並再次執行。 如果發生這種情況，系統會顯示如下所示的事件。 請注意，容器會在成功啟動後又迅速重新啟動。 Container Instances API 會包含 `retryCount` 屬性，以顯示特定容器的重新啟動次數。

```bash
"events": [
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:55+00:00",
    "lastTimestamp": "2017-08-03T22:23:22+00:00",
    "message": "Pulling: pulling image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:23:23+00:00",
    "message": "Pulled: Successfully pulled image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Created: Created container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Started: Started container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Created: Created container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Started: Started container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 13,
    "firstTimestamp": "2017-08-03T22:21:59+00:00",
    "lastTimestamp": "2017-08-03T22:24:36+00:00",
    "message": "BackOff: Back-off restarting failed container",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:22:13+00:00",
    "lastTimestamp": "2017-08-03T22:22:13+00:00",
    "message": "Created: Created container with id 72e347e891290e238135e4a6b3078748ca25a1275dbbff30d8d214f026d89220",
    "type": "Normal"
  },
  ...
```

> [!NOTE]
> Linux 散發套件的大部分容器映像都會設定殼層 (例如 bash) 來作為預設命令。 因為殼層本身不是長時間執行的服務，因此這些容器會立即結束並落入重新啟動迴圈。

### <a name="container-takes-a-long-time-to-start"></a>容器要等很久才會啟動

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

### <a name="resource-not-available-error"></a>資源無法使用錯誤

Azure 中有各種不同的地區資源負載，因此您在嘗試部署容器執行個體時，可能會收到下列錯誤訊息：

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

此錯誤訊息表示您嘗試執行部署產品的地區負載過重，因此當時無法配置您為容器指定的資源。 執行下列一或多個風險降低步驟有助於解決問題。

* 確認容器部署設定是否位於 [Azure Container Instances 地區可用性](container-instances-region-availability.md)所定義的參數範圍內
* 為容器指定較低階的 CPU 和記憶體設定
* 部署至其他 Azure 地區
* 過一段時間再部署

