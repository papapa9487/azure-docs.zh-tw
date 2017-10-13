---
title: "使用 Azure CLI 建立具有區域備援公用 IP 位址前端的公用 Load Balancer Standard | Microsoft Docs"
description: "了解如何使用 Azure CLI 建立具有區域備援公用 IP 位址前端的公用 Load Balancer Standard"
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
ms.openlocfilehash: 769eb86af3e0506ddf03d1ec616d5a17b7e5f714
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>使用 Azure CLI 建立具有區域備援前端的公用 Load Balancer Standard

本文會逐步說明如何使用 Public IP Standard 位址來建立具有區域備援前端的公用 [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>註冊可用性區域、Load Balancer Standard 和 Public IP Standard 預覽版

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.17 版或更新版本。  若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

>[!NOTE]
[Load Balancer Standard SKU](https://aka.ms/azureloadbalancerstandard) 目前為預覽狀態。 在預覽階段，功能可能沒有與正式發行版本功能相同層級的可用性和可靠性。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 為您的生產服務使用正式推出的 [Load Balancer Basic SKU](load-balancer-overview.md)。 

> [!NOTE]
> 可用性區域為預覽版功能，可供開發和測試案例使用。 此功能支援選取 Azure 資源和區域以及 VM 大小系列。 如需如何開始使用，以及有哪些 Azure 資源、區域和 VM 大小系列可供用來試用可用性區域，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。 如需支援，您可以透過 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 與我們聯繫或[開啟 Azure 支援票證](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。  

在選取 Load Balancer 前端公用 IP 位址的區域或區域備援選項之前，您必須先完成[註冊可用性區域預覽版](https://docs.microsoft.com/azure/availability-zones/az-overview)中的步驟。

請確定您已安裝最新的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 並使用 [az login](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest#login) 登入 Azure 帳戶。

## <a name="create-a-resource-group"></a>建立資源群組

使用下列命令建立資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-public-ip-standard"></a>建立 Public IP Standard

使用下列命令建立 Public IP Standard：

```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>建立負載平衡器

使用下列命令，以您在上一個步驟中建立的標準公用 IP 來建立公用 Load Balancer Standard：

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>在連接埠 80 上建立 LB 探查

使用下列命令建立負載平衡器健康情況探查：

```azurecli-interactive
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>為連接埠 80 建立 LB 規則

使用下列命令建立負載平衡器規則：

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>後續步驟
- 了解如何[在可用性區域中建立公用 IP](../virtual-network/create-public-ip-availability-zone-cli.md)



