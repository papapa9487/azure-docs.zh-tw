---
title: "使用 PowerShell 建立分區公用 IP 位址 | Microsoft Docs"
description: "使用 PowerShell 在可用性區域中建立公用 IP。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: bfeba57036338b4e325d2f122443f2cde0373eed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>使用 PowerShell 在可用性區域中建立公用 IP 位址

您可以在 Azure 可用性區域 (預覽) 中部署公用 IP 位址。 可用性區域是 Azure 區域內實際分開的區域。 了解如何：

> * 在可用性區域中建立公用 IP 位址
> * 識別可用性區域中所建立的相關資源
  

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

本文會要求您安裝 AzureRM 模組 4.4.0 版或更新版本。 若要尋找版本，請執行 `Get-Module -ListAvailable AzureRM`。 如果您需要安裝或升級，請從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureRM)安裝最新版的 AzureRM 模組。

> [!NOTE]
> 可用性區域為預覽版功能，可供開發和測試案例使用。 此功能支援選取 Azure 資源和區域以及 VM 大小系列。 如需如何開始使用，以及有哪些 Azure 資源、區域和 VM 大小系列可供用來試用可用性區域，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。 如需支援，您可以透過 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 與我們聯繫或[開啟 Azure 支援票證](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="log-in-to-azure"></a>登入 Azure

使用 `Login-AzureRmAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 在此範例中，會在 westeurope 區域中建立名為 myResourceGroup 的資源群組。 「westeurope」是其中一個在預覽中支援 Azure 可用性區域的區域。

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>建立區域性公用 IP 位址

使用下列命令在可用性區域中建立公用 IP 位址：

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> 當您將標準 SKU 的公用 IP 位址指派給虛擬機器的網路介面時，必須使用[網路安全性群組](security-overview.md#network-security-groups)明確地允許預定的流量。 在建立和關聯網路安全性群組並明確地允許所要流量前，與資源進行的通訊都會失敗。

## <a name="get-zone-information-about-a-public-ip-address"></a>取得關於公用 IP 位址的區域資訊

使用下列命令取得公用 IP 位址的區域資訊：

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>後續步驟

- 深入了解[可用性區域](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)
- 深入了解[公用 IP 位址](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 