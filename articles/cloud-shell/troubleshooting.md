---
title: "Azure Cloud Shell (預覽) 疑難排解 | Microsoft Docs"
description: "針對 Azure Cloud Shell 進行疑難排解"
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: damaerte
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 32d4d4d10e5d8986e2dfe94430f52db8f038e245
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="troubleshooting-azure-cloud-shell"></a>針對 Azure Cloud Shell 進行疑難排解
Azure Cloud Shell 問題的已知解決方式包括：

## <a name="error-400-disallowedoperation"></a>錯誤：400 DisallowedOperation
 - **詳細資料**：使用 Azure Active Directory 訂用帳戶時，您無法建立儲存體。
 - **解決方式**：使用能夠建立儲存體資源的 Azure 訂用帳戶。 Azure AD 訂用帳戶無法建立 Azure 資源。

## <a name="powershell-resolutions"></a>PowerShell 解決方式

### <a name="no-home-directory-persistence"></a>沒有 $Home 目錄持續性
  - **詳細資料**：在 PowerShell 工作階段之間不會保存任何將資料寫入 $Home 的應用程式 (例如：git、vim 和其他項目)。
  - **解決方式**：在 PowerShell 設定檔中，以在 $Home 的 `clouddrive` 中建立應用程式特定資料夾的符號連結。

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl+C 不會結束 Cmdlet 提示字元
 - **詳細資料**：嘗試結束 Cmdlet 提示字元時，`Ctrl+C` 不會結束提示字元。
 - **解決方式**：若要結束提示，請依序按 `Ctrl+C` 和 `Enter`。

### <a name="gui-applications-are-not-supported"></a>不支援 GUI 應用程式
  - **詳細資料**：如果使用者啟動 GUI 應用程式，則不會傳回提示。  例如，使用者使用 `git` 關閉已啟用雙因素驗證的私人存放庫時，會針對雙因素驗證程式碼顯示一個對話方塊。
  - **解決方式**：`Ctrl+C` 結束命令。

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help -online 不會開啟說明頁面
 - **詳細資料**：如果使用者鍵入 `Get-Help Find-Module -online`，則會看到一則錯誤訊息，例如：`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
 - **解決方式**：複製 URL，並手動在瀏覽器上予以開啟。
 
### <a name="troubleshooting-remote-management-of-azure-vms"></a>針對 Azure VM 遠端管理進行疑難排解
 - **詳細資料**：基於 WinRM 的預設 Windows 防火牆設定，使用者可能會看到下列錯誤：`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
 - **解決方式**：確定 VM 正在執行。 您可以執行 `Get-AzureRmVM -Status` 來找出 VM 狀態。  接下來，在遠端 VM 上新增防火牆規則，以允許任何子網路中的 WinRM 連線 (舉例來說)。

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 您可以使用 [Azure 自訂指令碼延伸模組][customex]，避免登入遠端 VM 來新增防火牆規則。
 您可以將上述指令碼儲存至檔案 (即 `addfirerule.ps1`)，並將它上傳至 Azure 儲存體容器。
 然後，嘗試下列命令：

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```
