---
title: "在 Azure 虛擬機器擴展集上部署應用程式 | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 範本，在虛擬機器擴展集上部署簡單的自動調整應用程式。"
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/24/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 07883a33382cc660b043c99872312a9e77228253
ms.contentlocale: zh-tw
ms.lasthandoff: 08/25/2017

---

# <a name="deploy-an-autoscaling-app-using-a-template"></a>使用範本部署自動調整應用程式

[Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)是部署相關資源群組的絕佳方式。 本教學課程是以[部屬簡單的擴展集](virtual-machine-scale-sets-mvss-start.md)為基礎，並說明如何使用 Azure Resource Manager 範本，在擴展集上部署簡單的自動調整應用程式。  您也可以使用 PowerShell、CLI 或入口網站設定自動調整。 如需詳細資訊，請參閱[自動調整概觀](virtual-machine-scale-sets-autoscale-overview.md)。

## <a name="two-quickstart-templates"></a>兩個快速入門範本
當您部署擴展集時，您可以使用 [VM 擴充功能](../virtual-machines/virtual-machines-windows-extensions-features.md)在平台映像上安裝新軟體。 VM 擴充功能是小型的應用程式，可在 Azure 虛擬機器上提供部署後設定和自動化工作，例如部署應用程式。 [Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) 中會提供兩個不同的範例範本，其顯示如何使用 VM 擴充功能在擴展集上部署自動調整應用程式。

### <a name="python-http-server-on-linux"></a>Linux 上的 Python HTTP 伺服器
[Linux 上的 Python HTTP 伺服器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)範例範本可部署在 Linux 擴展集上執行的簡單自動調整應用程式。  [Bottle](http://bottlepy.org/docs/dev/)、Python Web 架構以及簡單的 HTTP 伺服器都是使用 VM 擴充功能部署在擴展集中的每部 VM 上。 當所有 VM 的平均 CPU 使用率大於 60% 時，擴展集會相應放大，而當平均 CPU 使用率小於 30% 時，擴展集會相應縮小。

除了擴展集資源以外，*azuredeploy.json* 範例範本也會宣告虛擬網路、公用 IP 位址、負載平衡器和自動調整設定資源。  如需在範本中建立這些資源的詳細資訊，請參閱[具備自動調整功能的 Linux 擴展集](virtual-machine-scale-sets-linux-autoscale.md)。

在 *azuredeploy.json* 範本中，`Microsoft.Compute/virtualMachineScaleSets` 資源的 `extensionProfile` 屬性可指定自訂指令碼擴充功能。 `fileUris` 指定指令碼位置。 在此情況下，兩個檔案︰*workserver.py*可定義簡單的 HTTP 伺服器，以及 *installserver.sh* 可安裝 Bottle 並啟動 HTTP 伺服器。 `commandToExecute` 指定要在部署擴展集之後執行的命令。

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
                "properties": {
                  "publisher": "Microsoft.Azure.Extensions",
                  "type": "CustomScript",
                  "typeHandlerVersion": "2.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "fileUris": [
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
                    ],
                    "commandToExecute": "bash installserver.sh"
                  }
                }
              }
            ]
          }
