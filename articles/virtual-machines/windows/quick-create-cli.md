---
title: "Azure 快速入門 - 建立 Windows VM CLI | Microsoft Docs"
description: "快速了解如何使用 Azure CLI 建立 Windows 虛擬機器。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6dc92640f1ff46bac25c11fe246deed514030d9b
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2017
---
# <a name="create-a-windows-virtual-machine-with-the-azure-cli"></a>使用 Azure CLI 建立 Windows 虛擬機器

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本快速入門詳細說明如何使用 Azure CLI 來部署執行 Windows Server 2016 的虛擬機器。 部署完成後，我們會連線至伺服器並安裝 IIS。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Create virtual machine

使用 [az vm create](/cli/azure/vm#az_vm_create) 建立 VM。 

下列範例會建立名為 myVM 的 VM。 此範例會以 azureuser 作為系統管理使用者名稱並以 myPassword12 作為密碼。 將這些值更新為適合您環境的值。 建立與虛擬機器的連線時需要使用這些值。

```azurecli-interactive 
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
```

建立 VM 後，Azure CLI 會顯示類似下列範例的資訊。 記下 `publicIpAaddress`。 此位址用來存取 VM。

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>針對 Web 流量開啟連接埠 80 

依預設只能透過 RDP 連線至 Azure 中部署的 Windows 虛擬機器。 如果此 VM 即將成為 Web 伺服器，您需要從網際網路開啟連接埠 80。 使用 [az vm open-port](/cli/azure/vm#open-port) 命令來開啟所需的連接埠。  
 
 ```azurecli-interactive  
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```


## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

使用下列命令，建立與虛擬機器的遠端桌面工作階段。 以虛擬機器的公用 IP 位址取代 IP 位址。 出現提示時，請輸入您在建立虛擬機器時所使用的認證。

```bash 
mstsc /v:Public IP Address
```

## <a name="install-iis-using-powershell"></a>使用 PowerShell 安裝 IIS

您現已登入 Azure VM，可使用一行 PowerShell 來安裝 IIS，並啟用本機防火牆規則以允許 Web 流量通過。 開啟 PowerShell 提示字元並執行下列命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>檢視 IIS 歡迎使用頁面

安裝 IIS 後，現在經由網際網路在您的 VM 上開啟連接埠 80 - 您可以使用所選的網頁瀏覽器來檢視預設 IIS 歡迎使用畫面。 請務必使用您上面記載的公用 IP 位址來瀏覽預設網頁。 

![IIS 預設網站](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#delete) 命令來移除資源群組、VM 和所有相關資源。

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已部署簡單的虛擬機器、網路安全性群組規則，並已安裝 Web 伺服器。 若要深入了解 Azure 虛擬機器，請繼續 Windows VM 的教學課程。

> [!div class="nextstepaction"]
> [Azure Windows 虛擬機器教學課程](./tutorial-manage-vm.md)
