---
title: "Azure Cloud Shell (預覽) 概觀 | Microsoft Docs"
description: "Azure Cloud Shell 的概觀。"
services: 
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
ms.openlocfilehash: 44d2ac2fd35621ab8cd8d7584744139ee3bab5c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-cloud-shell-preview"></a>Azure Cloud Shell 的功能概觀 (預覽)
Azure Cloud Shell 是可經由瀏覽器存取的互動式殼層，應用在 Azure 資源管理上。
它可讓您彈性地選擇最適合您工作方式的殼層體驗。
Linux 使用者可以選擇 Bash 體驗，而 Windows 使用者可以選擇 PowerShell。

![Cloud Shell 中的 Bash](media/overview/overview-bash-pic.png)

![Cloud Shell 中的 PowerShell](media/overview/overview-ps-pic.png)

## <a name="features"></a>特性
### <a name="browser-based-shell-experience"></a>以瀏覽器為基礎的體驗
Cloud Shell 以 Azure 管理工作為考量，提供以瀏覽器存取命令列的體驗。
使用 Cloud Shell 可以只有雲端才能提供的方式離開本機電腦工作。

### <a name="choice-of-preferred-shell-experience"></a>選擇慣用的殼層體驗
Azure Cloud Shell 可讓您彈性地選擇最適合您的工作方式的殼層體驗。
Linux 使用者可以選擇 Bash 體驗，而 Windows 使用者可以選擇 PowerShell。

### <a name="pre-configured-azure-workstation"></a>預先設定的 Azure 工作站
Cloud Shell 預先安裝受歡迎的命令列工具和語言支援，能加快您的工作速度。

檢視 [Bash 經驗](features.md#tools)和 [PowerShell 經驗](features-powershell.md#tools)的完整工具清單。

### <a name="automatic-authentication"></a>自動驗證
Cloud Shell 可在每個工作階段安全地自動驗證，讓您可透過 Azure CLI 2.0 立即存取您的資源。

### <a name="connect-your-azure-file-storage"></a>連接您的 Azure 檔案儲存體
Cloud Shell 機器是暫存的，因此需要將 Azure 檔案共用掛接為 `clouddrive` 才能保存 $Home 目錄。
第一次啟動時，Cloud Shell 會提示要代替您建立資源群組、儲存體帳戶及檔案共用。 這是一次性的步驟，而且會針對所有工作階段自動連接。 單一檔案共用可以進行對應，並同時供 Cloud Shell 中的 Bash 和 PowerShell 使用。

#### <a name="create-new-storage"></a>建立新的儲存體
![](media/overview/basic-storage.png)

可代替您建立本地備援儲存體 (LRS) 帳戶和 Azure 檔案共用。 如果您選擇同時使用 Bash 和 PowerShell 環境，則 Azure 檔案共用將用於這兩個環境。 所需成本和一般儲存體相同。

將代替您建立下列三個資源：
1. 名稱如下的資源群組：`cloud-shell-storage-<region>`
2. 名稱如下的儲存體帳戶：`cs<uniqueGuid>`
3. 名稱如下的檔案共用：`cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Cloud Shell 中的 Bash 也會建立預設 5-GB 磁碟映像來保存 `$Home`。 $Home 目錄中的所有檔案 (例如 SSH 金鑰) 會都保存於已掛接檔案共用中儲存的使用者磁碟映像中。 在 $Home 目錄和已掛接的檔案共用中儲存檔案時，請套用最佳作法。

#### <a name="use-existing-resources"></a>使用現有的資源
![](media/overview/advanced-storage.png)

提供進階選項，將現有資源與 Cloud Shell 建立關聯。
在儲存體設定提示中，按一下 [顯示進階設定] 以顯示其他選項。
下拉式清單會針對您指派的 Cloud Shell 區域和本地/全域備援儲存體帳戶進行篩選。

[了解 Cloud Shell 儲存體、更新檔案共用，以及上傳/下載檔案。](persisting-shell-storage.md)

## <a name="concepts"></a>概念
* Cloud Shell 會在以每一工作階段、每位使用者為基礎所提供的暫存機器上執行
* Cloud Shell 會在無互動活動的 20 分鐘後逾時
* 只有在已連接檔案共用時才能存取 Cloud Shell
* Cloud Shell 會將相同的檔案共用同時用於 Bash 和 PowerShell
* Cloud Shell 會以一台機器、一個使用者帳戶的方式指派
* 權限設定為一般 Linux 使用者 (Bash)

深入了解 [Cloud Shell 中的 Bash](features.md) 和 [Cloud Shell 中的 PowerShell](features-powershell.md) 中的功能。

## <a name="examples"></a>範例
* 使用指令碼來自動化 Azure 管理作業
* 同時透過 Azure 入口網站和 Azure 命令列工具管理 Azure 資源
* 測試磁碟機 Azure CLI 2.0 或 Azure PowerShell Cmdlet

試用 [Cloud Shell 中的 Bash](quickstart.md) 和 [Cloud Shell 中的 PowerShell](quickstart-powershell.md) 的快速入門中的這些範例。

## <a name="pricing"></a>價格
裝載 Cloud Shell 的機器是免費提供的，但前提是必須掛接 Azure 檔案共用。 所需成本和一般儲存體相同。

## <a name="supported-browsers"></a>支援的瀏覽器
Cloud Shell 建議用於 Chrome、Edge 和 Safari 瀏覽器。
雖然 Chrome、Firefox、Safari、IE 和 Edge 都支援 Cloud Shell，Cloud Shell 仍受限於特定瀏覽器設定。