---
title: "使用 PowerShell 建立具有區域備援公用 IP 位址前端的公用 Load Balancer Standard | Microsoft Docs"
description: "了解如何使用 PowerShell 建立具有區域備援公用 IP 位址前端的公用 Load Balancer Standard"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2945e04814f3d1e5b281391485def47f4b463d88
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>使用 PowerShell 建立具有區域備援公用 IP 位址前端的公用 Load Balancer Standard

本文會逐步說明如何使用 Public IP Standard 位址來建立具有區域備援前端的公用 [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>註冊可用性區域、Load Balancer Standard 和 Public IP Standard 預覽版

本文會要求您安裝 AzureRM 模組 4.4.0 版或更新版本。 若要尋找版本，請執行 `Get-Module -ListAvailable AzureRM`。 如果您需要安裝或升級，請從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureRM)安裝最新版的 AzureRM 模組。

>[!NOTE]
[Load Balancer Standard SKU](https://aka.ms/azureloadbalancerstandard) 目前為預覽狀態。 在預覽階段，功能可能沒有與正式發行版本功能相同層級的可用性和可靠性。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 為您的生產服務使用正式推出的 [Load Balancer Basic SKU](load-balancer-overview.md)。 

> [!NOTE]
> 可用性區域為預覽版功能，可供開發和測試案例使用。 此功能支援選取 Azure 資源和區域以及 VM 大小系列。 如需如何開始使用，以及有哪些 Azure 資源、區域和 VM 大小系列可供用來試用可用性區域，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。 如需支援，您可以透過 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 與我們聯繫或[開啟 Azure 支援票證](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。  

在選取 Load Balancer 前端公用 IP 位址的區域或區域備援選項之前，您必須先完成[註冊可用性區域預覽版](https://docs.microsoft.com/azure/availability-zones/az-overview)中的步驟。

## <a name="log-in-to-azure"></a>登入 Azure

使用 `Login-AzureRmAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>建立資源群組

使用下列命令建立資源群組：

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>建立 Public IP Standard 
使用下列命令建立 Public IP Standard：

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>為網站建立前端 IP 組態

使用下列命令來建立前端 IP 組態：

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>建立後端位址集區

使用下列命令來建立後端位址集區：

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>在連接埠 80 上建立負載平衡器探查

使用下列命令在連接埠 80 上建立負載平衡器地健康情況探查：

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer"></a>建立負載平衡器
使用下列命令建立 Load Balancer Standard：

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>後續步驟
- 了解如何[在可用性區域中建立公用 IP](../virtual-network/create-public-ip-availability-zone-portal.md)




