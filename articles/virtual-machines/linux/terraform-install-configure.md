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
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: 533add8948544e37dc27623a2f51aad1054b1bef
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>安裝和設定 Terraform 以在 Azure 中佈建 VM 和的其他基礎結構
 
Terraform 使用[簡易範本化語言](https://www.terraform.io/docs/configuration/syntax.html)，提供簡單的方法來定義、預覽及部署雲端基礎結構。 本文說明在 Azure 中使用 Terraform 來佈建資源的必要步驟。 

> [!TIP]
> Terraform 預設安裝在 [Azure Cloud Shell 中的 Bash 體驗](/azure/cloud-shell/quickstart)。 它也會使用認證和 [Azure Terraform 模組](https://registry.terraform.io/modules/Azure)預先設定。 藉由使用 Cloud Shell，您可以略過此文件的安裝/設定部分。

## <a name="install-terraform"></a>安裝 Terraform

若要安裝 Terraform，請將適合您作業系統的套件[下載](https://www.terraform.io/downloads.html)到個別的安裝目錄中。 此下載包含單一可執行檔，您也應該為其定義全域路徑。 如需如何在 Linux 和 Mac 上設定路徑的指示，請移至[此網頁](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)。 如需如何在 Windows 上設定路徑的指示，請移至[此網頁](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)。 

使用 `terraform` 命令確認路徑組態。 您應該會在輸出看到一份可用的 Terraform 選項清單：

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>設定 Terraform 對 Azure 的 存取權

設定 [Azure AD 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)以讓 Terraform 將資源佈建至 Azure。 此服務主體會授權您的 Terraform 指令碼使用認證在 Azure 訂用帳戶中佈建資源。

有數種方式可建立 Azure AD 應用程式和 Azure AD 服務主體。 現今最簡單又最快速的方式是使用 Azure CLI 2.0，[您可以在 Windows、Linux 或 Mac 上進行下載和安裝](/cli/azure/install-azure-cli)。

發出下列命令來登入以管理您的 Azure 訂用帳戶：

   `az login`

如果您有多個 Azure 訂用帳戶，`az login` 命令會傳回其詳細資料。 設定 `SUBSCRIPTION_ID` 環境變數，以保存從您要使用之訂用帳戶傳回的 `id` 欄位值。 

為此工作階段設定您要使用的訂用帳戶。

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

查詢帳戶，以取得訂用帳戶識別碼和租用戶識別碼值。

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

接下來，為 Terraform 建立不同的認證。

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

會傳回您的 appId、密碼、sp_name 和租用戶。 記下 appId 和密碼。

若要測試您的認證，請開啟新的殼層，並使用 sp_name、密碼及租用戶傳回的值執行下列命令：

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>設定 Terraform 環境變數

設定 Terraform 以在建立 Azure 資源時使用來自服務主體的租用戶識別碼、訂用帳戶識別碼、用戶端識別碼和用戶端機密。 設定下列環境變數，[Azure Terraform 模組](https://registry.terraform.io/modules/Azure)會自動使用這些變數。

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

您可以使用此範例殼層指令碼來設定這些變數：

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

## <a name="run-a-sample-script"></a>執行指令碼範例

在空的目錄中建立檔案 `test.tf`，並在下列指令碼中貼上。 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

儲存檔案，然後執行 `terraform init`。 此命令會下載建立 Azure 資源群組所需的 Azure 模組。 您看到下列輸出：

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

使用 `terraform plan` 預覽指令碼，然後使用 `terraform apply` 建立 `testResouceGroup` 資源群組：

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>後續步驟

您已安裝 Terraform 並設定 Azure 認證，可以開始將基礎結構部署到您的 Azure 訂用帳戶中。 您接著會透過建立空的 Azure 資源群組來測試您的安裝。

> [!div class="nextstepaction"]
> [使用 Terraform 建立 Azure VM](terraform-create-complete-vm.md)

