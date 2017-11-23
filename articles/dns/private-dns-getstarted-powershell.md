---
title: "利用 PowerShell 開始使用 Azure DNS 私人區域 | Microsoft Docs"
description: "了解如何在 Azure DNS 中建立私人 DNS 區域和記錄。 這份逐步指南將引導您使用 PowerShell 建立和管理第一個私人 DNS 區域和記錄。"
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d71e2391b6415b2403447479dea4fd0a3b818ed0
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2017
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>利用 PowerShell 開始使用 Azure DNS 私人區域

本文將逐步引導您使用 Azure PowerShell 建立第一個私人 DNS 區域和記錄。

DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始將網域裝載到 Azure DNS 中，您必須建立該網域名稱的 DNS 區域。 接著在此 DNS 區域內，建立網域的每筆 DNS 記錄。 若要將私人 DNS 區域發佈至虛擬網路，指定可以在區域內解析記錄的虛擬網路清單。  我們稱之為「解析網路」。  您也可以指定一組虛擬網路，每當 VM 建立、變更 IP 或被終結時，Azure DNS 將維護其主機名稱記錄。  我們稱之為「註冊網路」。

因為這項功能目前是受管理的預覽版，將會提供預覽版 PowerShell 模組。

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="create-the-resource-group"></a>建立資源群組

建立 DNS 區域之前，要建立包含 DNS 區域的資源群組。 以下顯示命令。

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>建立 DNS 區域

使用 `New-AzureRmDnsZone` Cmdlet 建立 DNS 區域。 下列範例會在 *MyResourceGroup* 資源群組中建立 *contoso.local* DNS 區域，並使 *MyAzureVnet* 虛擬網路能夠使用此 DNS 區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

如果您需要 Azure 自動在區域中建立主機名稱記錄，使用*RegistrationVirtualNetworkId* 參數來代替 *ResolutionVirtualNetworkId*。  註冊虛擬網路會自動啟用以供解析。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```


## <a name="create-a-dns-record"></a>建立 DNS 記錄

您可以使用 `New-AzureRmDnsRecordSet` Cmdlet 來建立記錄集。 下列範例會在資源群組 "MyResourceGroup" 中的 DNS 區域 "contoso.local" 中，建立具有相對名稱 "db" 的記錄。 記錄集的完整名稱是 "db.contoso.local"。 記錄類型為 'A'，IP 位址是 "10.0.0.4"，TTL 為 3600 秒。

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

關於其他記錄類型、具有多個記錄的記錄集和修改現有的記錄，請參閱[使用 Azure PowerShell 管理 DNS 記錄和記錄集](dns-operations-recordsets.md)。 


## <a name="view-records"></a>檢視記錄

若要列出區域中的 DNS 記錄，請使用︰

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

## <a name="delete-all-resources"></a>刪除所有資源

若要刪除這篇文章中建立的所有資源，請採取下列步驟︰

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

若要深入了解私人 DNS 區域，請參閱[使用 Azure DNS 私人網域](private-dns-overview.md)。

若要深入了解在 Azure DNS 中管理 DNS 記錄，請參閱[使用 PowerShell 在 Azure DNS 中管理 DNS 記錄和記錄集](dns-operations-recordsets.md)。

