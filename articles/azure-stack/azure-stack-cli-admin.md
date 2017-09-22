---
title: "為 Azure Stack 使用者啟用 CLI | Microsoft Docs"
description: "了解如何使用跨平台命令列介面 (CLI) 在 Azure Stack 上管理及部署資源"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: sngun
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 2f7615e0f0928f4ef70f98b7e2b2dce823621314
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="enable-cli-for-azure-stack-users"></a>為 Azure Stack 使用者啟用 CLI

您無法使用 CLI 執行任何 Azure Stack 操作員特定工作。 但是在使用者可透過 CLI 管理資源之前，Azure Stack 操作員必須為使用者提供下列項目：

* **Azure Stack CA 根憑證**：如果使用者是從 Azure Stack 開發套件以外的工作站使用 CLI，就需要根憑證。  

* **虛擬機器別名端點**：使用 CLI 建立虛擬機器時需要此端點。

下列各節會說明如何取得這些值。

## <a name="export-the-azure-stack-ca-root-certificate"></a>匯出 Azure Stack CA 根憑證

您可以在開發套件中，以及在開發套件環境內執行的租用戶虛擬機器中找到 Azure Stack CA 根憑證。 登入您的開發套件或租用戶虛擬機器，然後執行下列指令碼，以 PEM 格式匯出 Azure Stack 根憑證：

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Cerficate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>設定虛擬機器別名端點

Azure Stack 操作員應該設定可公開存取，且包含虛擬機器映像別名的端點。 Azure Stack 操作員必須先[將映像下載到 Azure Stack 市集](azure-stack-download-azure-marketplace-item.md)，再將它們新增到映像別名端點。
   
例如，Azure 包含使用以下 URI：https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json。 操作員應該使用其市集中可用的映像，為 Azure Stack 設定類似的端點。

## <a name="next-steps"></a>後續步驟

[使用 Azure CLI 部署範本](azure-stack-deploy-template-command-line.md)

[使用 PowerShell 連線](azure-stack-connect-powershell.md)

[管理使用者權限](azure-stack-manage-permissions.md)


