---
title: "檢視 Azure Stack 中的公用 IP 位址使用 |Microsoft Docs"
description: "系統管理員可以檢視區域中的公用 IP 位址使用"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 7651565eebf6272f307a4ce4790ca19b41bfa826
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>檢視 Azure Stack 中的公用 IP 位址使用

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

身為雲端系統管理員，您可以檢視配置給租用戶的公用 IP 位址數目、仍可供配置的公用 IP 位址數目，以及已在該位置配置的公用 IP 位址百分比。

[公用 IP 集區使用方式] 磚會顯示網狀架構上所有公用 IP 位址集區中已使用的公用 IP 位址總數，無論其是否已用於租用戶 IaaS VM 執行個體、網狀架構基礎結構服務，或由租用戶所明確建立的公用 IP 位址資源。

此磚的目的是要讓 Azure Stack 系統管理員檢視在此位置中已使用的公用 IP 位址總數。 這可協助系統管理員判斷其是否即將耗盡此資源。

在 [資源提供者] 的 [網路] 刀鋒視窗上，[租用戶資源] 下的 [公用 IP 位址] 功能表項目僅會列出租用戶所明確建立的公用 IP 位址。 例如，[公用 IP 集區使用方式] 磚上的 [已使用] 公用 IP 位址數目，永遠不同於 (大於) 在 [租用戶資源] 下 [公用 IP 位址] 磚上的數目。

## <a name="view-the-public-ip-address-usage-information"></a>檢視公用 IP 位址使用方式資訊
若要檢視區域中已使用的公用 IP 位址總數：

1. 在 Azure Stack 系統管理員入口網站中，按一下 [更多服務]，然後在 [管理資源] 下方按一下 [資源提供者]。
2. 從 [資源提供者] 清單中，選取 [網路]。
3. [網路] 刀鋒視窗會在 [概觀] 區段中顯示 [公用 IP 集區使用方式]磚。

![網路資源提供者刀鋒視窗](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

請注意，[已使用] 數字代表已指派的該位置中來自所有公用 IP 位址集區的公用 IP 位址數目。 [可用] 數字代表來自所有尚未指派，且仍可供使用之公用 IP 位址集區的公用 IP 數目。 [% 已使用] 數字代表該位置內所有公用 IP 位址集區中已使用或已指派公用 IP 位址總數的百分比數目。

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>檢視租用戶訂用帳戶所建立的公用 IP 位址
若要查看特定區域中租用戶訂用帳戶所明確建立的公用 IP 位址清單，請按一下 [租用戶資源] 下的 [公用 IP 位址]。

![租用戶公用 IP 位址](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

您可能會注意到某些已動態配置的公用 IP 位址會顯示在清單中，但尚未具有與其相關聯的位址。 這是因為位址資源已在網路資源提供者中建立，但尚未在網路控制卡中建立。

網路控制卡在位址實際繫結至介面、網路介面卡 (NIC)、負載平衡器或虛擬網路閘道之前，不會將位址指派給此資源。 當公用 IP 位址繫結至介面時，網路控制卡會將 IP 位址配置給它，使其顯示在 [位址] 欄位中。

## <a name="view-the-public-ip-address-information-summary-table"></a>檢視公用 IP 位址資訊摘要資料表
指派公用 IP 位址時具有數種不同案例，可判斷位址是否顯示在一個 (或其他) 清單中。

| **公用 IP 位址指派案例** | **顯示於使用方式摘要** | **顯示於租用戶公用 IP 位址清單** |
| --- | --- | --- |
| 尚未指派給 NIC 或負載平衡器 (暫時) 的動態公用 IP 位址 |否 |是 |
| 已指派給 NIC 或負載平衡器的動態公用 IP 位址。 |是 |是 |
| 已指派給租用戶 NIC 或負載平衡器的靜態公用 IP 位址。 |是 |是 |
| 已指派給網狀架構基礎結構服務端點的靜態公用 IP 位址。 |是 |否 |
| 公用 IP 位址會以隱含方式針對 IaaS VM 執行個體而建立，並用於虛擬網路上的輸出 NAT。 每當租用戶建立 VM 執行個體，讓 VM 可以將資訊送出到網際網路時，這些位址便會在幕後建立。 |是 |否 |

## <a name="next-steps"></a>後續步驟
[在 Azure Stack 中管理儲存體帳戶](azure-stack-manage-storage-accounts.md)