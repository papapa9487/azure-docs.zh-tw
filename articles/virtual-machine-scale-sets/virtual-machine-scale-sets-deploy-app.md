---
title: "在虛擬機器擴展集上部署應用程式"
description: "使用延伸模組在 Azure 虛擬機器擴展集上部署應用程式。"
services: virtual-machine-scale-sets
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.openlocfilehash: 371295efea1eab66361b9aba21a55bbd2826c69b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>在虛擬機器擴展集上部署您的應用程式

本文說明在佈建擴展集時安裝軟體的各種不同方式。

您可能需要檢閱[擴展集設計概觀](virtual-machine-scale-sets-design-overview.md)一文，其描述虛擬機器擴展集合所加諸的一些限制。

## <a name="capture-and-reuse-an-image"></a>擷取並重複使用映像

您可以使用 Azure 中現有的虛擬機器來準備擴展集的基礎映像。 此程序會在您的儲存體帳戶中建立受控磁碟，您可以參考作為擴展集的基礎映像。 

請執行下列步驟：

1. 建立 Azure 虛擬機器
   * [Linux][linux-vm-create]
   * [Windows][windows-vm-create]

2. 遠端登入虛擬機器，並視喜好自訂系統。

   如果想要，您可以立即安裝應用程式。 不過，請了解若立即安裝應用程式，您可能會使升級應用程式更複雜，因為您可能必須先將它移除。 您可以改用此步驟來安裝應用程式可能需要的任何必要條件，例如特定執行階段或作業系統功能。

3. 遵循 [Linux][linux-vm-capture] 或 [Windows][windows-vm-capture] 的「擷取機器」教學課程。

4. 使用您在上一個步驟中擷取的映像 URI 建立[虛擬機器擴展集][vmss-create]。

如需磁碟的詳細資訊，請參閱[受控磁碟概觀](../virtual-machines/windows/managed-disks-overview.md)和[使用連結的資料磁碟](virtual-machine-scale-sets-attached-disks.md)。

## <a name="already-provisioned"></a>佈建擴展集時安裝

虛擬機器延伸模組可以套用至虛擬機器擴展集。 透過虛擬機器擴充功能，您可以將擴展集中的虛擬機器當做整個群組來自訂。 如需擴充功能的詳細資訊，請參閱[虛擬機器擴充功能](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

視您的作業系統為 Linux 或 Windows 而定，有三個主要延伸模組可供您使用。

### <a name="windows"></a>Windows

若是 Windows 作業系統，請使用**自訂指令碼 v1.8** 延伸模組或 **PowerShell DSC** 延伸模組。

#### <a name="custom-script"></a>Custom Script

自訂指令碼延伸模組會在擴展集的每部虛擬機器上執行指令碼。 設定檔或變數會指出下載到虛擬機器的檔案，再指出執行的命令。 例如，您可以使用此延伸模組來執行安裝程式、指令碼、批次檔和任何可執行檔。

PowerShell 針對設定使用雜湊表。 下列範例會設定自訂指令碼延伸模組，以執行安裝 IIS 的 PowerShell 指令碼。

```powershell
# Setup extension configuration hashtable variable
$customConfig = @{
  "fileUris" = @("https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1");
  "commandToExecute" = "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> `"%TEMP%\StartupLog.txt`" 2>&1";
};

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Compute -Type CustomScriptExtension -TypeHandlerVersion 1.8 -Name "customscript1" -Setting $customConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "MyVmssTest143"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>針對可能包含敏感性資訊的任何設定，請使用 `-ProtectedSetting` 參數。

---------


Azure CLI 針對設定使用 json 檔案。 下列範例會設定自訂指令碼延伸模組，以執行安裝 IIS 的 PowerShell 指令碼。 請將下列 json 檔案儲存為 _settings.json_。

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1"
  ],
  "commandToExecute": "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> \"%TEMP%\StartupLog.txt\" 2>&1"
}
```

然後，執行下列 Azure CLI 命令。

```azurecli
az vmss extension set --publisher Microsoft.Compute --version 1.8 --name CustomScriptExtension --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>針對可能包含敏感性資訊的任何設定，請使用 `--protected-settings` 參數。

### <a name="powershell-dsc"></a>PowerShell DSC

您可以使用 PowerShell DSC 來自訂擴展集的 VM 執行個體。 **Microsoft.Powershell** 所發行的 **DSC** 延伸模組會在每個虛擬機器執行個體上部署及執行所提供的 DSC 設定。 設定檔或變數會告訴延伸模組 *.zip* 封裝的位置，以及要執行的_指令碼函式_組合。

PowerShell 針對設定使用雜湊表。 下列範例會部署 DSC 封裝以安裝 IIS。

