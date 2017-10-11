---
title: "自動調整 Windows 虛擬機器擴展集 | Microsoft Docs"
description: "使用 Azure PowerShell 設定自動調整 Windows 虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
ms.openlocfilehash: 91f731bec46c005221f4e66e95822994070d4c26
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="automatically-scale-machines-in-a-virtual-machine-scale-set"></a>在虛擬機器擴展集中自動調整機器
虛擬機器擴展集可讓您將完全相同的虛擬機器以集合的方式進行部署和管理。 調整集可為超大規模的應用程式提供高度擴充和可自訂的計算層，並且可支援 Windows 平台映像、Linux 平台映像、自訂映像和擴充。 如需調整集的詳細資訊，請參閱 [虛擬機器調整集](virtual-machine-scale-sets-overview.md)。

本教學課程說明如何建立 Windows 虛擬機器的擴展集，並對擴展集中的機器進行自動調整。 您會透過建立 Azure Resource Manager 範本並使用 Azure PowerShell 部署它，來建立擴展集並設定調整。 如需範本的詳細資訊，請參閱 [編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。 若要深入了解擴展集的自動調整，請參閱 [Automatic scaling and Virtual Machine Scale Sets](virtual-machine-scale-sets-autoscale-overview.md)(自動調整和虛擬機器擴展集)。

在本文中，您可以部署下列資源和擴充：

* Microsoft.Storage/storageAccounts
* Microsoft.Network/virtualNetworks
* Microsoft.Network/publicIPAddresses
* Microsoft.Network/loadBalancers
* Microsoft.Network/networkInterfaces
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.Insights.VMDiagnosticsSettings
* Microsoft.Insights/autoscaleSettings

如需 Resource Manager 資源的詳細資訊，請參閱 [Azure Resource Manager 與傳統部署](../azure-resource-manager/resource-manager-deployment-model.md)。

## <a name="step-1-install-azure-powershell"></a>步驟 1：安裝 Azure PowerShell
如需如何安裝最新版 Azure PowerShell、選取訂用帳戶，以及登入 Azure 的相關資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview) 。

## <a name="step-2-create-a-resource-group-and-a-storage-account"></a>步驟 2：建立資源群組和儲存體帳戶
1. **建立資源群組** - 所有資源都必須部署至資源群組。 使用 [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx) 以建立名為 **vmsstestrg1**的資源群組。
2. **建立儲存體帳戶** - 此儲存體帳戶是範本的儲存位置。 使用 [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) 建立名為 **vmsstestsa**的儲存體帳戶。

## <a name="step-3-create-the-template"></a>步驟 3：建立範本
有了 Azure Resource Manager 範本之後，您就可以使用 JSON 來說明資源、相關設定和部署參數，一起部署和管理 Azure 資源。

1. 在您慣用的編輯器中，建立檔案 C:\VMSSTemplate.json ，並新增初始的 JSON 結構以支援範本。

    ```json
    {
      "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      },
      "variables": {
      },
      "resources": [
      ]
    }
    ```

2. 參數並非總是必要的項目，但它們能提供在部署範本時輸入值的方式。 在您新增至範本的參數父元素下，新增下列參數。

    ```json   
    "vmName": { "type": "string" },
    "vmSSName": { "type": "string" },
    "instanceCount": { "type": "string" },
    "adminUsername": { "type": "string" },
    "adminPassword": { "type": "securestring" },
    "resourcePrefix": { "type": "string" }
    ```
   
    * 用來存取擴展集內機器之個別虛擬機器的名稱。
    * 用來存放範本的儲存體帳戶名稱。
    * 最初在擴展集內建立的虛擬機器數目。
    * 虛擬機器之系統管理帳戶的名稱和密碼。
    * 建立以支援擴展集之資源的名稱前置詞。

