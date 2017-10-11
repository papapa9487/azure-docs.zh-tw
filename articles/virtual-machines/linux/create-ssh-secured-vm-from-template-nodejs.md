---
title: "使用 Azure 範本與 Azure CLI 1.0 建立 Linux VM | Microsoft Docs"
description: "使用 Azure CLI 1.0 和 Azure Resource Manager 範本在 Azure 上建立 Linux VM。"
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
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d4aaa78fcdf3bd9e2e236606f2d3049f464a8a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>如何使用 Azure CLI 1.0 和 Azure Resource Manager 範本建立 Linux VM
本文示範如何使用 Azure CLI 1.0 和 Azure Resource Manager 範本快速部署 Linux 虛擬機器。 本文需要：

* 一個 Azure 帳戶 ([取得免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/))。
* 使用 `azure login` 登入的 [Azure CLI 1.0](../../cli-install-nodejs.md)。
* Azure CLI *必須處於* Azure Resource Manager 模式 `azure config mode arm`。

您也可以使用 [Azure 入口網站](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)來快速部署 Linux VM 範本。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#quick-command-summary) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](create-ssh-secured-vm-from-template.md) - 適用於資源管理部署模型的新一代 CLI

## <a name="quick-command-summary"></a>快速命令摘要
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說
範本可讓您在 Azure 上使用要在啟動期間自訂的設定 (如使用者名稱和主機名稱等設定) 建立 VM。 在本文中，我們推出的 Azure 範本會利用 Ubuntu VM 以及連接埠 22 對 SSH 開放的網路安全性群組 (NSG)。

Azure Resource Manager 範本是 JSON 檔案，可用於進行簡單的一次性工作 (如本文中的啟動 Ubuntu VM)。  Azure 範本也可用來建構整個環境的複雜 Azure 組態，例如測試、開發或生產部署堆疊。

## <a name="create-the-linux-vm"></a>建立 Linux VM
下列程式碼範例示範如何呼叫 `azure group create` ，以使用 [此 Azure Resource Manager 範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)同時建立資源群組和部署受 SSH 保護的 Linux VM。 請記住，在您的範例中您必須使用環境中唯一的名稱。 此範例則以 myResourceGroup 作做為資源群組名稱，並以 myVM 作為 VM 名稱。

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

輸出應該看起來像下列的輸出區塊：

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

該範例使用 `--template-uri` 參數部署 VM。  您也可以使用 `--template-file` 參數並以範本檔案路徑做為引數，在本機下載或建立範本並傳遞範本。 Azure CLI 會提示您輸入範本所需的參數。

## <a name="next-steps"></a>後續步驟
請搜尋 [範本庫](https://azure.microsoft.com/documentation/templates/) 以探索接下來要部署哪些應用程式架構。

