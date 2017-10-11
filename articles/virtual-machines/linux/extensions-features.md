---
title: "適用於 Linux 的虛擬機器擴充功能和功能 | Microsoft Docs"
description: "了解哪些擴充功能適用於 Azure 虛擬機器，並依它們提供或改善的內容來分組。"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 8a5b39351f665c51ae7d83f755329e54ff3cf786
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>適用於 Linux 的虛擬機器擴充功能和功能

「Azure 虛擬機器」擴充功能是小型的應用程式，可在「Azure 虛擬機器」上提供部署後設定及自動化工作。 例如，如果「虛擬機器」要求安裝軟體、防毒保護或 Docker 組態，便可使用 VM 擴充功能來完成這些工作。 您可以使用 Azure CLI、PowerShell、Azure Resource Manager 範本及 Azure 入口網站來執行 Azure VM 擴充功能。 擴充功能可以與新的虛擬機器部署搭配，或是在任何現有的系統上執行。

本文件提供 VM 擴充功能概觀、使用 Azure VM 擴充功能的必要條件，以及如何偵測、管理和移除 VM 擴充功能的指引。 本文件提供通用的資訊，因為有許多可用的 VM 擴充功能，每個都有唯一可能的組態。 可以在每份個別擴充功能專用的文件中找到擴充功能特定的詳細資料。

## <a name="use-cases-and-samples"></a>使用案例和範例

有數個不同的 Azure VM 擴充功能可供使用，各有特定使用案例。 部分範例如下：

- 使用適用於 Linux 的 DSC 擴充功能將 PowerShell 預期狀態設定套用至虛擬機器。 如需詳細資訊，請參閱 [Azure 期望狀態組態擴充功能簡介](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)。
- 使用 Microsoft 監視代理程式 VM 擴充功能設定虛擬機器的監視。 如需詳細資訊，請參閱[如何監視 Linux 虛擬機器](tutorial-monitoring.md)。
- 使用 Datadog 擴充功能設定監視您的 Azure 基礎結構。 如需詳細資訊，請參閱 [Datadog 部落格](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)。
- 使用 Docker VM 擴充功能設定 Azure 虛擬機器上的 Docker 主機。 如需詳細資訊，請參閱 [Docker VM 擴充功能](dockerextension.md)。

除了處理序特定擴充功能，自訂指令碼延伸模組適用於 Windows 和 Linux 虛擬機器。 適用於 Linux 的自訂指令碼延伸模組可讓任何 Bash 指令碼在虛擬機器上執行。 自訂指令碼對於設計需要超過原生 Azure 工具可提供之設定的 Azure 部署很有用。 如需詳細資訊，請參閱 [Linux VM 自訂指令碼延伸模組](extensions-customscript.md)。


## <a name="prerequisites"></a>必要條件

每個虛擬機器擴充功能可能有它自己的必要條件組。 比方說，Docker VM 擴充功能有受支援 Linux 散發套件的必要條件。 擴充功能特定文件中詳述的個別擴充功能的需求。

### <a name="azure-vm-agent"></a>Azure VM 代理程式

「Azure VM 代理程式」可管理「Azure 虛擬機器」與「Azure 網狀架構控制器」之間的互動。 VM 代理程式負責部署和管理「Azure 虛擬機器」的許多功能層面，包括執行「VM 擴充功能」。 「Azure VM 代理程式」會預先安裝在「Azure Marketplace 映像」上，並可手動安裝在支援的作業系統上。

如需有關支援的作業系統和安裝指示，請參閱 [Azure 虛擬機器代理程式](../windows/classic/agents-and-extensions.md)。

## <a name="discover-vm-extensions"></a>探索 VM 擴充功能

有許多不同的 VM 擴充功能可供與「Azure 虛擬機器」搭配使用。 若要查看完整清單，請使用 Azure CLI 來執行下列命令，其中將範例位置取代成所選擇的位置。

```azurecli
az vm extension image list --location westus -o table
```

## <a name="run-vm-extensions"></a>執行 VM 延伸模組

Azure 虛擬機器延伸模組可以在現有的虛擬機器上執行，這在需要進行組態變更或復原已部署之 VM 上的連線時很有用。 VM 擴充功能也可以搭配 Azure Resource Manager 範本部署。 使用具有 Resource Manager 範本中的擴充功能，可以部署及設定 Azure 虛擬機器而不需要部署後介入。

下列方法可用來針對現有的虛擬機器執行擴充功能。

