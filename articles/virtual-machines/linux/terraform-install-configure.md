---
title: "安裝和設定 Terraform 以在 Azure 中佈建 VM 和的其他基礎結構 | Microsoft Docs"
description: "了解如何安裝和設定 Terraform 以建立 Azure 資源"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: HT
ms.sourcegitcommit: 8727e15448a74b68277c5bdd82c573e817254f80
ms.openlocfilehash: da567097be38ac649c6bf1de1508de24d21cb877
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>安裝和設定 Terraform 以在 Azure 中佈建 VM 和的其他基礎結構 
本文說明安裝和設定 Terraform 以在 Azure 中佈建資源 (例如虛擬機器) 的必要步驟。 您將了解如何建立及使用 Azure 認證來啟用 Terraform，進而以安全的方式佈建雲端資源。

HashiCorp Terraform 使用稱為 HashiCorp 組態語言 (HCL) 的自訂範本化語言，提供簡單的方法來定義及部署雲端基礎結構。 此自訂語言[容易撰寫且易於了解](terraform-create-complete-vm.md)。 此外，使用 `terraform plan` 命令，您可以在認可之前視覺化基礎結構的變更。 請遵循下列步驟以開始在 Azure 中使用 Terraform。

## <a name="install-terraform"></a>安裝 Terraform
若要安裝 Terraform，請將適合您作業系統的套件[下載](https://www.terraform.io/downloads.html)到個別的安裝目錄中。 此下載包含單一可執行檔，您也應該為其定義全域路徑。 如需如何在 Linux 和 Mac 上設定路徑的指示，請移至[此網頁](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)。 如需如何在 Windows 上設定路徑的指示，請移至[此網頁](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)。 若要確認您的安裝，請執行 `terraform` 命令。 您應該會在輸出看到一份可用的 Terraform 選項清單。

接著，您必須允許 Terraform 存取您 Azure 訂用帳戶以執行基礎結構佈建。

## <a name="set-up-terraform-access-to-azure"></a>設定 Terraform 對 Azure 的 存取權
若要讓 Terraform 能夠將資源佈建至 Azure，您必須在 Azure Active Directory (Azure AD) 中建立兩個實體：Azure AD 應用程式和 Azure AD 服務主體。 然後，您可以在 Terraform 指令碼中使用這些實體的識別碼。 服務主體是全域 Azure AD 應用程式的本機執行個體。 服務主體允許對全域資源進行細微的本機存取控制。

有數種方式可建立 Azure AD 應用程式和 Azure AD 服務主體。 現今最簡單又最快速的方式是使用 Azure CLI 2.0，[您可以在 Windows、Linux 或 Mac 上進行下載和安裝](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)。 您也可以使用 PowerShell 或 Azure CLI 1.0 來建立必要的安全性基礎結構。 下列指示說明如何使用這些方法針對 Azure 設定 Terraform。

### <a name="use-azure-cli-20-for-windows-linux-or-mac-users"></a>使用 Azure CLI 2.0 (適用於 Windows、Linux 或 Mac 使用者) 
下載並安裝 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) 之後，請發出下列命令來登入以管理您的 Azure 訂用帳戶：

```
az login
```

>[!NOTE]
>如果您使用中國、Azure Germany 或 Azure Government 的雲端，則必須先設定 Azure CLI 才能使用該雲端。 您可以執行下列命令來執行這項作業：

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

如果您有多個 Azure 訂用帳戶，`az login` 命令會傳回其詳細資料。 設定 `SUBSCRIPTION_ID` 環境變數，以保存從您要使用之訂用帳戶傳回的 `id` 欄位值。 

為此工作階段設定您要使用的訂用帳戶。

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

查詢帳戶，以取得訂用帳戶識別碼和租用戶識別碼值。

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

接下來，為 Terraform 建立不同的認證。

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

會傳回您的 appId、密碼、sp_name 和租用戶。 記下 appId 和密碼。

若要確認您的認證 (服務主體)，請開啟新的殼層並執行下列命令。 取代 sp_name、密碼和租用戶的傳回值：

```
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

### <a name="use-powershell-for-windows-users"></a>使用 PowerShell (適用於 Windows 使用者) 
若要使用 Windows 電腦來撰寫和執行您的 Terraform 指令碼，並且使用 PowerShell 進行設定工作，請先使用適當的 PowerShell 工具來設定您的電腦。 

1. 依照[安裝和設定 Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps) \(英文\) 中的步驟安裝 PowerShell 工具。 

2. 下載 [azure-setup.ps1 指令碼](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1)並從 PowerShell 主控台執行。

3. 若要執行 azure-setup.ps1 指令碼，請下載並從 PowerShell 主控台執行 `./azure-setup.ps1 setup` 命令。 然後使用系統管理權限登入您的 Azure 訂用帳戶。

4. 出現提示時，提供應用程式名稱 (任意字串，必填)。 出現提示時，選擇性提供強式密碼。 如果您未提供密碼，則會使用 .NET 安全性程式庫為您產生強式密碼。

### <a name="use-azure-cli-10-for-linux-or-mac-users"></a>使用 Azure CLI 1.0 (適用於 Linux 或 Mac 使用者)
若要在採用 Azure CLI 1.0 的 Linux 電腦或 Mac 上開始使用 Terraform，請在電腦上安裝適當的程式庫。  

1. 依照[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 中的步驟，安裝 Azure xPlat CLI 工具。 

2. 依照[下載 jq](https://stedolan.github.io/jq/download/) \(英文\) 中的指示，下載並安裝 JSON 處理器。

3. 下載 [azure-setup.ps 指令碼](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) 並從主控台執行此 bash 指令碼。

4. 若要執行 azure-setup.sh 指令碼，請下載並從主控台執行 `./azure-setup setup` 命令。 然後使用系統管理權限登入您的 Azure 訂用帳戶。
 
5. 出現提示時，提供應用程式名稱 (任意字串，必填)。 出現提示時，選擇性提供強式密碼。 如果您未提供密碼，則會使用 .NET 安全性程式庫為您產生強式密碼。

先前所有的指令碼都會建立 Azure AD 應用程式和服務主體。 服務主體會取得訂用帳戶上參與者或擁有者層級的存取權。 因為授與高階存取權，所以您應該一律保護這些指令碼所產生的安全性資訊。 記下那些指令碼所提供的四個安全性資訊：appId、密碼、subscription_id 和 tenant_id。

## <a name="set-environment-variables"></a>設定環境變數
建立並設定 Azure AD 服務主體之後，您必須讓 Terraform 知道要使用的租用戶識別碼、訂用帳戶識別碼、用戶端識別碼和用戶端祕密。 您可以如[使用 Terraform 建立基本基礎結構](terraform-create-complete-vm.md)所述，在 Terraform 指令碼中內嵌這些值來執行此作業。 或者，您也可以設定下列環境變數 (也可因此避免不小心簽入或共用您的認證)：

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

您可以使用此範例殼層指令碼來設定這些變數：

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

此外，如果您在中國的 Azure、Azure Government 或 Azure Germany 使用 Terraform，您需要適當地設定 ENVIRONMENT 變數。

## <a name="next-steps"></a>後續步驟
您現在已安裝 Terraform 並設定 Azure 認證，可以開始將基礎結構部署到您的 Azure 訂用帳戶中。 接下來，深入了解如何[使用 Terraform 建立基礎結構](terraform-create-complete-vm.md)。

