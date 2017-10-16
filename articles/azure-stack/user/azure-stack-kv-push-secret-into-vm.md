---
title: "使用安全地存放在 Azure Stack 上的憑證部署虛擬機器 | Microsoft 文件"
description: "了解如何使用 Azure Stack 中的金鑰保存庫來部署虛擬機器，並將憑證推送至該虛擬機器"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 29ccdc9eca9911b2f550f9e09da83d0b1d30f9db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>建立虛擬機器，並加入從金鑰保存庫擷取的憑證

本文可協助您在 Azure Stack 中建立虛擬機器，並將憑證推送至該虛擬機器。 

## <a name="prerequisites"></a>必要條件

* 您必須訂閱包含 Key Vault 服務的供應項目。 
* [安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)  
* [設定 Azure Stack 使用者的 PowerShell 環境](azure-stack-powershell-configure-user.md)

Azure Stack 中的金鑰保存庫可用來存放憑證。 憑證在許多不同情況下都很有幫助。 例如，假設您在 Azure Stack 中有一個虛擬機器正在執行需要憑證的應用程式。 此憑證可用於加密、向 Active Directory 進行驗證，或用於網站上的 SSL。 將憑證存放在金鑰保存庫有助於確保憑證的安全。

在本文中，我們會針對如何將憑證推送至 Azure Stack 中的 Windows 虛擬機器，逐步說明所需的步驟。 您可以從 Azure Stack 開發套件，或從 Windows 外部用戶端 (如果是透過 VPN 連線) 來使用這些步驟。

下列步驟說明將憑證推送至虛擬機器所需的程序：

1. 建立 Key Vault 祕密。
2. 更新 azuredeploy.parameters.json 檔案。
3. 部署範本

## <a name="create-a-key-vault-secret"></a>建立 Key Vault 祕密

下列指令碼會建立 .pfx 格式的憑證、建立金鑰保存庫，並將憑證存放在金鑰保存庫中當做祕密。 建立金鑰保存庫時，您必須使用 `-EnabledForDeployment` 參數。 此參數可確保您能夠從 Azure Resource Manager 範本參考金鑰保存庫。

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

當您執行上述指令碼時，輸出會包含祕密 URI。 請記下此 URI。 在[將憑證推送至 Windows Resource Manager 範本](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) \(英文\) 中，您必須參考此 URI。 將 [vm-push-certificate-windows 範本](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) \(英文\) 資料夾下載至您的開發電腦上。 此資料夾中包含 `azuredeploy.json` 和 `azuredeploy.parameters.json` 檔案，您在接下來的步驟中將需要這些檔案。

根據您的環境值，修改 `azuredeploy.parameters.json` 檔案。 要注意的參數是保存庫名稱、保存庫資源群組以及祕密 URI (產生自先前的指令碼)。 下列檔案是參數檔案的範例：

## <a name="update-the-azuredeployparametersjson-file"></a>更新 azuredeploy.parameters.json 檔案

根據您的環境，以 vaultName、祕密 URI、VmName 和其他值來更新 azuredeploy.parameters.json 檔案。 下列 JSON 檔案會顯示範本參數檔案的範例： 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>部署範本

現在，使用下列 PowerShell 指令碼部署範本：

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

成功部署範本之後，會產生下列輸出：

![部署輸出](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

部署此虛擬機器時，Azure Stack 會將憑證推送至虛擬機器。 在 Windows 中，系統會利用使用者提供的憑證存放區，將憑證新增至 LocalMachine 憑證位置。 在 Linux 中，憑證會置於 /var/lib/waagent 目錄底下，其中 X509 憑證檔案的檔案名稱為 &lt;UppercaseThumbprint&gt;.crt，且私密金鑰的檔案名稱為 &lt;UppercaseThumbprint&gt;.prv。

## <a name="retire-certificates"></a>淘汰憑證

在上一節中，我們示範如何將新的憑證推送至虛擬機器。 您的舊憑證仍然在虛擬機器上，而且無法移除。 不過，您可以使用 `Set-AzureKeyVaultSecretAttribute` Cmdlet 來停用舊版的祕密。 以下是此 Cmdlet 的使用範例。 請務必根據您的環境，取代保存庫名稱、祕密名稱和版本值：

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>後續步驟

* [使用金鑰保存庫密碼部署 VM](azure-stack-kv-deploy-vm-with-secret.md)
* [允許應用程式存取 Key Vault](azure-stack-kv-sample-app.md)


