---
title: "針對 Azure 虛擬網路閘道和連線進行疑難排解 - PowerShell | Microsoft Docs"
description: "此頁面說明如何使用 Azure 網路監看員來針對 PowerShell Cmdlet 進行疑難排解"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: c3fa22bd599026b0838b134e26062d9837df703e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>使用 Azure 網路監看員 PowerShell 來針對虛擬網路閘道和連線進行疑難排解

> [!div class="op_single_selector"]
> - [入口網站](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

網路監看員提供了許多功能，因為它的作用就是為了讓您了解您在 Azure 中的網路資源。 這些功能的其中之一便是資源疑難排解。 您可以透過入口網站、PowerShell、CLI 或 REST API 呼叫資源疑難排解。 一經呼叫，網路監看員就會檢查虛擬網路閘道或連線的健全狀況，並傳回其調查結果。

## <a name="before-you-begin"></a>開始之前

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員。

如需支援的閘道類型清單，請瀏覽[支援的閘道類型](network-watcher-troubleshoot-overview.md#supported-gateway-types)。

## <a name="overview"></a>概觀

資源疑難排解可讓您針對虛擬網路閘道和連線所發生的問題進行疑難排解。 在要求進行資源疑難排解後，便會查詢並檢查記錄。 檢查完成時，就會傳回結果。 資源疑難排解要求是執行時間很長的要求，可能需要幾分鐘的時間才會傳回結果。 疑難排解記錄會儲存在指定儲存體帳戶的容器中。

## <a name="troubleshoot-a-gateway-or-connection"></a>針對閘道或連線進行疑難排解

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)然後按一下 [網路] > [網路監看員] > [VPN 診斷]
2. 選取 [訂用帳戶]、[資源群組] 和 [位置]。
3. 資源疑難排解會傳回有關資源健康情況的資料。 它也會將相關的記錄儲存至儲存體帳戶以供檢閱。 按一下 [儲存體帳戶] 以選取儲存體帳戶。
4. 在 [儲存體帳戶] 刀鋒視窗上，選取現有的儲存體帳戶，或按一下 [+ 儲存體帳戶] 來建立新的帳戶。
5. 在 [容器] 刀鋒視窗上，選擇現有的容器，或按一下 [+ 容器] 來建立新的容器。 完成時，按一下 [選取]
6. 選取要進行疑難排解的閘道和連線資源，並按一下 [開始疑難排解]

如果選取多個資源，系統會在這些閘道資源上同時執行疑難排解。 它無法同時在連線及與其相關聯的閘道上執行。 建議先針對閘道進行疑難排解，因為對連線進行疑難排解的程序會花上較久的時間。 當 VPN 診斷在資源上執行時，[疑難排解狀態] 欄位會顯示 [正在執行] 的狀態

完成時，狀態欄位會變更為 [狀況良好] 或 [狀況不良]。

![疑難排解完成][2]

## <a name="understanding-the-results"></a>了解結果

在視窗的 [詳細資料] 區段中，[狀態] 索引標籤會針對所選的資源，顯示上次執行的疑難排解狀態。 最新的診斷結果會顯示自上次執行後已經過 xx 分鐘。

|屬性  |說明  |
|---------|---------|
|資源     | 資源的連結。        |
|儲存體路徑     |  包含記錄檔的儲存體帳戶及容器的路徑 (若在執行期間有產生的話)。 在您離開入口網站之後，此設定將不會保存。        |
|摘要     | 資源健康狀態的摘要。        |
|詳細資料     | 資源健康狀態的詳細資訊。        |
|上次執行     | 上次執行疑難排解的時間。        |


[動作] 索引標籤會提供如何解決問題的一般指引。 如果問題有可行動作，則會提供附有其他指引的連結。 如果沒有其他指引，回應中會提供 URL 以供您開啟支援案例。  如需回應屬性和所含內容的詳細資訊，請瀏覽[網路監看員疑難排解概觀](network-watcher-troubleshoot-overview.md)


## <a name="next-steps"></a>後續步驟

如果設定已變更而停止了 VPN 連線，請參閱[管理網路安全性群組](../virtual-network/virtual-network-manage-nsg-arm-portal.md)以追蹤可能有問題的網路安全性群組和安全性規則。


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png