```powershell
# Setup extension configuration hashtable variable
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Powershell -Type DSC -TypeHandlerVersion 2.24 -Name "dsc1" -Setting $dscConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "myscaleset1"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>針對可能包含敏感性資訊的任何設定，請使用 `-ProtectedSetting` 參數。

-----------

Azure CLI 針對設定使用 json。 下列範例會部署 DSC 封裝以安裝 IIS。 請將下列 json 檔案儲存為 _settings.json_。

```json
{
  "wmfVersion": "latest",
  "configuration": {
    "url": "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip",
    "script": "configure-http.ps1",
    "function": "WebsiteTest"
  }
}
```

然後，執行下列 Azure CLI 命令。

```azurecli
az vmss extension set --publisher Microsoft.Powershell --version 2.24 --name DSC --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>針對可能包含敏感性資訊的任何設定，請使用 `--protected-settings` 參數。

### <a name="linux"></a>Linux

Linux 可以使用**自訂指令碼 v2.0** 延伸模組，或在建立期間使用 **cloud-init**。

自訂指令碼是簡單的延伸模組，可將檔案下載到虛擬機器執行個體，以及執行命令。

#### <a name="custom-script"></a>Custom Script

請將下列 json 檔案儲存為 _settings.json_。

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
  ],
  "commandToExecute": "bash installserver.sh"
}
```

使用 Azure CLI 將此延伸模組新增至現有的虛擬機器擴展集。 擴展集中的每部虛擬機器都會自動執行此延伸模組。

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>針對可能包含敏感性資訊的任何設定，請使用 `--protected-settings` 參數。

#### <a name="cloud-init"></a>Cloud-Init

您可以在建立擴展集時使用 Cloud-Init。 首先，建立名為 _cloud-init.txt_ 的本機檔案並加入您的設定。 例如，請參閱[此 gist](https://gist.github.com/Thraka/27bd66b1fb79e11904fb62b7de08a8a6#file-cloud-init-txt)

使用 Azure CLI 建立擴展集。 `--custom-data` 欄位接受 cloud-init 指令碼的檔案名稱。

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

## <a name="how-do-i-manage-application-updates"></a>如何管理應用程式更新？

如果您透過延伸模組部署應用程式，請對延伸模組定義進行某些變更。 此變更會將延伸模組重新部署到所有虛擬機器執行個體。 您**必須**對延伸模組進行某些變更 (例如重新命名所參考的檔案)，否則 Azure 不會看到延伸模組已變更。

如果您將應用程式內建到自己的作業系統映像中，請使用自動化部署管線進行應用程式更新。 請設計您的架構以協助將分段擴展集快速切換到生產環境。 此方法的其中一個好例子就是 [Azure Spinnaker 驅動程式工作](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/)。

[Packer](https://www.packer.io/) 和 [Terraform](https://www.terraform.io/) 支援 Azure Resource Manager，因此您也可以「以程式碼方式」定義您的映像並在 Azure 中建置它們，然後在您的擴展集內使用 VHD。 不過，這麼做對 Marketplace 映像會造成問題，其中延伸模組/自訂指令碼會變得更加重要，因為您不是直接從 Marketplace 操縱位元。

## <a name="what-happens-when-a-scale-set-scales-out"></a>當擴展集相應放大時，會發生什麼情況？
當您將一或多部虛擬機器新增至擴展集時，會自動安裝應用程式。 例如，如果擴展集已定義延伸模組，則每次建立新的虛擬機器時，這些延伸模組都會在新的虛擬機器上執行。 如果擴展集是以自訂映像為基礎，則所有新的虛擬機器都是來源自訂映像的複本。 如果擴展集的虛擬機器是容器主機，則您可以讓啟動程式碼載入自訂指令碼延伸模組中的容器。 或者，延伸模組可以安裝會向叢集 Orchestrator (例如 Azure Container Service) 註冊的代理程式。


## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>如何在各個更新網域推出 OS 更新？
假設您想要在更新 OS 映像的同時，又讓虛擬機器擴展集持續執行。 PowerShell 和 Azure CLI 可以更新虛擬機器映像，一次一部虛擬機器。 [升級虛擬機器擴展集](./virtual-machine-scale-sets-upgrade-scale-set.md)一文也提供進一步的資訊，說明虛擬機器擴展集內執行作業系統升級的可用選項。

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 管理您的擴展集。](virtual-machine-scale-sets-windows-manage.md)
* [建立擴展集範本。](virtual-machine-scale-sets-mvss-start.md)


[linux-vm-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[windows-vm-create]: ../virtual-machines/windows/tutorial-manage-vm.md
[linux-vm-capture]: ../virtual-machines/linux/capture-image.md
[windows-vm-capture]: ../virtual-machines/windows/capture-image.md 
[vmss-create]: virtual-machine-scale-sets-create.md

