---
title: "在 Azure Cloud Shell 的 PowerShell 中保存檔案 (預覽) | Microsoft Docs"
description: "逐步解說 Azure Cloud Shell 如何保存檔案。"
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: damaerte
ms.openlocfilehash: d0bc16bc951fce17235d8070012de44ebab89888
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Azure Cloud Shell 中的 PowerShell (預覽) 如何運作
Azure Cloud Shell 中的 PowerShell (預覽) 透過下列方法保存檔案： 
* 在您的 `$Home` 目錄中，將指定的 Azure 檔案共用掛接為 `clouddrive`，以便直接與檔案共用互動。

## <a name="list-cloud-drive-azure-file-shares"></a>列出雲端磁碟機 Azure 檔案共用
`Get-CloudDrive` 命令會取出 Cloud Shell 中雲端磁碟機目前所掛接的 Azure 檔案共用資訊。 <br>
![Running Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>卸載雲端磁碟機
您可以隨時將掛接至 Cloud Shell 的 Azure 檔案共用卸載。 如果已移除 Azure 檔案共用，則系統會在下一個工作階段提示您建立和掛接新的 Azure 檔案共用。

`Dismount-CloudDrive` 命令會從目前儲存體帳戶卸載 Azure 檔案共用。 卸載雲端磁碟機會終止目前工作階段。 系統會提示使用者在下一個工作階段期間建立和掛接新的 Azure 檔案共用。
![執行 Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>後續步驟
[PowerShell 的快速入門](quickstart-powershell.md) <br>
[了解 Azure 檔案](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[了解儲存體標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>