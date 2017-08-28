---
title: "在 Azure 中的 Linux VM 上執行自訂指令碼 | Microsoft Docs"
description: "使用「自訂指令碼擴充功能」來自動執行 Linux VM 組態工作"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 89001404fa7255efc656b98983502d1f1d84fd73
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017

---
# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>搭配 Linux 虛擬機器使用 Azure 自訂指令碼擴充功能
「自訂指令碼擴充功能」會在 Azure 虛擬機器上下載並執行指令碼。 此擴充功能適用於部署後組態、軟體安裝或其他任何組態/管理工作。 您可以從 Azure 儲存體或其他可存取的網際網路位置下載指令碼，或是將指令碼提供給擴充功能執行階段。 「自訂指令碼擴充功能」會與 Azure Resource Manager 範本整合，您也可以使用 Azure CLI、PowerShell、Azure 入口網站或「Azure 虛擬機器 REST API」來執行它。

本文件詳細說明如何從 Azure CLI 和 Azure Resource Manager 範本使用「自訂指令碼擴充功能」，同時也詳細說明 Linux 系統上的疑難排解步驟。

## <a name="extension-configuration"></a>擴充功能組態
「自訂指令碼擴充功能」組態會指定指令碼位置和要執行命令等項目。 此組態可以儲存在組態檔中、在命令列中指定，或在 Azure Resource Manager 範本中指定。 機密資料可以儲存在受保護的組態中，此組態會經過加密，並且只會在虛擬機器內解密。 當執行命令包含機密資料 (例如密碼) 時，受保護的組態會相當有用。

### <a name="public-configuration"></a>公用組態
結構描述：

**注意** - 屬性名稱會區分大小寫。 使用如下所示的名稱以避免發生部署問題。

* **commandToExecute**：(必要，字串) 要執行的進入點指令碼。
* **fileUris**：(選擇性，字串陣列) 所要下載檔案的 URL。
* **timestamp** ：(選擇性，整數) 只有在透過變更此欄位的值來觸發指令碼的重新執行時，才需使用此欄位。

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>受保護的組態
結構描述：

**注意** - 屬性名稱會區分大小寫。 使用如下所示的名稱以避免發生部署問題。

* **commandToExecute**：(選擇性，字串) 要執行的進入點指令碼。 如果您的命令包含機密資料 (例如密碼)，請改用此欄位。
* **storageAccountName**：(選擇性，字串) 儲存體帳戶的名稱。 如果您指定儲存體證明資料，則所有 fileUri 都必須是 Azure Blob 的 URL。
* **storageAccountKey**：(選擇性，字串) 儲存體帳戶的存取金鑰。

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
使用 Azure CLI 來執行「自訂指令碼擴充功能」時，請建立一或多個至少包含檔案 URI 和指令碼執行命令的組態檔。

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

您可以視需要在命令中以 JSON 格式化字串的形式指定設定。 這可讓您在執行期間指定組態，而不需使用個別的組態檔。

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],"commandToExecute": "./test.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI 範例

**範例 1** - 含指令檔的公用組態。

```json
{
  "fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],
  "commandToExecute": "./test.sh"
}
```

Azure CLI 命令：

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**範例 2** - 不含指令檔的公用組態。

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI 命令：

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**範例 3** - 使用公用組態檔來指定指令檔 URI，以及使用受保護的組態檔來指定要執行的命令。

公用組態檔：

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

受保護的組態檔：  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Azure CLI 命令：

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Resource Manager 範本
您可以透過使用 Resource Manager 範本，在「虛擬機器」部署階段執行「Azure 自訂指令碼擴充功能」。 若要這麼做，請將格式正確的 JSON 新增到部署範本中。

### <a name="resource-manager-examples"></a>Resource Manager 範例
**範例 1** - 公用組態。

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**範例 2** - 受保護組態中的執行命令。

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
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
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

如需完整的範例，請參閱「.Net 核心音樂市集示範」- [音樂市集示範](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db)。

## <a name="troubleshooting"></a>疑難排解
當「自訂指令碼擴充功能」執行時，會建立指令碼，或將指令碼下載到類似下列範例的目錄。 命令輸出也會儲存到這個目錄的 `stdout` 和 `stderr` 檔案中。

```bash
/var/lib/waagent/custom-script/download/0/
```

「Azure 指令碼擴充功能」會產生一個記錄檔，位置如下。

```bash
/var/log/azure/custom-script/handler.log
```

您也可以使用 Azure CLI 來擷取「自訂指令碼擴充功能」的執行狀態。

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

輸出看起來會像下列文字：

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>後續步驟
如需有關其他「VM 指令碼擴充功能」的資訊，請參閱 [適用於 Linux 的 Azure 指令碼擴充功能概觀](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。


