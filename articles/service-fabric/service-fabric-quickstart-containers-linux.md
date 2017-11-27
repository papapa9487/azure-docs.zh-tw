---
title: "在 Linux 上建立 Azure Service Fabric 容器應用程式 | Microsoft Docs"
description: "在 Azure Service Fabric 上建立第一個 Linux 容器應用程式。  使用您的應用程式建置 Docker 映像、將映像推送到容器登錄，建置和部署 Service Fabric 容器應用程式。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.openlocfilehash: 0a0ab748dcf722358633c7be350c6addcd61352d
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>在 Azure 上部署 Azure Service Fabric Linux 容器應用程式
Azure Service Fabric 是一個分散式系統平台，可讓您部署及管理可調整和可信賴的微服務與容器。 

本快速入門示範如何將 Linux 容器部署到 Service Fabric 叢集。 完成後，您會有一個投票應用程式，它是由在 Service Fabric 叢集中執行的 Python Web 前端和 Redis 後端所組成。 

![quickstartpic][quickstartpic]

在此快速入門中，您可了解如何：
> [!div class="checklist"]
> * 將容器部署到 Azure Linux Service Fabric 叢集
> * 在 Service Fabric 中縮放和容錯移轉容器

## <a name="prerequisite"></a>必要條件
如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/en-us/free/) 。
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用命令列介面 (CLI)，則可確保您執行 Azure CLI 2.0.4 版或更新版本。 若要尋找版本，請執行 az --version。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)。

## <a name="get-application-package"></a>取得應用程式套件
若要將容器部署至 Service Fabric，您需要一組可描述個別容器和應用程式的資訊清單檔案 (應用程式定義)。

在雲端殼層中，使用 git 來複製一份應用程式定義。

```azurecli-interactive
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="deploy-the-containers-to-a-service-fabric-cluster-in-azure"></a>將容器部署到 Azure 中的 Service Fabric 叢集
若要將應用程式部署到 Azure 中的叢集，請使用您自己的叢集，或使用合作對象叢集。

> [!Note]
> 應用程式必須部署到 Azure 中的叢集，而不是本機部署機器上的 Service Fabric 叢集。 
>

合作對象叢集是 Azure 上裝載的免費、限時 Service Fabric 叢集。 這類叢集是由任何人皆可部署應用程式並了解平台的 Service Fabric 小組所維護。 若要存取合作對象叢集，請[遵循指示](http://aka.ms/tryservicefabric)。 

如需建立您自己叢集的資訊，請參閱[在 Azure 上建立您的 Service Fabric 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)。

> [!Note]
> Web 前端服務設定為在連接埠 80 上接聽傳入流量。 請確定您的叢集中已開啟該連接埠。 如果您使用合作對象叢集，此連接埠已開啟。
>

### <a name="deploy-the-application-manifests"></a>部署應用程式資訊清單 
在 CLI 環境中安裝 [Service Fabric CLI (sfctl)](service-fabric-cli.md)

```azurecli-interactive
pip3 install --user sfctl 
export PATH=$PATH:~/.local/bin
```
使用 Azure CLI 連線到 Azure 中的 Service Fabric 叢集。 此端點是叢集的管理端點，例如 `http://linh1x87d1d.westus.cloudapp.azure.com:19080`。

```azurecli-interactive
sfctl cluster select --endpoint http://linh1x87d1d.westus.cloudapp.azure.com:19080
```

使用所提供的安裝指令碼將投票應用程式定義複製到叢集、註冊應用程式類型，以及建立應用程式的執行個體。

```azurecli-interactive
./install.sh
```

開啟瀏覽器並瀏覽至 Service Fabric Explorer (http://\<my-azure-service-fabric-cluster-url>:19080/Explorer)，例如 `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`。 展開 [應用程式] 節點，可看到投票應用程式類型和您建立的執行個體現在有一個項目。

![Service Fabric Explorer][sfx]

連線到執行中的容器。  開啟 Web 瀏覽器並指向您叢集的 URL，例如 `http://linh1x87d1d.westus.cloudapp.azure.com:80`。 您應會在瀏覽器中看到投票應用程式。

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>容錯移轉叢集中的容器
Service Fabric 可確保如果發生失敗，容器執行個體會自動移至叢集中的其他節點。 您也可以手動清空容器的節點，並將它們依正常程序移至叢集中的其他節點。 您有多種方式可調整您的服務，在此範例中，我們會使用 Service Fabric Explorer。

若要容錯移轉前端容器，請執行下列步驟：

1. 在您的叢集中開啟 Service Fabric Explorer，例如 `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`。
2. 按一下樹狀檢視中的 **fabric:/Voting/azurevotefront** 節點，然後展開資料分割節點 (以 GUID 表示)。 請注意樹狀檢視中的節點名稱，其中顯示哪些節點上的容器目前正在執行 - 例如 `_nodetype_4`
3. 展開樹狀檢視中的 [節點] 節點。 按一下正在執行容器之節點旁邊的省略符號 (三個點)。
4. 選擇 [重新啟動] 以重新啟動節點並確認重新啟動動作。 重新啟動會造成容器容錯移轉至叢集中的其他節點。

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>調整叢集中的應用程式和服務
您可以在整個叢集內輕鬆地調整 Service Fabric 服務，以符合服務的負載。 您可以藉由變更叢集中執行的執行個體數目來調整服務。

若要調整 Web 前端服務，請執行下列步驟：

1. 在您的叢集中開啟 Service Fabric Explorer，例如 `http://linh1x87d1d.westus.cloudapp.azure.com:19080`。
2. 按一下樹狀檢視中 **fabric:/Voting/azurevotefront** 節點旁邊的省略符號 (三個點)，然後選擇 [調整服務]。

    ![containersquickstartscale][containersquickstartscale]

  您現在可以選擇調整 Web 前端服務的執行個體數目。

3. 將數字變更為 **2**，然後按一下 [調整服務]。
4. 按一下樹狀檢視中的 **fabric:/Voting/azurevotefront** 節點，然後展開資料分割節點 (以 GUID 表示)。

    ![containersquickstartscaledone][containersquickstartscaledone]

    您現在可以看到服務有兩個執行個體。 在樹狀檢視中，您可看到執行個體在哪些節點上執行。

藉由這項簡單的管理工作，我們會加倍前端服務可用來處理使用者負載的資源。 請務必了解，您不需要多個服務執行個體，就能夠可靠地執行。 如果服務失敗，Service Fabric 可確保新的服務執行個體在叢集中執行。

## <a name="clean-up"></a>清除
使用範本中提供的解除安裝指令碼，刪除叢集中的應用程式執行個體並取消註冊應用程式類型。 這個命令會花一些時間來清除執行個體，而且不得在此指令碼之後立即執行 'install'sh' 命令。 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>後續步驟
在此快速入門中，您已了解如何：
> [!div class="checklist"]
> * 將 Linux 容器應用程式部署至 Azure
> * 容錯移轉 Service Fabric 叢集中的容器
> * 調整 Service Fabric 叢集中的容器

* 深入了解如何[在 Service Fabric 上執行容器](service-fabric-containers-overview.md)。
* 深入了解 Service Fabric [應用程式生命週期](service-fabric-application-lifecycle.md)。
* 請查看 GitHub 上的 [ Service Fabric 容器程式碼範例](https://github.com/Azure-Samples/service-fabric-containers)。

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