### <a name="azure-cli"></a>Azure CLI

Azure 虛擬機器擴充功能可以使用 `az vm extension set` 命令對現有的虛擬機器執行。 這個範例會針對虛擬機器執行自訂指令碼延伸模組。

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

指令碼會產生類似下列文字的輸出：

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure 入口網站

VM 擴充功能可以透過 Azure 入口網站套用至現有的虛擬機器。 若要這樣做，請選取虛擬機器，選擇 [擴充功能]，然後按一下 [新增]。 從可用延伸模組清單選取您想要的延伸模組，並遵循精靈中的指示。

下圖顯示從 Azure 入口網站安裝 Linux 自訂指令碼擴充功能。

![安裝自訂指令碼延伸模組](./media/extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

VM 擴充功能可以新增至 Azure Resource Manager 範本，並使用範本的部署執行。 當您使用範本部署擴充功能時，可以建立完全設定的 Azure 部署。 例如，下列 JSON 取自 Resource Manager 範本。 此範本會部署一組負載平衡虛擬機器和 Azure SQL Database，並在每個 VM 上安裝 .NET 核心應用程式。 VM 擴充功能會處理軟體安裝。

如需詳細資訊，請參閱完整的 [Resource Manager 範本](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)。

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

如需詳細資訊，請參閱[編寫 Azure Resource Manager 範本](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions)。

## <a name="secure-vm-extension-data"></a>安全的 VM 擴充功能資料

當您執行 VM 擴充功能時，可能需要包含機密資訊，例如認證、儲存體帳戶名稱和儲存體帳戶存取金鑰。 許多 VM 擴充功能包含受保護的組態，其會加密資料並只在目標虛擬機器內加以解密。 每個擴充功能有特定受保護的組態結構描述，並每個都會在擴充功能特定文件中詳細說明。

下列範例會顯示適用於 Linux 的自訂指令碼延伸模組執行個體。 請注意，要執行的命令包含一組認證。 在此範例中，要執行的命令不會加密。


```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

將 **command to execute**屬性移至 **protected**組態可保護執行字串。

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

## <a name="troubleshoot-vm-extensions"></a>針對 VM 擴充功能進行疑難排解

每個 VM 擴充功能可能會有擴充功能特定的疑難排解步驟。 例如，當您使用自訂指令碼延伸模組時，指令碼執行詳細資料可以在擴充功能執行所在的虛擬機器上本機找到。 所有擴充功能特定疑難排解步驟都會在擴充功能特定文件中詳述。

下列疑難排解步驟適用於所有虛擬機器擴充功能。

### <a name="view-extension-status"></a>檢視擴充功能狀態

在虛擬機器擴充功能都已針對虛擬機器執行後，請使用下列 Azure CLI 命令傳回擴充功能的狀態。 請以您自己的值取代範例參數名稱。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

輸出看起來會像下列文字：

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

也可以在 Azure 入口網站中找到擴充功能的執行狀態。 若要檢視擴充功能的狀態，請選取虛擬機器中，選擇 [擴充功能]，然後選取所需的擴充功能。

### <a name="rerun-a-vm-extension"></a>重新執行 VM 擴充功能

可能會發生必須重新執行虛擬機器擴充功能的情況。 您可以藉由移除延伸模組，然後使用您所選的執行方法重新執行延伸模組，來重新執行延伸模組。 若要移除擴充功能，請使用 Azure CLI 執行下列命令。 請以您自己的值取代範例參數名稱。

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

您可以在 Azure 入口網站中使用下列步驟來移除擴充功能：

1. 選取虛擬機器。
2. 選擇**擴充功能**。
3. 選取所需的擴充功能。
4. 選擇**解除安裝**。

## <a name="common-vm-extension-reference"></a>常見的 VM 擴充功能參考
| 擴充功能名稱 | 說明 | 詳細資訊 |
| --- | --- | --- |
| Linux 的自訂指令碼擴充功能 |對「Azure 虛擬機器」執行指令碼 |[Linux 的自訂指令碼擴充功能](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Docker 擴充功能 |安裝 Docker 背景程式以支援遠端 Docker 命令。 |[Docker VM 擴充功能](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| VM 存取擴充功能 |重新取得對「Azure 虛擬機器」的存取權 |[VM 存取擴充功能](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure 診斷擴充功能 |管理「Azure 診斷」 |[Azure 診斷擴充功能](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM 存取擴充功能 |管理使用者和認證 |[適用於 Linux 的 VM 存取擴充功能](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