3. 變數可以在範本中用來指定會經常變更的值或需要透過參數值組合建立的值。 在您新增至範本的變數父元素下，新增下列變數。

    ```json
    "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
    "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
    "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
    "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
    "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
    "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
    "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
    "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
      "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
    ```
   
    * 網路介面所使用的 DNS 名稱。

        * 虛擬網路和子網路的 IP 位址名稱和前置詞。
        * 虛擬網路、負載平衡器和網路介面的名稱和識別碼。
        * 與調整集內的機器相關聯之帳戶的儲存體帳戶名稱。
        * 安裝在虛擬機器上的診斷延伸模組的設定。 如需診斷延伸模組的詳細資訊，請參閱[使用 Azure Resource Manager 範本建立具有監控和診斷功能的 Windows 虛擬機器](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

4. 在您新增至範本的資源父元素下，新增下列儲存體帳戶資源： 此範本使用迴圈來建立儲存作業系統磁碟和診斷資料的五個建議的儲存體帳戶。 這組帳戶最多可在一個調整集內支援 100 個虛擬機器，這是目前的最大值。 每個儲存體帳戶的命名方式都相同，即變數中所定義的字母指示項，加上您在參數中為範本提供的前置詞。

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
      "apiVersion": "2015-06-15",
      "copy": {
        "name": "storageLoop",
        "count": 5
      },
      "location": "[resourceGroup().location]",
      "properties": { "accountType": "Standard_LRS" }
    },
    ```

5. 新增虛擬網路資源。 如需詳細資訊，請參閱 [網路資源提供者](../virtual-network/resource-groups-networking.md)。

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
        "subnets": [
          {
            "name": "subnet1",
            "properties": { "addressPrefix": "10.0.0.0/24" }
          }
        ]
      }
    },
    ```

6. 新增負載平衡器和網路介面所使用的公用 IP 位址資源。

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP1')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName1')]"
        }
      }
    },
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP2')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName2')]"
        }
      }
    },
    ```

7. 新增調整集所使用的負載平衡器資源。 如需詳細資料，請參閱 [Azure 資源管理員的負載平衡器支援](../load-balancer/load-balancer-arm.md)。

    ```json   
    {
      "apiVersion": "2015-06-15",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
              }
            }
          }
        ],
        "backendAddressPools": [ { "name": "bepool1" } ],
        "inboundNatPools": [
          {
            "name": "natpool1",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPortRangeStart": 50000,
              "frontendPortRangeEnd": 50500,
              "backendPort": 3389
            }
          }
        ]
      }
    },
    ```

8. 新增個別的虛擬機器所使用的網路介面資源。 由於擴展集內的機器無法直接透過公用 IP 位址來存取，因此必須在相同的虛擬網路中建立個別的虛擬機器，以從遠端存取該機器。

    ```json  
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
              },
              "subnet": {
                "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
              }
            }
          }
        ]
      }
    },
    ```

9. 在與擴展集相同的網路中新增個別的虛擬機器。

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": { "vmSize": "Standard_A1" },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "[concat(parameters('resourcePrefix'), 'os1')]",
            "vhd": {
              "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
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
        }
      }
    },
    ```

