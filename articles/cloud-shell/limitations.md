---
title: "Azure Cloud Shell (預覽) 限制 | Microsoft Docs"
description: "Azure Cloud Shell 限制的概觀"
services: azure
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
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: fe325cf5fa5e3d4b0a188599de7308cf2008b458
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell 限制
Azure Cloud Shell 具有下列已知限制：

## <a name="general-limitations"></a>一般限制

### <a name="system-state-and-persistence"></a>系統狀態和持續性
提供 Cloud Shell 工作階段的電腦只是暫時性，在工作階段閒置 20 分鐘後就會回收。 Cloud Shell 需要掛接檔案共用。 因此，您的訂用帳戶必須能夠設定儲存體資源，才可存取 Cloud Shell。 其他考量包括：
* 在掛接的儲存體中，只會保存 `clouddrive` 目錄內的修改。 在 Bash 中，也會保存 `$Home` 目錄。
* 只能從您的[已指派區域](persisting-shell-storage.md#mount-a-new-clouddrive)內掛接檔案共用。
  * 在 Bash 中，會執行 `env` 來尋找設定為 `ACC_LOCATION` 的區域。
* Azure 檔案只支援本機備援儲存體和異地備援儲存體帳戶。

### <a name="browser-support"></a>瀏覽器支援
Cloud Shell 支援最新版的 Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox 及 Apple Safari。 不支援 Safari 私密瀏覽模式。

### <a name="copy-and-paste"></a>複製和貼上

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>針對指定的使用者，只能有一個殼層作用中
使用者一次只能啟動一種類型的 Shell：**Bash** 或 **PowerShell**。 不過，您可能一次會執行多個 Bash 或 PowerShell 執行個體。 在 Bash 或 PowerShell 之間交換會讓 Cloud Shell 重新啟動，這樣會終止現有工作階段。

### <a name="usage-limits"></a>使用限制
Cloud Shell 主要用於互動式的使用案例。 因此，任何長時間執行而沒有互動的工作階段會在不發出警告的情況下結束。

## <a name="bash-limitations"></a>Bash 限制

### <a name="user-permissions"></a>使用者權限
權限設定為沒有 sudo 存取權的一般使用者。 將不會保存 `$Home` 目錄之外的任何安裝。
雖然 `git clone` 目錄內的某些命令 (例如 `clouddrive`) 沒有適當的權限，但您的 `$Home` 目錄有適當的權限。

### <a name="editing-bashrc"></a>編輯 .bashrc
編輯 .bashrc 時請小心，這麼做可能會導致 Cloud Shell 發生未預期的錯誤。 

### <a name="bashhistory"></a>.bash_history
Bash 命令的歷程記錄可能因為 Cloud Shell 工作階段中斷或並行工作階段而出現不一致的情況。

## <a name="powershell-limitations"></a>PowerShell 限制

### <a name="slow-startup-time"></a>緩慢的啟動時間
在預覽期間，Azure Cloud Shell 中的 PowerShell 最多需要 60 秒才能初始化。

### <a name="no-home-directory-persistence"></a>沒有 $Home 目錄持續性
在 PowerShell 工作階段之間不會保存任何應用程式 (例如：git、vim 和其他項目) 寫入至 $Home 的資料。  如需因應措施，[請參閱這裡](troubleshooting.md#powershell-resolutions)。

### <a name="error-if-azurerm-module-is-updated-from-powershellgallery"></a>如果是從 PowerShellGallery 更新 AzureRM 模組，就會發生錯誤
如果您將 AzureRM 模組更新為最新版本 (4.4.0)，可能會在 Cloudshell 啟動上看到下列錯誤。
``` powershell
VERBOSE: Authenticating to Azure ...
Import-Module : Method 'RemoveUser' in type 'Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory' from assembly 'Microsoft.Azure.PSCloudConsole.ADAuth, Version=0.0.0.0,
Culture=neutral, PublicKeyToken=null' does not have an implementation.
At C:\Program Files\WindowsPowerShell\Modules\PSCloudShellADAuth\PSCloudShellADAuth.psm1:12 char:1
+ Import-Module -Name $AdAuthModulePath -PassThru -Force
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Import-Module], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.PowerShell.Commands.ImportModuleCommand

Unable to find type [Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory].
At C:\Users\ContainerAdministrator\PSCloudShellStartup.ps1:94 char:9
+         [Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory]::Update ...
+         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (Microsoft.Azure...h.ADAuthFactory:TypeName) [], RuntimeException
    + FullyQualifiedErrorId : TypeNotFound
```

## <a name="next-steps"></a>後續步驟
[針對 Cloud Shell 進行疑難排解](troubleshooting.md) <br>
[Bash 的快速入門](quickstart.md) <br>
[PowerShell 的快速入門](quickstart-powershell.md)
