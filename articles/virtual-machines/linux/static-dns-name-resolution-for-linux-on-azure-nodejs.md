---
title: "在 Azure 上針對 VM 名稱解析使用內部 DNS | Microsoft Docs"
description: "在 Azure 上針對 VM 名稱解析使用內部 DNS。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
ms.openlocfilehash: bfba2cf38a0624e8480a32bf153f391d820da5a1
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>在 Azure 上針對 VM 名稱解析使用內部 DNS

這篇文章說明如何使用虛擬 NIC 卡 (VNic) 和 DNS 標籤名稱設定 Linux VM 的靜態內部 DNS 名稱。 靜態 DNS 名稱是用於永久基礎結構服務，例如 Jenkins 組建伺服器，它用於這份文件或 Git 伺服器。

這些需求包括：

* [一個 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)
* [SSH 公開金鑰和私密金鑰檔案](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#quick-commands) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 適用於資源管理部署模型的新一代 CLI


## <a name="quick-commands"></a>快速命令

如果您需要快速完成工作，接下來這一節將詳細說明所需的命令。 每個步驟的詳細資訊和內容可在文件其他地方找到，[從這裡開始](#detailed-walkthrough)。  

必要條件︰資源群組、VNet、具有 SSH 輸入的 NSG、子網路。

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>使用靜態內部 DNS 名稱建立 VNic

`-r` CLI 旗標是用於設定 DNS 標籤，它提供 VNic 的靜態 DNS 名稱。

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>將 VM 部署至 VNet、NSG 並連接 VNic

在部署至 Azure 期間，`-N` 將 VNic 連接到新的 VM。

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說

Azure 上完整的連續整合和連續部署 (CiCd) 基礎結構需要特定的伺服器是靜態或長時間執行的伺服器。  像虛擬網路 (VNet) 和網路安全性群組 (NSG) 之類的 Azure 資產，最好是當做很少部署的靜態且長久的資源。  VNet 部署之後可供新的部署重複使用，完全不會對基礎結構造成負面影響。  新增至此靜態網路，Git 存放庫伺服器和 Jenkins 自動化伺服器會將 CiCd 傳遞至您的開發或測試環境。  

僅可在 Azure 虛擬網路內解析內部 DNS 名稱。  因為 DNS 名稱是內部的，它們不會解析至外部網際網路，為基礎結構提供額外安全性。

_將任何範例換成您自己的名稱。_

## <a name="create-the-resource-group"></a>建立資源群組

需要資源群組以組織我們在本逐步解說中建立的所有項目。  如需 Azure 資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>建立 VNet

第一個步驟是建立可放入 VM 的 VNet。  在本逐步解說中，VNet 包含的一個子網路。  如需 Azure VNet 的詳細資訊，請參閱[使用 Azure CLI 建立虛擬網路](../../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>建立 NSG

子網路是建置在現有網路安全性群組的背後，所以我們在子網路之前建置 NSG。  Azure NSG 相當於網路層的防火牆。  如需 Azure NSG 的詳細資訊，請參閱[如何在 Azure CLI 中建立 NSG](../../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>新增輸入 SSH 允許規則

由於需要從網際網路存取 Linux VM，因此需要有規則來允許輸入連接埠 22 流量通過網路流向 Linux VM 的連接埠 22。

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix * \
--source-port-range * \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>將子網路新增至 VNet

VNet 內的 VM 必須位於子網路。  每一個 VNet 可以有多個子網路。  建立子網路，並將子網路和 NSG 產生關聯，以便將防火牆新增至子網路。

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

子網路現在已新增至 VNet 內，而且與 NSG 及與 NSG 規則相關聯。

## <a name="creating-static-dns-names"></a>建立靜態 DNS 名稱

Azure 非常有彈性，但是若要針對 VM 名稱解析使用 DNS 名稱，您需要使用 DNS 標籤將它們建立為虛擬網路卡 (VNics)。  VNic 很重要，因為您可以將它們連接至不同的 VM 以重複使用，這樣可讓 VNic 保持為靜態資源，而 VM 可以是暫時的。  使用 VNic 上的 DNS 標籤，我們就可以從 VNet 中的其他 VM 啟用簡單名稱解析。  使用可解析的名稱，會讓其他 VM 依據 DNS 名稱 `Jenkins` 存取自動化伺服器，或以 `gitrepo` 存取 Git 伺服器。  建立 VNic，並將它與先前步驟中建立的子網路產生關聯。

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>將 VM 部署至 VNet 和 NSG

我們現在有 VNet、該 VNet 內的子網路，還有 NSG 做為防火牆來封鎖所有輸入流量 (除了用於 SSH 的連接埠 22)，以保護我們的子網路。  現在可以將 VM 部署在這個現有的網路基礎結構內。

您可以使用 Azure CLI 和 `azure vm create` 命令，將 Linux VM 部署至現有的 Azure 資源群組、VNet、子網路和 VNic。  如需使用 CLI 來部署完整 VM 的詳細資訊，請參閱[使用 Azure CLI 建立完整的 Linux 環境](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

我們使用 CLI 旗標來呼叫現有的資源，以指示 Azure 將 VM 部署在現有的網路內。  重申一次，VNet 和子網路部署之後，就可以在 Azure 區域內保持為靜態或永久性資源。  

## <a name="next-steps"></a>後續步驟
* [直接使用 Azure CLI 命令，建立自訂的 Linux VM 環境](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用範本在 Azure 上建立 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