```

### <a name="aspnet-mvc-application-on-windows"></a>Windows 上的 ASP.NET MVC 應用程式
[Windows 上的 ASP.NET MVC 應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale)範例範本可部署一個簡單的 ASP.NET MVC 應用程式，其在 Windows 擴展集上的 IIS 中執行。  IIS 和 MVC 應用程式是使用 [PowerShell 預期狀態設定 (DSC)](virtual-machine-scale-sets-dsc.md) VM 擴充功能進行部署。  當 CPU 使用率大於 50% 長達 5 分鐘時，擴展集會 (一度在 VM 執行個體上) 相應增加。 

除了擴展集資源以外，*azuredeploy.json* 範例範本也會宣告虛擬網路、公用 IP 位址、負載平衡器和自動調整設定資源。 此範本也會示範應用程式升級。  如需在範本中建立這些資源的詳細資訊，請參閱[具備自動調整的 Windows 擴展集](virtual-machine-scale-sets-windows-autoscale.md)。

在 *azuredeploy.json* 範本中，`Microsoft.Compute/virtualMachineScaleSets` 資源的 `extensionProfile` 屬性會指定[期望狀態組態 (DSC)](virtual-machine-scale-sets-dsc.md) 擴充功能，該功能可從 WebDeploy 套件安裝 IIS 和預設 Web 應用程式。  *IISInstall.ps1* 指令碼會在虛擬機器上安裝 IIS 並且位於 *DSC* 資料夾中。  MVC Web 應用程式位於 *WebDeploy* 資料夾中。  指令碼的安裝路徑和 Web 應用程式是定義於 *azuredeploy.parameters.json* 檔案的 `powershelldscZip` 和 `webDeployPackage` 參數中。 

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

## <a name="deploy-the-template"></a>部署範本
若要部署 [Linux 上的 Python HTTP 伺服器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)或 [Windows 上的 ASP.NET MVC 應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale)範本，最簡單的方式就是使用 GitHub 讀我檔案中的 [部署至 Azure] 按鈕。  您也可以使用 PowerShell 或 Azure CLI 來部署範本範例。

### <a name="powershell"></a>PowerShell
從 GitHub 儲存機制將 [Linux 上的 Python HTTP 伺服器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)或 [Windows 上的 ASP.NET MVC 應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale)檔案複製到本機電腦上的資料夾。  開啟 *azuredeploy.parameters.json* 檔案並更新 `vmssName`、`adminUsername` 和 `adminPassword` 參數的預設值。 將下列 PowerShell 指令碼儲存至相同資料夾中的 *deploy.ps1* 作為 *azuredeploy.json* 範本。 若要部署範例範本，請從 PowerShell 命令視窗執行 *deploy.ps1* 指令碼。

```powershell
param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "template.json",

 [string]
 $parametersFilePath = "parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @("microsoft.compute","microsoft.insights","microsoft.network");
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
```

### <a name="azure-cli"></a>Azure CLI
```azurecli
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

# -e: immediately exit if any command has a non-zero exit status
# -o: prevents errors in a pipeline from being masked
# IFS new value is less likely to cause confusing bugs when looping arrays or arguments (e.g. $@)

usage() { echo "Usage: $0 -i <subscriptionId> -g <resourceGroupName> -n <deploymentName> -l <resourceGroupLocation>" 1>&2; exit 1; }

declare subscriptionId=""
declare resourceGroupName=""
declare deploymentName=""
declare resourceGroupLocation=""

# Initialize parameters specified from command line
while getopts ":i:g:n:l:" arg; do
    case "${arg}" in
        i)
            subscriptionId=${OPTARG}
            ;;
        g)
            resourceGroupName=${OPTARG}
            ;;
        n)
            deploymentName=${OPTARG}
            ;;
        l)
            resourceGroupLocation=${OPTARG}
            ;;
        esac
done
shift $((OPTIND-1))

#Prompt for parameters is some required parameters are missing
if [[ -z "$subscriptionId" ]]; then
    echo "Subscription Id:"
    read subscriptionId
    [[ "${subscriptionId:?}" ]]
fi

if [[ -z "$resourceGroupName" ]]; then
    echo "ResourceGroupName:"
    read resourceGroupName
    [[ "${resourceGroupName:?}" ]]
fi

if [[ -z "$deploymentName" ]]; then
    echo "DeploymentName:"
    read deploymentName
fi

if [[ -z "$resourceGroupLocation" ]]; then
    echo "Enter a location below to create a new resource group else skip this"
    echo "ResourceGroupLocation:"
    read resourceGroupLocation
fi

#templateFile Path - template file to be used
templateFilePath="template.json"

if [ ! -f "$templateFilePath" ]; then
    echo "$templateFilePath not found"
    exit 1
fi

#parameter file path
parametersFilePath="parameters.json"

if [ ! -f "$parametersFilePath" ]; then
    echo "$parametersFilePath not found"
    exit 1
fi

if [ -z "$subscriptionId" ] || [ -z "$resourceGroupName" ] || [ -z "$deploymentName" ]; then
    echo "Either one of subscriptionId, resourceGroupName, deploymentName is empty"
    usage
fi

#login to azure using your credentials
az account show 1> /dev/null

if [ $? != 0 ];
then
    az login
fi

#set the default subscription id
az account set --name $subscriptionId

set +e

#Check for existing RG
az group show $resourceGroupName 1> /dev/null

if [ $? != 0 ]; then
    echo "Resource group with name" $resourceGroupName "could not be found. Creating new resource group.."
    set -e
    (
        set -x
        az group create --name $resourceGroupName --location $resourceGroupLocation 1> /dev/null
    )
    else
    echo "Using existing resource group..."
fi

#Start deployment
echo "Starting deployment..."
(
    set -x
    az group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file $templateFilePath --parameters $parametersFilePath
)

if [ $?  == 0 ];
 then
    echo "Template has been successfully deployed"
fi
```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

