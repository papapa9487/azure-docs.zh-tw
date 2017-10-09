---
title: "在 Azure Cloud Shell (預覽) 中保存檔案 | Microsoft Docs"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 933af3860bfe087a0b4db7eff53d4b978a1475da
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-works"></a>Cloud Shell 的運作方式
Cloud Shell 透過下列方法來保存檔案： 
* 在您的 `$Home` 目錄中，將指定的檔案共用掛接為 `clouddrive`，以便直接與檔案共用互動。

## <a name="list-cloud-drive-file-shares"></a>列出雲端磁碟機檔案共用
`Get-CloudDrive` 命令會取出 Cloud Shell 中雲端磁碟機目前所掛接的檔案共用資訊。 <br>
![執行 Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>卸載雲端磁碟機
您可以隨時將掛接至 Cloud Shell 的檔案共用卸載。 如果已移除檔案共用，則系統會在下一個工作階段提示您建立和掛接新的檔案共用。

`Dismount-CloudDrive` 命令會從目前儲存體帳戶卸載檔案共用。 卸載雲端磁碟機會終止目前工作階段。 系統會提示使用者在下一個工作階段期間建立和掛接新的檔案共用。
![執行 Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>後續步驟
[PowerShell 的快速入門](quickstart-powershell.md) <br>
[了解 Azure 檔案儲存體](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[了解儲存體標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
