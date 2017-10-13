---
title: "檢查與 Azure 網路監看員的連線 - Azure 入口網站 | Microsoft Docs"
description: "此頁面說明如何使用 Azure 入口網站檢查與網路監看員的連線"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 1f19da71731039e1a39c4440f925b1369886a993
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="check-connectivity-with-azure-network-watcher-using-the-azure-portal"></a>使用 Azure 入口網站檢查與 Azure 網路監看員的連線

> [!div class="op_single_selector"]
> - [入口網站](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

了解如何使用連線，確認是否可以建立從虛擬機器到指定端點的直接 TCP 連線。

## <a name="before-you-begin"></a>開始之前

本文假設您具有下列資源：

* 您想要檢查連線之區域中的網路監看員執行個體。

* 要檢查與其連線的虛擬機器。

> [!IMPORTANT]
> 連線檢查需要虛擬機器延伸模組 `AzureNetworkWatcherExtension`。 若要在 Windows VM 上安裝擴充功能，請瀏覽[適用於 Windows 的 Azure 網路監看員代理程式虛擬機器擴充功能](../virtual-machines/windows/extensions-nwa.md)，若要在 Linux VM 上安裝，則請瀏覽[適用於 Linux 的 Azure 網路監看員代理程式虛擬機器擴充功能](../virtual-machines/linux/extensions-nwa.md)。

## <a name="check-connectivity-to-a-virtual-machine"></a>檢查與虛擬機器的連線

這個範例會檢查透過連接埠 80 的目的地虛擬機器連線。

瀏覽至您的網路監看員，然後按一下 [連線能力檢查 (預覽)]。 選取要作為連線能力檢查起點的虛擬機器。 在 [目的地] 區段中選擇 [選取虛擬機器]，然後選擇要測試的正確虛擬機器和連接埠。

在按一下 [檢查] 後，系統就會檢查指定連接埠上之虛擬機器之間的連線能力。 在範例中，目的地 VM 無法連線，因此會顯示躍點清單。

![查看虛擬機器的連線能力結果][1]

## <a name="check-remote-endpoint-connectivity"></a>檢查遠端端點的連線能力

若要檢查遠端端點的連線能力和延遲，請在 [目的地] 區段中選擇 [手動指定] 選項按鈕，輸入 URL 和連接埠，然後按一下 [檢查]。  這會用於網站與儲存體端點之類的遠端端點。

![查看網站的連線能力結果][2]

## <a name="next-steps"></a>後續步驟

檢視[建立由警示觸發的封包擷取](network-watcher-alert-triggered-packet-capture.md)來了解如何透過虛擬機器警示自動化封包擷取

造訪[檢查 IP 流量驗證](network-watcher-check-ip-flow-verify-portal.md)來得知 VM 是否允許特定流量流入或流出

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png
