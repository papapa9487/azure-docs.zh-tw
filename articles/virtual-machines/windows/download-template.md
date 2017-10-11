---
title: "下載 Azure VM 的範本 | Microsoft Docs"
description: "下載 VM 的範本以協助在 Resource Manager 部署模型中進行自動化部署"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 9e4c0c3cf0e233447369a24b1d5fe27495abd1cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="download-the-template-for-a-vm"></a>下載 VM 的範本
當您使用入口網站或 PowerShell 在 Azure 中建立 VM 時，系統會自動為您建立 Resource Manager 範本。 您可以使用此範本快速地重複部署。 範本包含資源群組中所有資源的相關資訊。 針對虛擬機器，這表示範本包含針對支援該資源群組中 VM 而建立的所有項目，包括網路功能資源。

## <a name="download-the-template-using-the-portal"></a>使用入口網站下載範本
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表上，選取 [虛擬機器]。
3. 然後從清單中選取虛擬機器。
4. 選取 [自動化指令碼]。
5. 選取 [下載]，將 .zip 檔儲存到本機電腦。
6. 開啟 .zip 檔，並將檔案解壓縮至資料夾。 .Zip 檔會包含︰
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

template.json 檔案為範本。

## <a name="download-the-template-using-powershell"></a>使用 PowerShell 下載範本
您也可以使用 [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) cmdlet 下載 .json 範本檔案。 您可以使用 `-path` 參數來提供 .json 檔的檔名和路徑。 這個範例示範如何將名為 **myResourceGroup** 的資源群組範本下載至本機電腦上的 **C:\users\public\downloads** 資料夾。

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>後續步驟
若要了解有關使用範本部署資源的詳細資訊，請參閱 [Resource Manager 範本逐步解說](../../azure-resource-manager/resource-manager-template-walkthrough.md)。

