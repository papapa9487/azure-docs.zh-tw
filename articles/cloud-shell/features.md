---
title: "Azure Cloud Shell (預覽) 中 Bash 的功能 | Microsoft Docs"
description: "Azure Cloud Shell 中 Bash 的功能概觀"
services: Azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: juluk
ms.openlocfilehash: 23faadc38fde75d4c83361465fdacc76d42d7523
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2017
---
# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Azure Cloud Shell 中 Bash 的功能和工具

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> 也提供 [PowerShell](features-powershell.md) 中的功能和工具。

Cloud Shell 中的 Bash 是在 `Ubuntu 16.04 LTS` 上執行。

## <a name="features"></a>特性

### <a name="secure-automatic-authentication"></a>安全的自動驗證

Cloud Shell 中的 Bash 會安全地自動驗證 Azure CLI 2.0 的帳戶存取。

### <a name="ssh-into-azure-linux-virtual-machines"></a>SSH 到 Azure Linux 虛擬機器

從 Azure CLI 2.0 建立 Linux VM 可以建立預設 SSH 金鑰，並將它放在您的 `$Home` 目錄中。 將 SSH 金鑰放在 `$Home` 中，會直接從 Cloud Shell 啟用與 Azure Linux VM 的直接 SSH 連線。 金鑰會保留在檔案共用的 acc_<user>.img 中，在使用或共用檔案共用或索引鍵的存取權時，請使用最佳做法。

### <a name="home-persistence-across-sessions"></a>工作階段之間的 $Home 持續性

若要在工作階段之間保存檔案，Cloud Shell 會在第一次啟動時逐步引導您連結 Azure 檔案共用。
完成後，Cloud Shell 會自動連結儲存體 (掛接為 `$Home\clouddrive`)，供所有未來的工作階段使用。
此外，在 Cloud Shell 的 Bash 中，於 Azure 檔案共用中，您的 `$Home` 目錄會保存為 .img。
在工作階段之間不會保存 `$Home` 外面的檔案和電腦狀態。

[深入了解在 Cloud Shell 中的 Bash 保存檔案。](persisting-shell-storage.md)

## <a name="tools"></a>工具

|類別   |名稱   |
|---|---|
|Linux 工具            |Bash<br> sh<br> tmux<br> dig<br>               |
|Azure 工具            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) \(英文\) 和 [1.0](https://github.com/Azure/azure-xplat-cli) \(英文\)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard) <br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) <br>    |
|文字編輯器           |vim<br> nano<br> emacs       |
|原始檔控制         |git                    |
|建置工具            |make<br> maven<br> npm<br> pip         |
|容器             |[Docker CLI](https://github.com/docker/cli) \(英文\)/[Docker Machine](https://github.com/docker/machine) \(英文\)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/) \(英文\)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli) \(英文\)         |
|資料庫              |MySQL 用戶端<br> PostgreSql 用戶端<br> [sqlcmd 公用程式](https://docs.microsoft.com/sql/tools/sqlcmd-utility) \(英文\)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|其他                  |iPython 用戶端<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli) \(英文\)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> |

## <a name="language-support"></a>語言支援

|語言   |版本   |
|---|---|
|.NET       |2.0.0       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|PowerShell |[6.0 (搶鮮版 (Beta))](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 和 3.5 (預設)|

## <a name="next-steps"></a>後續步驟
[Cloud Shell 中 Bash 的快速入門](quickstart.md) <br>
[了解 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) \(英文\)