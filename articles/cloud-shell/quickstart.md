---
title: "Azure Cloud Shell (預覽) 中的 Bash 快速入門 | Microsoft Docs"
description: "Cloud Shell 中 Bash 的快速入門"
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
ms.openlocfilehash: bbad2735acf06ebefda020410731fb307c4ec614
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Azure Cloud Shell 中 Bash 的快速入門

本文件會詳細說明如何在 [Azure 入口網站](https://ms.portal.azure.com/)中使用 Azure Cloud Shell 中的 Bash。

> [!NOTE]
> 也提供 [Azure Cloud Shell 中的 PowerShell](quickstart-powershell.md) 快速入門。

## <a name="start-cloud-shell"></a>啟動 Cloud Shell
1. 從 Azure 入口網站的頂端導覽啟動 **Cloud Shell** <br>
![](media/quickstart/shell-icon.png)
2. 選取用來建立儲存體帳戶和 Azure 檔案共用的訂用帳戶
3. 選取 [建立儲存體]

> [!TIP]
> 在每個工作階段會自動驗證您以使用 Azure CLI 2.0。

### <a name="select-the-bash-environment"></a>選取 Bash 環境
1. 從殼層視窗左邊選取環境下拉式清單 <br>
![](media/quickstart/env-selector.png)
2. 選取 Bash

### <a name="set-your-subscription"></a>設定您的訂用帳戶
1. 列出您可存取的訂用帳戶： <br>
`az account list`
2. 設定您慣用的訂用帳戶： <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> 系統將使用 `/home/<user>/.azure/azureProfile.json` 來記住您的訂用帳戶，以供未來工作階段使用。

### <a name="create-a-resource-group"></a>建立資源群組
在 WestUS 中建立名為 "MyRG" 的新資源群組： <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>建立 Linux VM
在您的新資源群組中建立 Ubuntu VM。 Azure CLI 2.0 將會建立 SSH 金鑰，並使用它們來設定 VM。 <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> Azure CLI 2.0 預設會將用來驗證 VM 的公開金鑰和私密金鑰置於 `/User/.ssh/id_rsa` 和 `/User/.ssh/id_rsa.pub`。 您的 .ssh 資料夾會保存在所連接 Azure 檔案共用的 5-GB 映像中。

您在此 VM 上的使用者名稱，將會是用於 Cloud Shell 中的使用者名稱 ($User@Azure:)。

### <a name="ssh-into-your-linux-vm"></a>透過 SSH 連線到您的 Linux VM
1. 在 Azure 入口網站搜尋列中搜尋您的 VM 名稱
2. 按一下 [連線] 然後執行：`ssh username@ipaddress`

![](media/quickstart/sshcmd-copy.png)

建立 SSH 連線時，應該會看到 Ubuntu 歡迎提示。 <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>清除 
刪除您的資源群組以及其中的任何資源： <br>
執行 `az group delete -n MyRG`

## <a name="next-steps"></a>後續步驟
[了解保存 Cloud Shell 中 Bash 的檔案](persisting-shell-storage.md) <br>
[了解 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) \(英文\) <br>
[了解 Azure 檔案儲存體](../storage/files/storage-files-introduction.md) <br>