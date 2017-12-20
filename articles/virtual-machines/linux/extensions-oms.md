---
title: "適用於 Linux 的 OMS Azure 虛擬機器擴充功能 | Microsoft Docs"
description: "使用虛擬機器擴充功能在 Linux 虛擬機器上部署 OMS 代理程式。"
services: virtual-machines-linux
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: dcb7a777c66200c5046a6ad34dc4ff5d346f13e0
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="oms-virtual-machine-extension-for-linux"></a>適用於 Linux 的 OMS 虛擬機器擴充功能

## <a name="overview"></a>概觀

Operations Management Suite (OMS) 可提供雲端和內部部署資產的監視、警示和警示補救功能。 Microsoft 已發佈和支援適用於 Linux 的 OMS 代理程式虛擬機器擴充功能。 擴充功能會在 Azure 虛擬機器上安裝 OMS 代理程式，並且在現有的 OMS 工作區中註冊虛擬機器。 本文件詳述適用於 Linux 的 OMS 虛擬機器擴充功能所支援的平台、組態和部署選項。

## <a name="prerequisites"></a>先決條件

### <a name="operating-system"></a>作業系統

可以針對這類 Linux 散發套件執行 OMS 代理程式擴充功能。

| 配送映像 | 版本 |
|---|---|
| CentOS Linux | 5、6 和 7 |
| Oracle Linux | 5、6 和 7 |
| Red Hat Enterprise Linux Server | 5、6 和 7 |
| Debian GNU/Linux | 6、7 和 8 |
| Ubuntu | 12.04 LTS、14.04 LTS、15.04、15.10、16.04 LTS |
| SUSE Linux Enterprise Server | 11 和 12 |

### <a name="azure-security-center"></a>Azure 資訊安全中心

Azure 資訊安全中心會使用 Azure 訂用帳戶的預設記錄分析工作區，自動佈建 OMS 代理程式並與它連線。 如果您使用的是 Azure 資訊安全中心，請不要執行此文件中的步驟。 這樣做會覆寫已設定的工作區，並中斷與 Azure 資訊安全中心的連線。

### <a name="internet-connectivity"></a>網際網路連線

適用於 Linux 的 OMS 代理程式擴充功能會要求目標虛擬機器連線到網際網路。 

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示 OMS 代理程式擴充功能的結構描述。 此擴充功能需要來自目標 OMS 工作區的工作區識別碼和工作區金鑰，您可以在 OMS 入口網站中找到這些值。 由於工作區金鑰應視為敏感性資料，因此應儲存在受保護的設定組態中。 Azure VM 擴充功能保護的設定資料會經過加密，只會在目標虛擬機器上解密。 請注意，**workspaceId** 和 **workspaceKey** 區分大小寫。

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| 類型 | OmsAgentForLinux |
| typeHandlerVersion | 1.4 |
| workspaceId (例如) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (例如) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 部署一或多部需要部署後設定的虛擬機器時，很適合使用範本。 在 [Azure 快速啟動資源庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)上可找到包含 OMS 代理程式 VM 擴充功能的範例 Resource Manager 範本。 

虛擬機器擴充功能的 JSON 設定可以巢狀方式置於虛擬機器資源內部，或放在 Resource Manager JSON 範本的根目錄或最上層。 JSON 設定的放置會影響資源名稱和類型的值。 如需詳細資訊，請參閱[設定子資源的名稱和類型](../../azure-resource-manager/resource-manager-template-child-resource.md)。 

下列範例假設 OMS 擴充功能以巢狀方式置於虛擬機器資源內部。 在巢狀處理擴充資源時，JSON 會放在虛擬機器的 `"resources": []` 物件中。

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

將擴充 JSON 置於範本的根目錄時，資源名稱包含父系虛擬機器的參考，而類型可反映巢狀的組態。  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

Azure CLI 可以用來將 OMS 代理程式 VM 擴充功能部署到現有的虛擬機器。 請將 OMS 金鑰和 OMS 識別碼取代成來自您 OMS 工作區的 OMS 金鑰和 OMS 識別碼。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.4 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure CLI，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

擴充功能執行輸出會記錄至下列檔案︰

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>錯誤碼及其意義

| 錯誤碼 | 意義 | 可能的動作 |
| :---: | --- | --- |
| 10 | VM 已經連線到 OMS 工作區 | 若要將 VM 連線到擴充功能結構描述中所指定的工作區，請在公用設定中將 stopOnMultipleConnections 設定為 false，或是移除此屬性。 針對此 VM 所連線的每個工作區都會向此 VM 計費一次。 |
| 11 | 提供給擴充功能的組態無效 | 依照上述範例來設定部署所需的所有屬性值。 |
| 12 | dpkg 套件管理員已鎖定 | 請確定機器上的所有 dpkg 更新作業皆已完成，然後重試。 |
| 20 | 啟用提前呼叫 | [將 Azure Linux 代理程式更新](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent)為最新的可用版本。 |
| 51 | VM 的作業系統上不支援此擴充功能 | |
| 55 | 無法連線至 Microsoft Operations Management Suite 服務 | 請確認系統是否有網際網路存取權，或已提供有效的 HTTP Proxy。 此外，請確認工作區識別碼是否正確。 |

如需其他疑難排解資訊，請參閱 [OMS-Agent-for-Linux 疑難排解指南](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#)。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/en-us/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/en-us/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/en-us/support/faq/)。
