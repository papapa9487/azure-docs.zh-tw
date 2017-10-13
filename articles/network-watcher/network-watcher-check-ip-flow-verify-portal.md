---
title: "使用 Azure 網路監看員 IP 流量驗證來驗證流量 - Azure 入口網站 | Microsoft Docs"
description: "本文說明如何檢查虛擬機器中的流入或流出流量是被允許或拒絕"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8333ccfd1e4cc917c8af4b3006292e43b77ecc7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>使用 Azure 網路監看員的 IP 流量驗證元件來檢查 VM 中的流入或流出流量是被允許或拒絕

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)


IP 流量驗證是網路監看員的一項功能，可讓您驗證虛擬機器中的流入或流出流量是否被允許。 可執行傳入或傳出流量的驗證。 此案例可用於取得虛擬機器的目前狀態，以了解其是否可以和外部資源或其他資源。 IP 流量驗證可用來驗證是否已正確設定網路安全性群組 (NSG) 規則，並針對 NSG 規則所封鎖的流量進行疑難排解。 使用 IP 流量驗證的另一個原因是可以確保 NSG 會正確封鎖您想要封鎖的流量。

## <a name="before-you-begin"></a>開始之前

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員，或您擁有現有的網路監看員執行個體。 此案例也假設已有具有有效虛擬機器的資源群組可供使用。

## <a name="scenario"></a>案例

此案例使用 IP 流量驗證來驗證虛擬機器是否可以透過連接埠 443 和其他機器通訊。 如果流量被拒絕，它會傳回拒絕該流量的安全性規則。 若要深入了解 IP 流量驗證，請造訪 [IP 流量驗證概觀](network-watcher-ip-flow-verify-overview.md)

### <a name="run-ip-flow-verify"></a>執行 IP 流量驗證

瀏覽至您的網路監看員，然後按一下 [IP 流量驗證]。 選取虛擬機器和您想要驗證流量的網路介面。 輸入任何其他篩選的資訊，然後按一下 [檢查]。

一旦您按一下 [檢查]，會檢查根據您提供之準則的流程。 結果是**允許存取**或**拒絕存取**。 如果存取被拒，會提供封鎖流量的網路安全性群組 (NSG) 和安全性規則。 如果拒絕流量是預期的行為，則此規則已成功。

> [!NOTE]
> IP 流量驗證需要配置 VM 資源。

您可以從下列映像中看到，允許輸出的 HTTPS 流量。

![IP 流量驗證概觀][1]

如下圖所示，流量會變更為輸入，輸入連接埠會變更為 123。 流量現在會遭拒絕，會提供「拒絕存取」訊息與網路安全性群組以及拒絕流量的安全性規則。

![IP 流量結果][2]

## <a name="next-steps"></a>後續步驟

如果流量遭到封鎖，但不應如此，請參閱[管理網路安全性群組](../virtual-network/virtual-network-manage-nsg-arm-portal.md)以追蹤網路安全性群組和所定義的安全性規則。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













