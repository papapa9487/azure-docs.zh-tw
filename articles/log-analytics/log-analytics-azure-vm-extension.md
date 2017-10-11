---
title: "將 Azure 虛擬機器連接到 Log Analytics | Microsoft Docs"
description: "針對在 Azure 中執行的 Windows 和 Linux 虛擬機器，收集記錄和計量的建議方法是安裝 Log Analytics Azure VM 擴充功能。 您可以使用 Azure 入口網站或 PowerShell，將 Log Analytics 虛擬機器擴充功能安裝到 Azure VM。"
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: ewinner
editor: 
ms.assetid: ca39e586-a6af-42fe-862e-80978a58d9b1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: richrund
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cdae291b546fef4d7fdb8b067c8e4f4c9708d43f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="connect-azure-virtual-machines-to-log-analytics-with-a-log-analytics-agent"></a>使用 Log Analytics 代理程式將 Azure 虛擬機器連接到 Log Analytics

針對 Windows 和 Linux 電腦，收集記錄和度量的建議方式是安裝 Log Analytics 代理程式。

在 Azure 虛擬機器上安裝 Log Analytics 代理程式最簡單的方式是透過 Log Analytics VM 擴充。  使用擴充可以簡化安裝程序，並自動設定代理程式將資料傳送到您指定的 Log Analytics 工作區。 代理程式也會自動升級，以確保您擁有最新的功能和修正程式。

對於 Windows 虛擬機器，啟用 *Microsoft Monitoring Agent* 虛擬機器擴充功能。
對於 Linux 虛擬機器，啟用 *OMS Agent for Linux* 虛擬機器擴充功能。

