---
title: "使用 Azure 入口網站建立具有區域備援公用 IP 位址前端的公用 Load Balancer Standard | Microsoft Docs"
description: "了解如何使用 Azure 入口網站建立具有區域備援公用 IP 位址前端的公用 Load Balancer Standard"
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
ms.openlocfilehash: e28983f6015f8dcaa546c20fd9bc8d986a15ab5d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>使用 Azure 入口網站建立具有區域備援公用 IP 位址前端的公用 Load Balancer Standard

本文會逐步說明如何使用 Public IP Standard 位址來建立具有區域備援前端的公用 [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>註冊可用性區域、Load Balancer Standard 和 Public IP Standard 預覽版
 
>[!NOTE]
[Load Balancer Standard SKU](https://aka.ms/azureloadbalancerstandard) 目前為預覽狀態。 在預覽階段，功能可能沒有與正式發行版本功能相同層級的可用性和可靠性。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 為您的生產服務使用正式推出的 [Load Balancer Basic SKU](load-balancer-overview.md)。 

> [!NOTE]
> 可用性區域為預覽版功能，可供開發和測試案例使用。 此功能支援選取 Azure 資源和區域以及 VM 大小系列。 如需如何開始使用，以及有哪些 Azure 資源、區域和 VM 大小系列可供用來試用可用性區域，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。 如需支援，您可以透過 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 與我們聯繫或[開啟 Azure 支援票證](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。  

在選取 Load Balancer 前端公用 IP 位址的區域或區域備援選項之前，您必須先完成[註冊可用性區域預覽版](https://docs.microsoft.com/azure/availability-zones/az-overview)中的步驟。

## <a name="log-in-to-azure"></a>登入 Azure 

登入 Azure 入口網站，網址是 https://portal.azure.com。

## <a name="create-a-zone-redundant-load-balancer"></a>建立區域備援負載平衡器

1. 透過瀏覽器瀏覽至 Azure 入口網站 [http://portal.azure.com](http://portal.azure.com) ，並使用您的 Azure 帳戶登入。
2. 在畫面左側選取 [新增] > [網路] > [負載平衡器]。
3. 在 [建立負載平衡器] 的 [名稱] 底下輸入 **myPublicLB**。
4. 在 [類型] 底下選取 [公用]。
5. 在 SKU 底下選取 [標準 (預覽)]。
6. 按一下 [公用 IP 位址]，按一下 [建立新的]，在 [建立公用 IP 位址] 頁面上的 [名稱] 底下輸入 **myPublicIPStandard**，然後在 [可用性區域 (預覽)] 中選取 [區域備援]。
7. 在 [位置] 底下選取 [美國東部 2]，然後按一下 [確定]。 負載平衡器接著會開始部署，並且需要幾分鐘的時間才能順利完成部署。

    ![使用 Azure 入口網站建立區域備援 Load Balancer Standard](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>後續步驟
- 了解如何[在可用性區域中建立公用 IP](../virtual-network/create-public-ip-availability-zone-portal.md)




