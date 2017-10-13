---
title: "使用 Azure CLI 建立分區公用 IP 位址 | Microsoft Docs"
description: "使用 Azure CLI 在可用性區域中建立公用 IP。"
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
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>使用 Azure CLI 在可用性區域中建立公用 IP 位址

您可以在 Azure 可用性區域 (預覽) 中部署公用 IP 位址。 可用性區域是 Azure 區域內實際分開的區域。 了解如何：

> * 在可用性區域中建立公用 IP 位址
> * 識別可用性區域中所建立的相關資源

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行大於 2.0.17 版的 Azure CLI。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

> [!NOTE]
> 可用性區域為預覽版功能，可供開發和測試案例使用。 此功能支援選取 Azure 資源和區域以及 VM 大小系列。 如需如何開始使用，以及有哪些 Azure 資源、區域和 VM 大小系列可供用來試用可用性區域，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。 如需支援，您可以透過 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 與我們聯繫或[開啟 Azure 支援票證](../azure-supportability/how-to-create-azure-support-request.md)。

## <a name="create-a-zonal-public-ip-address"></a>建立區域性公用 IP 位址

使用下列命令在可用性區域中建立公用 IP 位址：


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> 當您將標準 SKU 的公用 IP 位址指派給虛擬機器的網路介面時，必須使用[網路安全性群組](security-overview.md#network-security-groups)明確地允許預定的流量。 在建立和關聯網路安全性群組並明確地允許所要流量前，與資源進行的通訊都會失敗。

## <a name="get-zone-information-about-a-public-ip-address"></a>取得關於公用 IP 位址的區域資訊

使用下列命令取得公用 IP 位址的區域資訊：

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>後續步驟

- 深入了解[可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)
- 深入了解[公用 IP 位址](../virtual-network/virtual-network-public-ip-address.md) 
