---
title: "搭配使用 Terraform 與 Azure Cloud Shell"
description: "使用 Terraform 與 Azure Cloud Shell 來簡化驗證與範本設定。"
keywords: "terraform，devops，擴展集，虛擬機器， 網路，儲存體，模組"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 253e5d341f93e61d851893eb05832fbf35707cfc
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="terraform-cloud-shell-development"></a>Terraform Cloud Shell 開發 

Terraform 非常適合從 macOS 終端機的 Bash 命令列，或在 Windows 或 Linux 上的 Bash 中運作。 在 [Azure Cloud Shell](/azure/cloud-shell/overview) 的 Bash 體驗中執行您的 Terraform 設定，具有可加速開發週期的一些獨特優點。

此概念本文章涵蓋 Cloud Shell 功能，能協助您撰寫部署至 Azure 的 Terraform 指令碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>自動認證設定

Terraform 已安裝且立即可在 Cloud Shell 中使用。 不使用任何額外的設定登入 Cloud Shell 來管理結礎結構時，使用 Azure 驗證 Terraform 指令碼。 自動驗證會略過手動建立 Active Directory 服務主體，及設定 Azure Terraform 提供者變數的必要。


## <a name="using-modules-and-providers"></a>使用模組和提供者

Azure Terraform 模組需要認證才能存取和變更您的 Azure 訂用帳戶中的資源。 在 Cloud Shell 中工作時，會將下列程式碼新增至您的指令碼，以便在 Cloud Shell 中使用 Azure Terraform 模組：

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

使用任何 `terraform` CLI 命令時，Cloud Shell 會透過環境變數傳遞 `azurerm` 提供者的必要值。

## <a name="other-cloud-shell-developer-tools"></a>其他 Cloud Shell 開發人員工具

檔案和殼層狀態會保存在 Cloud Shell 工作階段之間的 Azure 儲存體中。 使用 [Azure 儲存體總管](/azure/vs-azure-tools-storage-manage-with-storage-explorer)，將檔案從本機電腦複製並上傳至 Cloud Shell。

Azure CLI 2.0 隨附於 Cloud Shell，是在完成 `terraform apply` 或 `terraform destroy` 後測試設定和檢查工作的適用工具。


## <a name="next-steps"></a>後續步驟

[使用模組登錄建立小 VM 叢集](terraform-create-vm-cluster-module.md)
[使用自訂 HCL 建立小 VM 叢集](terraform-create-vm-cluster-with-infrastructure.md)
