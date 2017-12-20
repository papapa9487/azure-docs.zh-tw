---
title: "從 GitHub 下載 Azure Stack 工具 | Microsoft Docs"
description: "瞭解如何下載必要的工具來處理 Azure Stack。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 28F360AD-789A-488D-965F-FC6E6CCF3329
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: mabrigg
ms.openlocfilehash: d4f8a8d73f8e2ea321cb6cc1deda2301033b249d
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>從 GitHub 下載 Azure Stack 工具

AzureStack-Tools 是裝載 PowerShell 模組的 GitHub 存放庫，可用來管理和部署至 Azure Stack 的資源。 您可以針對 Azure Stack 開發套件，或針對以 Windows 為基礎的外部用戶端 (如果您打算建立 VPN 連線能力) 下載並使用這些 PowerShell 模組。 若要取得這些工具，請複製 GitHub 存放庫或下載 AzureStack-Tools 資料夾，方法是執行下列指令碼：

```PowerShell
# Change directory to the root directory 
cd \

# Download the tools archive
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>模組所提供的功能

AzureStack-Tools 存放庫包含 PowerShell 模組，其支援下列 Azure Stack 功能：  

| 功能 | 說明 | 可以使用此模組的人員？ |
| --- | --- | --- |
| [雲端功能](azure-stack-validate-templates.md) | 使用此模組來取得雲端的雲端功能。 例如，您可以針對 Azure Stack 和使用此模組的 Azure 雲端取得雲端功能，例如 API 版本、Azure Resource Manager 資源、VM 擴充功能等。 | 雲端系統管理員和使用者。 |
| [適用於 Azure Stack 的 Resource Manager 原則](azure-stack-policy-module.md) | 使用此模組來設定 Azure 訂用帳戶或 Azure 資源群組，包含與 Azure Stack 相同的版本和服務可用性。 | 雲端系統管理員和使用者 |
| [連線至 Azure Stack](azure-stack-connect-azure-stack.md) | 使用此模組，透過 PowerShell 連線至 Azure Stack 執行個體，並設定 Azure Stack 的 VPN 連線。 | 雲端系統管理員和使用者 |
| [範本驗證程式](azure-stack-validate-templates.md) | 使用此模組來確認現有或新範本是否可以部署到 Azure Stack。 | 雲端系統管理員和使用者 |


## <a name="next-steps"></a>後續步驟
* [設定 Azure Stack 使用者的 PowerShell 環境](azure-stack-powershell-configure-user.md)   
* [透過 VPN 連線至 Azure Stack 開發套件](azure-stack-connect-azure-stack.md)  
