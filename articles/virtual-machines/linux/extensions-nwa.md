---
title: "適用於 Linux 的 Azure 網路監看員代理程式虛擬機器擴充功能 | Microsoft Docs"
description: "使用虛擬機器擴充功能在 Linux 虛擬機器上部署網路監看員代理程式。"
services: virtual-machines-linux
documentationcenter: 
author: dennisg
manager: amku
editor: 
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: eaadd531b9e05a54446e61f98584ae9d75470a5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>適用於 Linux 的網路監看員代理程式虛擬機器擴充功能

## <a name="overview"></a>概觀

[Azure 網路監看員](https://review.docs.microsoft.com/en-us/azure/network-watcher/)是網路效能的監視、診斷和分析服務，可讓您監視 Azure 網路。 Azure 虛擬機器上網路監看員的某些功能必須要有網路監看員代理程式虛擬機器擴充功能。 這包括擷取隨選網路流量和其他進階功能。

本文件會詳述適用於 Linux 的網路監看員代理程式虛擬機器擴充功能所支援的平台和部署選項。

## <a name="prerequisites"></a>必要條件

### <a name="operating-system"></a>作業系統

可以針對這類 Linux 散發套件執行網路監看員代理程式擴充功能：

| 配送映像 | 版本 |
|---|---|
| Ubuntu | 16.04 LTS、14.04 LTS 和 12.04 LTS |
| Debian | 7 和 8 |
| RedHat | 6.x 和 7.x |
| Oracle Linux | 7x |
| Suse | 11 和 12 |
| OpenSuse | 7.0 |
| CentOS | 7.0 |

請注意，目前不支援 CoreOS。

### <a name="internet-connectivity"></a>網際網路連線

網路監看員代理程式的某些功能需要目標虛擬機器連線到網際網路。 若沒有建立傳出的連線，網路監看員代理程式的某些功能可能會有問題或者無法使用。 如需詳細資訊，請參閱[網路監看員文件](https://review.docs.microsoft.com/en-us/azure/network-watcher/)。

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示網路監看員代理程式擴充功能的結構描述。 擴充功能此次不需要也不支援任何使用者提供的設定，而是依賴其預設組態。

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| 類型 | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 上一節中詳述的 JSON 結構描述可在部署 Azure Resource Manager 範本時，在 Azure Resource Manager 範本中用來執行網路監看員代理程式擴充功能。

## <a name="azure-cli-deployment"></a>Azure CLI 部署

Azure CLI 可以用來將網路監看員代理程式 VM 擴充功能部署到現有的虛擬機器。

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>疑難排解和支援

### <a name="troubleshooting"></a>疑難排解

使用 Azure CLI，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令。

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

擴充功能執行輸出會記錄至下列目錄中的檔案︰

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>支援

如果關於本文有任何需要協助的地方，您可以參閱網路監看員文件，或連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/en-us/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/en-us/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/en-us/support/faq/)。
