---
title: "Azure Cloud Shell (預覽) 中 PowerShell 的功能 | Microsoft Docs"
description: "Azure Cloud Shell 中 PowerShell 的功能概觀"
services: Azure
documentationcenter: 
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: damaerte
ms.openlocfilehash: 9eacb57b5a00ff11739695ed3311be0c638ba25f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell"></a>Azure Cloud Shell 中 PowerShell 的功能和工具

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> 也提供 [Bash](features.md) 的功能和工具。

Cloud Shell 中的 PowerShell 是在 `Windows Server 2016` 上執行。

## <a name="features"></a>特性

### <a name="secure-automatic-authentication"></a>安全的自動驗證

Cloud Shell 中的 PowerShell 會安全地自動驗證 Azure PowerShell 的帳戶存取。

### <a name="files-persistence-across-sessions"></a>工作階段之間的檔案持續性

若要在工作階段之間保存檔案，Cloud Shell 會在第一次啟動時逐步引導您連結 Azure 檔案共用。
完成後，Cloud Shell 會自動連結儲存體 (掛接為 `$home\clouddrive`)，供所有未來的工作階段使用。
因為 Cloud Shell 的每個要求都會配置暫時電腦，所以不會在工作階段之間保存位於您 `$home\clouddrive` 外面的檔案和電腦狀態。

[深入了解將 Azure 檔案共用連結至 Cloud Shell。](persisting-shell-storage-powershell.md)

### <a name="azure-drive-azure"></a>Azure 磁碟機 (Azure:)

Cloud Shell 中的 PowerShell 可讓您在 Azure 磁碟機 (`Azure:`) 中開始。
Azure 磁碟機會啟用輕鬆探索和導覽與檔案系統導覽類似的 Azure 資源 (例如計算、網路、儲存體等等)。
您可以繼續使用熟悉的 [Azure PowerShell Cmdlet](https://docs.microsoft.com/en-us/powershell/azure) 來管理這些資源。
對 Azure 資源進行的任何變更 (在 Azure 入口網站中直接進行，或透過 Azure PowerShell Cmdlet) 都會立即反映在 Azure 磁碟機中。

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>內容感知

- **資源群組範圍**：在 Azure 磁碟機 (`Azure:`) 的資源群組路徑內容時，會自動將資源群組名稱傳遞給 Azure PowerShell Cmdlet。

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**：這個 Cmdlet 會傳回 Azure 磁碟機 (`Azure:`) 的位置內容中適用的命令清單。 例如，使用者位在 `Azure:\<subscription name>\StorageAccounts`下時，它只會顯示儲存體相關命令。

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>豐富 PowerShell 指令碼編輯

當您使用 VIM 編輯 PowerShell 檔案 (`.ps1,.psm1,.psd1`) 時，會自動取得語法反白顯示和 IntelliSense 支援。
IntelliSense 支援的實作是透過與 [PowerShell Editor Services](https://github.com/PowerShell/PowerShellEditorServices) 本機執行個體互動的 vim-plugin。

> [!TIP]
> 使用 `TAB` 以完成 (IntelliSense) 命令名稱、參數名稱和參數值 (適用時)。

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>可延伸模型

使用 [PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget)，即可從 [PowerShell 資源庫](https://www.powershellgallery.com)輕鬆地安裝 (和更新) 自訂模組和指令碼。
安裝之後，會自動在 Cloud Shell 工作階段之間保存您的模組。

> [!TIP]
> 使用者所安裝的模組會儲存在 `$Home\CloudDrive\.pscloudshell\WindowsPowerShell` 資料夾中。 此資料夾的符號連結是建立於使用者的 documents 資料夾 (`$home\Documents\WindowsPowerShell`) 中。

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>管理客體 VM

您可以使用 `Enter-AzureRmVM` 和 `Invoke-AzureRmVMCommand` 這兩個內建命令，遠端管理 Azure VM。
這些命令是以 PowerShell 遠端為建置基礎，並且需要與 Azure VM 的 PowerShell 連線。

## <a name="tools"></a>工具

|**類別**    |**名稱**                                 |
|----------------|-----------------------------------------|
|Azure 工具     |[Azure PowerShell (4.3.1)](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1) |
|文字編輯器    |vim<br> nano                             |
|套件管理員 |PowerShellGet<br> PackageManagement<br> npm<br> pip |
|原始檔控制  |git                                      |
|資料庫       |[SqlServer 模組](https://www.powershellgallery.com/packages/SqlServer)<br> [sqlcmd 公用程式](https://docs.microsoft.com/sql/tools/sqlcmd-utility) \(英文\)      |
|測試工具      |Pester                                   |

## <a name="language-support"></a>語言支援

|**語言**|**版本**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 和 [6.0 (搶鮮版 (Beta))](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>後續步驟

[Cloud Shell 中 PowerShell 的快速入門](quickstart-powershell.md) <br>
[了解 Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
