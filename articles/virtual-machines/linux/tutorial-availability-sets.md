---
title: "Azure 中 Linux VM 的可用性設定組教學課程 | Microsoft Docs"
description: "了解 Azure 中 Linux VM 的可用性設定組。"
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: cynthn
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 486405aca760922ebed5f413495d3a0e1e339229
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---

# <a name="how-to-use-availability-sets"></a>如何使用可用性設定組


在本教學課程中，您會學到如何使用稱為「可用性設定組」的功能，增加 Azure 虛擬機器解決方案的可用性和可靠性。 可用性設定組可確保您在 Azure 上部署的 VM 會分散到多個各自獨立的硬體叢集中。 這麼做可以確保當 Azure 發生硬體或軟體故障時，受到影響的只會是一小部分的 VM，整體解決方案會維持可用且正常運作，而作為使用者的客戶卻不會發現故障情形。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立可用性設定組
> * 在可用性設定組中建立 VM
> * 檢查可用的 VM 大小


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="availability-set-overview"></a>可用性設定組概觀

可用性設定組是一種可在 Azure 中使用的邏輯群組功能，用以確保其中所放置的 VM 資源在部署到 Azure 資料中心時會彼此隔離。 Azure 可確保您在可用性設定組中所放置的 VM，會橫跨多部實體伺服器、計算機架、儲存體單位和網路交換器來執行。 如此便能確保當硬體或 Azure 軟體發生故障時，只有一小部分的 VM 會受到影響，整個應用程式會保持運作狀態，可供客戶繼續使用。 如果您想要建置可靠的雲端解決方案，就一定要運用可用性設定組功能。

請想想典型的 VM 架構解決方案，在此解決方案中，您可能有 4 個前端 Web 伺服器，並使用 2 個後端 VM 來裝載資料庫。 在使用 Azure 時，建議您先定義兩個可用性設定組，再部署 VM︰一個可用性設定組用來放置「Web」層，另一個可用性設定組用來放置「資料庫」層。 當您建立新的 VM 時，您便可以將可用性設定組指定為 az vm create 命令的參數，Azure 會自動確保您在可用性設定組內所建立的 VM，會跨多個實體硬體資源來隔離。 這表示，如果其中一個用來執行 Web 伺服器或資料庫伺服器 VM 的實體硬體發生問題，您知道 Web 伺服器和資料庫 VM 的其他執行個體會繼續正常執行，因為它們位於不同硬體上。

如果您想要在 Azure 中部署可靠的虛擬機器架構解決方案，請務必使用可用性設定組。


## <a name="create-an-availability-set"></a>建立可用性設定組

您可以使用 [az vm availability-set create](/cli/azure/vm/availability-set#create) 來建立可用性設定組。 在此範例中，我們將針對 *myResourceGroupAvailability* 資源群組中名為 *myAvailabilitySet* 的可用性設定組，同時將更新數目和容錯網域數目設為 *2*。

建立資源群組。

```azurecli-interactive 
az group create --name myResourceGroupAvailability --location eastus
```


```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

可用性設定組可讓您跨「容錯網域」和「更新網域」來隔離資源。 **容錯網域**代表以隔離方式集合在一起的伺服器、網路和儲存體資源。 在前面的範例中，我們表示我們想在部署 VM 時，讓可用性設定組分散到至少兩個容錯網域。 我們也表示我們想要讓可用性設定組分散到兩個**更新網域**。  兩個更新網域可確保當 Azure 執行軟體更新時，我們的 VM 資源是隔離的，以免 VM 中執行的所有軟體同時更新。

## <a name="create-vms-inside-an-availability-set"></a>建立位於可用性設定組內的 VM

您必須將 VM 建立於可用性設定組內，才能確保 VM 會在硬體中正確地分散。 您無法在建立可用性設定組之後，將現有的 VM 加入至其中。 

當您使用 [az vm create](/cli/azure/vm#create) 建立 VM 時，會使用 `--availability-set` 參數來指定可用性設定組，以指定可用性設定組的名稱。

```azurecli-interactive 
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

現在，我們新建立的可用性設定組內有兩部虛擬機器。 由於它們位於相同的可用性設定組內，Azure 會確保 VM 和其所有資源 (包括資料磁碟) 會分散到隔離開來的實體硬體中。 這樣的分佈方式有助於確保 VM 解決方案整體有更高的可用性。

當您新增 VM 時，您可能會遇到特定 VM 大小已不再適用於可用性設定組的情況。 此問題的發生原因是，其容量已經不足，無法既新增 VM，又保持可用性設定組所強制實施的隔離規則。 您可以使用 `--availability-set list-sizes` 參數，檢查看看有哪些 VM 大小可在現有的可用性設定組內使用。

## <a name="check-for-available-vm-sizes"></a>檢查可用的 VM 大小 

您稍後可以將更多 VM 加入至可用性設定組，但是需要知道硬體上有哪些可用的 VM 大小。 針對可用性設定組，使用 [az vm availability-set list-sizes](/cli/azure/availability-set#list-sizes) 來列出硬體叢集上所有可用的大小。

```azurecli-interactive 
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立可用性設定組
> * 在可用性設定組中建立 VM
> * 檢查可用的 VM 大小

請前進到下一個教學課程，以了解虛擬機器擴展集。

> [!div class="nextstepaction"]
> [建立 VM 擴展集](tutorial-create-vmss.md)


