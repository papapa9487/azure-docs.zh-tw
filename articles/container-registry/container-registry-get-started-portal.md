---
title: "快速入門 - 使用 Azure 入口網站在 Azure 中建立私用的 Docker 登錄"
description: "快速了解如何使用 Azure 入口網站建立私用的 Docker 容器登錄。"
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 514fa3490e480647f0923c99bd9606a3726d4d30
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>使用 Azure 入口網站建立容器登錄庫

Azure Container Registry 是 Azure 中的私人 Docker 登錄，您可以在其中儲存並管理私人 Docker 容器映像。 在本快速入門中，您會使用 Azure 入口網站建立容器登錄。

若要完成此快速入門，您必須在本機上有 Docker 帳戶。 Docker 提供可輕鬆在 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系統上設定 Docker 的套件。

## <a name="log-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，網址是 https://portal.azure.com。

## <a name="create-a-container-registry"></a>建立容器登錄庫

選取 [新增] > [容器] > [Azure Container Registry]。

![在 Azure 入口網站中建立容器登錄][qs-portal-01]

輸入 [登錄名稱] 和 [資源群組] 的值。 登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 建立名為 `myResourceGroup` 的新資源群組，[SKU] 請選取 [基本]。 選取 [建立] 以部署 ACR 執行個體。

![在 Azure 入口網站中建立容器登錄][qs-portal-03]

在本快速入門中，我們會建立「基本」登錄。 有數個不同的 SKU 提供 Azure Container Registry，簡略說明於下表。 如需個別項目更詳細的資訊，請參閱[容器登錄 SKU](container-registry-skus.md)。

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

當 [部署成功] 訊息出現時，請在入口網站中選取容器登錄，然後選取 [存取金鑰]。

![在 Azure 入口網站中建立容器登錄][qs-portal-05]

在 [管理使用者] 下，選取 [啟用]。 請將下列值記下：

* 登入伺服器
* 使用者名稱
* password

您在透過 Docker CLI 使用登錄時，請使用下列步驟中的這些值。

![在 Azure 入口網站中建立容器登錄][qs-portal-06]

## <a name="log-in-to-acr"></a>登入 ACR

發送和提取容器映像之前，您必須登入 ACR 執行個體。 若要這樣做，請使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令。 將*使用者名稱*、*密碼*和*登入伺服器*取代為您在上一個步驟中記下的值。

```bash
docker login --username <username> --password <password> <login server>
```

完成後，此命令會傳回 `Login Succeeded`。 您也可能會看到安全性警告，建議您使用 `--password-stdin` 參數。 其使用超出本文的範圍時，建議您遵循此最佳做法。 如需詳細資訊，請參閱 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令參考。

## <a name="push-image-to-acr"></a>推送映像到 ACR

若要將映像推送到 Azure Container Registry，您必須先有映像。 如有需要，請執行下列命令，以從 Docker Hub 提取現有的映像。

```bash
docker pull microsoft/aci-helloworld
```

在將映像推送至您的登錄之前，必須使用 ACR 登入伺服器名稱來標記映像。 使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 命令來標記映像。 將登入伺服器取代為您先前記錄的登入伺服器名稱。

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

最後，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 將映像推送到 ACR 執行個體。 將登入伺服器取代為 ACR 執行個體的登入伺服器名稱。

```
docker push <login server>/aci-helloworld:v1
```

成功 `docker push` 命令的輸出類似於：

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>列出容器映像

若要在您的 ACR 執行個體中列出映像，請瀏覽至入口網站中的登錄，然後選取 [存放庫]，以及選取您使用 `docker push` 建立的存放庫。

在此範例中，我們選取 **aci-helloworld** 存放庫，且可以在 **TAGS**之下看到 `v1` 標記的映像。

![在 Azure 入口網站中建立容器登錄][qs-portal-09]

## <a name="clean-up-resources"></a>清除資源

不再需要時，請刪除 **myResourceGroup** 資源群組。 這麼做會將資源群組、ACR 執行個體，以及所有容器映像刪除。

![在 Azure 入口網站中建立容器登錄][qs-portal-08]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用 Azure CLI 建立了 Azure Container Registry。 如果您想要使用 Azure Container Registry 搭配 Azure 容器執行特體，請繼續進行 Azure 容器執行個體教學課程。

> [!div class="nextstepaction"]
> [Azure Container Instances 教學課程](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png