10. 新增虛擬機器擴展集資源，並指定在擴展集內所有虛擬機器上安裝的診斷擴充。 此資源有許多設定都與虛擬機器資源相類似。 主要差異是指定擴展集中虛擬機器數量的容量元素，以及指定如何對虛擬機器進行更新的 upgradePolicy。 在依照 dependsOn 元素的指示建立所有的儲存體帳戶之前，不會建立擴展集。

    ```json
    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "apiVersion": "2016-03-30",
      "name": "[parameters('vmSSName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "sku": {
        "name": "Standard_A1",
        "tier": "Standard",
        "capacity": "[parameters('instanceCount')]"
      },
      "properties": {
        "upgradePolicy": {
          "mode": "Manual"
        },
        "virtualMachineProfile": {
          "storageProfile": {
            "osDisk": {
              "vhdContainers": [
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4], '.blob.core.windows.net/vhds')]"
              ],
              "name": "vmssosdisk",
              "caching": "ReadOnly",
              "createOption": "FromImage"
            },
            "imageReference": {
              "publisher": "MicrosoftWindowsServer",
              "offer": "WindowsServer",
              "sku": "2012-R2-Datacenter",
              "version": "latest"
            }
          },
          "osProfile": {
            "computerNamePrefix": "[parameters('vmSSName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "networkconfig1",
                "properties": {
                  "primary": "true",
                  "ipConfigurations": [
                    {
                      "name": "ip1",
                      "properties": {
                        "subnet": {
                          "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                          }
                        ]
                      }
                    }
                  ]
                }
              }
            ]
          },
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Insights.VMDiagnosticsSettings",
                "properties": {
                  "publisher": "Microsoft.Azure.Diagnostics",
                  "type": "IaaSDiagnostics",
                  "typeHandlerVersion": "1.5",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                    "storageAccount": "[variables('diagnosticsStorageAccountName')]"
                  },
                  "protectedSettings": {
                    "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
                    "storageAccountKey": "[listkeys(variables('accountid'), '2015-06-15').key1]",
                    "storageAccountEndPoint": "https://core.windows.net"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

11. 新增 autoscaleSettings 資源，以定義擴展集如何根據擴展集中機器的處理器使用情形進行調整。

    ```json
    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Processor(_Total)\\% Processor Time",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 50.0
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      }
    }
    ```

    在本教學課程中，以下的值相當重要：
    
    * **metricName**  
    此值與我們在 wadperfcounter 變數中定義的效能計數器相同。 使用該變數時，診斷擴充將會收集 **Processor(_Total)\% Processor Time** 計數器。
    
    * **metricResourceUri**  
    此值是虛擬機器擴展集的資源識別碼。
    
    * **timeGrain**  
    此值是所收集之計量的精細度。 此範本中，此值設為 1 分鐘。
    
    * **statistic**  
    此值會決定如何結合計量以因應自動調整動作的需要。 可能的值為：Average、Min、Max。 在這個範本中，將會收集虛擬機器的平均總 CPU 使用率。
    
    * **timeWindow**  
    此值是收集執行個體資料的時間範圍。 其值必須介於 5 分鐘到 12 小時之間。
    
    * **timeAggregation**  
    此值會決定收集的資料應如何隨著時間結合。 預設值為 Average。 可能的值為：Average、Minimum、Maximum、Last、Total、Count。
    
    * **operator**  
    此值是用來比較計量資料和臨界值的運算子。 可能的值為：Equals、NotEquals、GreaterThan、GreaterThanOrEqual、LessThan、LessThanOrEqual。
    
    * **threshold**  
    此值是觸發調整動作的值。 在此範本中，會在調整集內的機器之間的平均 CPU 使用率超過 50% 時，將機器新增至調整集。
    
    * **direction**  
    此值會決定達到臨界值時所採取的動作。 可能的值為 Increase 或 Decrease。 在此範本中，如果臨界值在定義的時間範圍內超過 50%，則會增加調整集內的虛擬機器數目。
    
    * **type**  
    此值是所應採取的動作類型，必須設為 ChangeCount。
    
    * **value**  
    此值是從擴展集內新增或移除的虛擬機器數目。 此值必須是 1 或更大。 預設值為 1。 在此範本中，會在達到臨界值時，將調整集內的機器數目加 1。
    
    * **cooldown**  
    此值是在上一個調整動作之後、下一個動作執行之前的等待時間量。 此值必須介於一分鐘到一週之間。

12. 儲存範本檔案。    

## <a name="step-4-upload-the-template-to-storage"></a>步驟 4：將範本上傳至儲存體
只要您知道您在步驟 1 中建立之儲存體帳戶的名稱和主要金鑰，即可上傳範本。

1. 在 Microsoft Azure PowerShell 視窗中，設定一個變數來指定您在步驟 1 中建立的儲存體帳戶名稱。
   
    ```powershell
    $storageAccountName = "vmstestsa"
    ```

2. 設定一個變數，指定儲存體帳戶的主要金鑰。
   
    ```powershell
    $storageAccountKey = "<primary-account-key>"
    ```
   
   您可以在 Azure 入口網站中檢視儲存體帳戶資源，並按一下金鑰圖示，以取得此金鑰。
3. 建立用來驗證儲存體帳戶之作業的儲存體帳戶內容物件。
   
    ```powershell
    $ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    ```

4. 建立儲存範本的容器。

    ```powershell
    $containerName = "templates"
    New-AzureStorageContainer -Name $containerName -Context $ctx  -Permission Blob
    ```

5. 將範本檔案上傳至新的容器。

    ```powershell   
    $blobName = "VMSSTemplate.json"
    $fileName = "C:\" + $BlobName
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob  $blobName -Context $ctx
    ```

## <a name="step-5-deploy-the-template"></a>步驟 5：部署範本
建立範本後，您就可以開始部署資源。 使用下列命令啟動程序：

```powershell
New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"
```

當您按 Enter 鍵時，系統會提示您提供您所指派的變數值。 請提供下列值：

```powershell
vmName: vmsstestvm1
  vmSSName: vmsstest1
  instanceCount: 5
  adminUserName: vmadmin1
  adminPassword: VMpass1
  resourcePrefix: vmsstest
