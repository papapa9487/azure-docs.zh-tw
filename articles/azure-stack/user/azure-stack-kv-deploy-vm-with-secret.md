---
title: "使用安全地存放在 Azure Stack 上的密碼部署 VM | Microsoft Docs"
description: "了解如何使用存放在 Azure Stack Key Vault 中的密碼部署 VM"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/08/2017
ms.author: mabrigg
ms.openlocfilehash: 8d9a2cebd7a28ca13cf88518a7c83b217af4c0e1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-virtual-machine-by-retrieving-the-password-stored-in-a-key-vault"></a>擷取存放在 Key Vault 中的密碼來建立虛擬機器

當您需要在部署期間傳遞安全值 (例如密碼) 時，可以將該值儲存為 Azure Stack Key Vault 中的祕密，並在 Azure Resource Manager 範本中加以參考。 您不需要在每次部署資源時手動輸入祕密，您也可以指定哪些使用者或服務主體可以存取祕密。 

在本文中，我們會針對如何擷取存放在 Key Vault 中的密碼，以完成在 Azure Stack 中部署 Windows 虛擬機器，逐步說明所需的步驟。 因此，密碼絕不會以純文字的形式，放在範本參數檔案中。 您可以從 Azure Stack 開發套件，或從外部用戶端 (如果是透過 VPN 連線) 來使用這些步驟。

## <a name="prerequisites"></a>必要條件
 
* 您必須訂閱包含 Key Vault 服務的供應項目。  
* [安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)  
* [設定 Azure Stack 使用者的 PowerShell 環境。](azure-stack-powershell-configure-user.md)

下列步驟說明擷取存放在 Key Vault 中的密碼來建立虛擬機器所需的程序：

1. 建立 Key Vault 祕密。
2. 更新 azuredeploy.parameters.json 檔案。
3. 部署範本。

## <a name="create-a-key-vault-secret"></a>建立 Key Vault 祕密

下列指令碼會建立金鑰保存庫，並將密碼存放為金鑰保存庫中的祕密。 建立金鑰保存庫時，請使用 `-EnabledForDeployment` 參數。 此參數可確保您能夠從 Azure Resource Manager 範本參考金鑰保存庫。

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

當您執行上述指令碼時，輸出會包含祕密 URI。 請記下此 URI。 在[使用金鑰保存庫範本中的密碼部署 Windows 虛擬機器](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) \(英文\) 中，您必須參考該 URI。 將 [101-vm-secure-passwor](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) \(英文\) 資料夾下載至您的開發電腦上。 此資料夾中包含 `azuredeploy.json` 和 `azuredeploy.parameters.json` 檔案，您在接下來的步驟中將需要這些檔案。

根據您的環境值，修改 `azuredeploy.parameters.json` 檔案。 要注意的參數是保存庫名稱、保存庫資源群組以及祕密 URI (產生自先前的指令碼)。 下列檔案是參數檔案的範例：

## <a name="update-the-azuredeployparametersjson-file"></a>更新 azuredeploy.parameters.json 檔案

根據您的環境，以 KeyVault URI、secretName、虛擬機器 adminUsername 的值來更新 azuredeploy.parameters.json 檔案。 下列 JSON 檔案會顯示範本參數檔案的範例： 

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>範本部署

現在，使用下列 PowerShell 指令碼部署範本：

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
成功部署範本之後，會產生下列輸出：

![部署輸出](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)


## <a name="next-steps"></a>後續步驟
[使用 Key Vault 部署範例應用程式](azure-stack-kv-sample-app.md)

[使用 Key Vault 憑證部署 VM](azure-stack-kv-push-secret-into-vm.md)

