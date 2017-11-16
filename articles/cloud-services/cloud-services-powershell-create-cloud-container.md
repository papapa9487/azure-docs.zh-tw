---
title: "使用 PowerShell 建立雲端服務容器 | Microsoft Docs"
description: "這篇文章說明如何使用 PowerShell 建立雲端服務容器。 容器會裝載 Web 和背景工作角色。"
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: 
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: cd703feb7bf5af765fc3a5448464499aa7b48d6a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>使用 Azure PowerShell 命令來建立空白的雲端服務容器
這篇文章說明如何使用 Azure PowerShell Cmdlet 快速建立雲端服務容器。 請依照下列步驟執行：

1. 從 [Azure PowerShell 下載](http://aka.ms/webpi-azps) 頁面安裝 Microsoft Azure PowerShell Cmdlet。
2. 開啟 PowerShell 命令提示字元。
3. 使用 [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) 登入。

   > [!NOTE]
   > 如需安裝 Azure PowerShell Cmdlet 及連接至您的 Azure 訂用帳戶的進一步指示，請參閱 [如何安裝及設定 Azure PowerShell](/powershell/azure/overview)。
   >
   >
4. 使用 **New-AzureService** Cmdlet 來建立空白的 Azure 雲端服務容器。

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. 遵循此範例以叫用 Cmdlet：

   ```
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

如需建立 Azure 雲端服務的詳細資訊，請執行：

```
Get-help New-AzureService
```

### <a name="next-steps"></a>後續步驟
* 若要管理雲端服務部署，請參閱 [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx)、[Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) 及 [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) 命令。 另請參閱 [如何設定雲端服務](cloud-services-how-to-configure-portal.md) 以取得進一步的資訊。
* 若要將雲端服務專案發佈至 Azure，請參閱[在 Azure 中持續傳遞雲端服務](cloud-services-dotnet-continuous-delivery.md)中的 **PublishCloudService.ps1** 程式碼範例。