```

所有資源要順利部署完成，大約需要 15 分鐘。

> [!NOTE]
> 您也可以使用入口網站的功能來部署資源。 請使用此連結：「https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>」
> 
> 

## <a name="step-6-monitor-resources"></a>步驟 6：監視資源
您可以使用下列方法，取得關於虛擬機器調整集的某些資訊：

* Azure 入口網站 - 目前，使用入口網站可以取得限定數量的資訊。
* [Azure 資源總管](https://resources.azure.com/) - 此工具是瀏覽擴展集目前狀態的最佳選項。 按照此路徑，您應該會看到您所建立之調整集的執行個體檢視：
  
    subscriptions > {您的訂用帳戶} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

* Azure PowerShell - 使用下列命令可取得某些資訊：

  ```powershell
  Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  ```
  
  或
  
  ```powershell
  Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView
  ```

* 比照連接到任何其他機器的方式來連接到個別的虛擬機器，即可從遠端存取擴展集中的虛擬機器以監視個別的程序。

> [!NOTE]
> 在 [虛擬機器調整集](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-7-remove-the-resources"></a>步驟 7：移除資源
您將為 Azure 中所使用的資源支付費用，因此，刪除不再需要的資源永遠是最好的做法。 您不需要從資源群組個別刪除每個資源。 您可以刪除資源群組，其所有資源都將會自動刪除。

  ```powershell
  Remove-AzureRmResourceGroup -Name vmsstestrg1
  ```

如果您想要保留資源群組，您可以只刪除調整集。

  ```powershell
  Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"
  ```

## <a name="next-steps"></a>後續步驟
* 使用 [管理虛擬機器擴展集中的虛擬機器](virtual-machine-scale-sets-windows-manage.md)中的資訊，管理您剛建立的擴展集。
* 透過檢閱 [使用虛擬機器擴展集垂直自動調整](virtual-machine-scale-sets-vertical-scale-reprovision.md)
* 在 [Azure 監視器 PowerShell 快速入門範例](../monitoring-and-diagnostics/insights-powershell-samples.md)中找到 Azure 監視器監視功能的範例
* 若要深入了解通知功能，請參閱[使用自動調整動作在 Azure 監視器中傳送電子郵件和 Webhook 警示通知](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
* 深入了解如何[使用稽核記錄，在 Azure 監視器中傳送電子郵件和 Webhook 警示通知](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)

