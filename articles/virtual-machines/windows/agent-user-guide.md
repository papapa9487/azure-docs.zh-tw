---
title: "Azure 虛擬機器代理程式概觀 | Microsoft Docs"
description: "Azure 虛擬機器代理程式概觀"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: nepeters
ms.openlocfilehash: 24ad2c2d2872f844e32d3fae559683c3d992bd00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-machine-agent-overview"></a>Azure 虛擬機器代理程式概觀

Microsoft Azure 虛擬機器代理程式 (AM 代理程式) 是安全的輕量型處理序，可管理 VM 與 Azure 網狀架構控制器的互動。 VM 代理程式已啟用主要角色並執行 Azure 虛擬機器擴充功能。 VM 擴充功能可啟用虛擬機器的部署後組態，例如安裝和設定軟體。 虛擬機器擴充功能也會啟用復原功能，例如重設虛擬機器的系統管理密碼。 若沒有 Azure VM 代理程式，便無法執行虛擬機器擴充功能。

本文件詳述 Azure 虛擬機器代理程式的安裝、偵測及移除。

## <a name="install-the-vm-agent"></a>安裝 VM 代理程式

### <a name="azure-gallery-image"></a>Azure 資源庫映像

根據預設，Azure VM 代理程式會安裝於任何從 Azure 資源庫映像部署的 Windows 虛擬機器上。 經由入口網站、PowerShell、命令列介面或 Azure Resource Manager 範本部署 Azure 資源庫映像時，也會安裝 Azure VM 代理程式。 

### <a name="manual-installation"></a>手動安裝

Windows VM 代理程式可以使用 Windows Installer 套件來手動安裝。 建立會部署在 Azure 中的自訂虛擬機器映像時，可能需要手動安裝。 若要手動安裝 Windows VM 代理程式，請從這個位置下載 VM 代理程式安裝程式：[Windows Azure VM 代理程式下載](http://go.microsoft.com/fwlink/?LinkID=394789)。 

按兩下 Windows Installer 檔案即可安裝 VM 代理程式。 如需自動安裝 VM 代理程式，請執行下列命令。

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>偵測 VM 代理程式

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell 模組可以用來擷取 Azure 虛擬機器的相關資訊。 執行 `Get-AzureRmVM` 可傳回相當多的資訊，包括 Azure VM 代理程式的佈建狀態。

```PowerShell
Get-AzureRmVM
```

以下只是 `Get-AzureRmVM` 輸出的子集。 請注意，`ProvisionVMAgent` 屬性會以巢狀方式置於 `OSProfile` 內，這個屬性可用來判斷 VM 代理程式是否已部署至虛擬機器。

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

下列指令碼可以用來傳回簡明的虛擬機器名稱清單和 VM 代理程式的狀態。

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>手動偵測

記錄到 Windows Azure VM 時，工作管理員可用來檢查執行中的程序。 若要檢查 Azure VM 代理程式，請開啟 [工作管理員] > 按一下 [詳細資料] 索引標籤，然後尋找程序名稱 `WindowsAzureGuestAgent.exe`。 此程序的目前狀態表示已安裝 VM 代理程式。

## <a name="upgrade-the-vm-agent"></a>升級 VM 代理程式

適用於 Windows 的 Azure VM 代理程式會自動升級。 當新的虛擬機器部署至 Azure 時，這些機器會收到最新的 VM 代理程式。 自訂 VM 映像應進行手動更新，以包含新的 VM 代理程式。