深入了解 [Azure 虛擬機器擴充功能](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)和 [Linux 代理程式](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

如果您使用代理程式來收集記錄資料，則必須設定 [Log Analytics 中的資料來源](log-analytics-data-sources.md)，以指定您要收集的記錄和度量。

> [!IMPORTANT]
> 如果您已將 Log Analytics 設為使用 [Azure 診斷](log-analytics-azure-storage.md)來編製記錄資料的索引，並設定代理程式來收集相同的記錄，則記錄會收集兩次。 您需支付這兩個資料來源的費用。 如果您已安裝代理程式，則請僅使用代理程式來收集記錄資料 - 不要設定 Log Analytics 從 Azure 診斷收集記錄資料。
>
>

有三個簡單的方法可啟用 Log Analytics 虛擬機器擴充功能︰

* 使用 Azure 入口網站
* 使用 Azure PowerShell
* 透過使用 Azure Resource Manager 範本

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>在 Azure 入口網站中啟用 VM 擴充
您可以安裝 Log Analytics 的代理程式，並使用 [Azure 入口網站](https://portal.azure.com)來連接執行該代理程式的 Azure 虛擬機器。

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>安裝 Log Analytics 代理程式，並將虛擬機器連接到 Log Analytics 工作區
1. 登入 [Azure 入口網站](http://portal.azure.com)。
2. 在入口網站左側選取 [瀏覽]，然後移至 [Log Analytics (OMS)] 並選取它。
3. 在您的 Log Analytics 工作區清單中，選取要用於 Azure VM 的工作區。  
   ![OMS 工作區](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4. 在 [Log Analytics 管理] 下方，選取 [虛擬機器]。  
   ![虛擬機器](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5. 在 [虛擬機器] 清單中，選取您要安裝代理程式的虛擬機器。 VM 的 [OMS 連接狀態] 會指出它是 [未連接] 狀態。  
   ![VM 未連接](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6. 在虛擬機器的詳細資訊中，選取 [連接]。 隨即會為 Log Analytics 工作區自動安裝和設定代理程式。 這個程序需要幾分鐘的時間，在這段期間 OMS 連線狀態是「連接中...」  
   ![連接 VM](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7. 安裝並連接代理程式之後，[OMS 連接] 狀態會更新為顯示 [此工作區]。  
   ![已連接](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)

## <a name="enable-the-vm-extension-using-powershell"></a>使用 PowerShell 啟用 VM 擴充
使用 PowerShell 設定虛擬機器時，您必須提供 **workspaceId** 和 **workspaceKey**。 json 組態中的屬性名稱需**區分大小寫**。

您可以在 OMS 入口網站的 [設定] 頁面上，或使用上述範例所示的 PowerShell，找到識別碼及金鑰。

![工作區識別碼及主要金鑰](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

Azure 傳統虛擬機器和 Resource Manager 虛擬機器各使用不同的命令。 以下是傳統和 Resource Manager 虛擬機器的範例。

若是傳統 Azure 虛擬機器，請使用下列 PowerShell 範例：

```PowerShell
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

針對 Resource Manager Linux VM 請使用下列 CLI
```azurecli
az vm extension set --resource-group myRGMonitor --vm-name myMonitorVM --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --version 1.3 --protected-settings ‘{"workspaceKey": "<workspace-key>"}’ --settings ‘{"workspaceId": "<workspace-id>"}’ 
```

若是 Resource Manager 虛擬機器，請使用下列 PowerShell 範例：

```PowerShell
Login-AzureRMAccount
Select-AzureRMSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```


## <a name="deploy-the-vm-extension-using-a-template"></a>使用範本部署 VM 擴充
您可以利用 Azure Resource Manager 建立範本 (JSON 格式)，以定義應用程式的部署和設定。 此範本就是所謂的資源管理員範本，並提供定義部署的宣告方式。 在整個應用程式生命週期內，您可以使用範本來重複部署應用程式，並確信您的資源會以一致的狀態部署。

您可以將 Log Analytics 代理程式納入 Resource Manager 範本中，以確定每個虛擬機器都預先設定為向您的 Log Analytics 工作區報告。

如需 Resource Manager 範本的詳細資訊，請參閱[編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。

以下是用來部署虛擬機器的 Resource Manager 範本的範例，此虛擬機器執行 Windows 且已安裝 Microsoft Monitoring Agent 擴充。 此範例是一個典型的虛擬機器範本，而且還增加下列項目︰

* workspaceId 和 workspaceName 參數
* Microsoft.EnterpriseCloud.Monitoring 資源擴充功能區段
* 輸出以查詢 workspaceId 和 workspaceSharedKey

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMS workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

您可以使用下列 PowerShell 命令來部署範本：

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-the-log-analytics-vm-extension"></a>針對 Log Analytics VM 擴充功能進行疑難排解
當系統無法運作時，您通常會收到一則訊息 (從 Azure 入口網站或 Azure Powershell)。

1. 登入 [Azure 入口網站](http://portal.azure.com)。
2. 找出 VM 並開啟 VM 詳細資料。
3. 按一下 [擴充功能]，檢查是否已啟用 OMS 擴充功能。

   ![VM 擴充功能檢視](./media/log-analytics-azure-vm-extension/oms-vmview-extensions.png)

4. 按一下 [MicrosoftMonitoringAgent] \(Windows) 或 [OmsAgentForLinux] \(Linux) 擴充功能，並檢視詳細資料。 

   ![VM 擴充功能詳細資料](./media/log-analytics-azure-vm-extension/oms-vmview-extensiondetails.png)

### <a name="troubleshooting-windows-virtual-machines"></a>針對 Windows 虛擬機器進行疑難排解
如果未安裝或沒有回報 Microsoft Monitoring Agent VM 代理程式擴充功能，您可以執行下列步驟來針對問題進行疑難排解。

1. 使用 [KB 2965986](https://support.microsoft.com/kb/2965986#mt1) 中的步驟，檢查 Azure VM 代理程式是否已安裝且正確運作。
   * 您也可以檢閱 VM 代理程式記錄檔 `C:\WindowsAzure\logs\WaAppAgent.log`
   * 如果記錄檔不存在，則表示未安裝 VM 代理程式。
     * [在傳統 VM 上安裝 Azure VM 代理程式](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
2. 使用下列步驟，確認 Microsoft Monitoring Agent 擴充活動訊號工作正在執行︰
   * 登入虛擬機器
   * 開啟工作排程器，找出 `update_azureoperationalinsight_agent_heartbeat` 工作
   * 確認此工作已啟用，且每一分鐘執行一次
   * 檢查 `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log` 中的活動訊號記錄檔
3. 檢閱 `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent` 中的 Microsoft Monitoring Agent VM 擴充記錄檔
4. 確定虛擬機器可以執行 PowerShell 指令碼
5. 確定 C:\Windows\temp 的權限未變更
6. 在虛擬機器上提高權限的 PowerShell 視窗中輸入下列命令，以檢視 Microsoft Monitoring Agent 的狀態 `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. 檢閱 `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs` 中的 Microsoft Monitoring Agent 安裝記錄檔

如需詳細資訊，請參閱[針對 Windows 擴充功能進行疑難排解](../virtual-machines/windows/extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

### <a name="troubleshooting-linux-virtual-machines"></a>針對 Linux 虛擬機器進行疑難排解
如果未安裝或沒有回報 OMS Agent for Linux VM 代理程式擴充功能，您可以執行下列步驟來針對問題進行疑難排解。

1. 如果擴充狀態是「未知」，請檢閱 VM 代理程式記錄檔 `/var/log/waagent.log`，以檢查 Azure VM 代理程式是否已安裝且正常運作
   * 如果記錄檔不存在，則表示未安裝 VM 代理程式。
   * [在 Linux VM 上安裝 Azure VM 代理程式](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
2. 若是其他不良狀態，請檢閱 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` 和 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log` 中的 OMS Agent for Linux VM 擴充記錄檔
3. 如果擴充狀態良好，但資料未上傳，請檢閱 `/var/opt/microsoft/omsagent/log/omsagent.log` 中的 OMS Agent for Linux 記錄檔

## <a name="next-steps"></a>後續步驟
* 設定 [Log Analytics 中的資料來源](log-analytics-data-sources.md) 來指定要收集的記錄和計量。
* [從方案庫新增 Log Analytics 方案](log-analytics-add-solutions.md)，以收集來自虛擬機器的資料。
* 針對 Azure 中執行的其他資源，[使用 Azure 診斷收集資料](log-analytics-azure-storage.md)。

如果電腦不是在 Azure 中，您可以使用下列文章中所述的方法來安裝 Log Analytics 代理程式︰

* [將 Windows 電腦連接到 Log Analytics](log-analytics-windows-agents.md)
* [將 Linux 電腦連接到 Log Analytics](log-analytics-linux-agents.md)
