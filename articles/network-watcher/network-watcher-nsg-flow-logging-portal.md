---
title: "使用 Azure 網路監看員管理網路安全性群組流量記錄 | Microsoft Docs"
description: "此頁面說明如何在 Azure 網路監看員中管理網路安全性群組流量記錄"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 41cb5ffab9bd3a3bed75ffdb6a7383ca1690f810
ms.contentlocale: zh-tw
ms.lasthandoff: 06/02/2017

---

# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>在 Azure 入口網站中管理網路安全性群組流量記錄

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

網路安全性群組流量記錄是網路監看員的一項功能，可讓您檢視透過網路安全性群組傳輸的輸入和輸出 IP 流量相關資訊。 這些流量記錄是以 JSON 格式寫入，並提供重要資訊，包括： 

- 每個規則的輸出和輸入流量。
- 套用流量的 NIC。
- 與流量相關的 5-Tuple 資訊 (來源/目的地 IP、來源/目的地連接埠，通訊協定)。
- 流量被允許或拒絕的資訊。

## <a name="before-you-begin"></a>開始之前

此案例假設您已依照[建立網路監看員執行個體](network-watcher-create.md)中的步驟執行。 此案例也假設您已擁有具備有效虛擬機器的資源群組。

## <a name="register-insights-provider"></a>註冊 Insights 提供者

為了讓流量記錄成功運作，您必須註冊 **Microsoft.Insights** 提供者。 若要註冊提供者，請執行下列步驟： 

1. 移至 [訂用帳戶]，然後選取您要為其啟用流量記錄的訂用帳戶。 
2. 在 [訂用帳戶] 刀鋒視窗中，選取 [資源提供者]。 
3. 查看提供者清單，並確認 **microsoft.insights** 提供者已註冊。 如果沒有，則選取 [註冊]。

![檢視提供者][providers]

## <a name="enable-flow-logs"></a>啟用流量記錄

這些步驟會引導您完成在網路安全性群組上啟用流量記錄的程序。

### <a name="step-1"></a>步驟 1

移至網路監看員執行個體，然後選取 [NSG 流量記錄]。

![流量記錄概觀][1]

### <a name="step-2"></a>步驟 2

從清單中選取網路安全性群組。

![流量記錄概觀][2]

### <a name="step-3"></a>步驟 3 

在 [流量記錄設定] 刀鋒視窗上，將狀態設為 [開啟] 並設定儲存體帳戶。  完成後，選取 [確定]。 然後選取 [儲存]。

![流量記錄概觀][3]

## <a name="download-flow-logs"></a>下載流量記錄

流量記錄會儲存在儲存體帳戶中。 下載流量記錄以檢視它們。

### <a name="step-1"></a>步驟 1

若要下載流量記錄，請選取 [您可從設定的儲存體帳戶下載流量記錄檔]。 此步驟會帶您前往儲存體帳戶檢視，以便在該處選擇要下載的記錄。

![流量記錄設定][4]

### <a name="step-2"></a>步驟 2

移至正確的儲存體帳戶。 然後選取 [容器] > [insights-log-networksecuritygroupflowevent]。

![流量記錄設定][5]

### <a name="step-3"></a>步驟 3

移至流量記錄的位置並選取它，然後選取 [下載]。

![流量記錄設定][6]

如需記錄結構的相關資訊，請造訪[網路安全性群組流量記錄概觀](network-watcher-nsg-flow-logging-overview.md)。

## <a name="next-steps"></a>後續步驟

了解如何[使用 PowerBI 視覺化 NSG 流量記錄](network-watcher-visualize-nsg-flow-logs-power-bi.md)。

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png

