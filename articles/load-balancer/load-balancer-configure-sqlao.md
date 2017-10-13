---
title: "設定負載平衡器使用 SQL 一律開啟 | Microsoft Docs"
description: "設定負載平衡器使用 SQL 一律開啟，以及如何運用 powershell 來建立 SQL 實作的負載平衡器"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3ebbf1c4009d89b1f18b2ff8ff5dd243c456dff8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="configure-load-balancer-for-sql-always-on"></a>設定負載平衡器使用 SQL 一律開啟

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

SQL Server AlwaysOn 可用性群組現在可以與 ILB 搭配執行。 可用性群組是 SQL Server 針對高可用性和災難復原的旗艦解決方案。 不論組態中的複本數目為何，可用性群組接聽程式可讓用戶端應用程式順暢地連接到主要複本。

接聽程式 (DNS) 名稱會對應至負載平衡的 IP 位址，而 Azure 的負載平衡器會將連入流量只導向複本集中的主要伺服器。

您可以使用 ILB 的 SQL Server AlwaysOn (接聽程式) 端點支援。 您現在可以控制接聽程式的存取設定，並且可以從虛擬網路 (VNet) 的特定子網路中選擇負載平衡的 IP 位址。

在接聽程式上使用 ILB 之後，只有下列項目可以存取 SQL Server 端點 (例如 Server=tcp:ListenerName,1433;Database=DatabaseName)：

* 相同虛擬網路中的服務與 VM
* 來自已連線內部部署網路的服務與 VM
* 來自互連式 VNet 的服務與 VM

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

圖 1 - 使用網際網路面向負載平衡器設定的 SQL AlwaysOn

## <a name="add-internal-load-balancer-to-the-service"></a>將內部負載平衡器新增至服務

1. 在下列範例中，我們將設定一個包含名為 'Subnet-1' 之子網路的虛擬網路：

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. 為每個 VM 上的 ILB 新增負載平衡端點

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    上述範例中，您有 2 個分別稱為 "sqlsvc1" 和 "sqlsvc2" 的 VM 正在雲端服務 "Sqlsvc" 中執行。 在使用 `DirectServerReturn` 參數建立 ILB 之後，您會將負載平衡的端點新增 ILB，讓 SQL 能針對可用性群組設定接聽程式。

如需有關 SQL AlwaysOn 的詳細資訊，請參閱[針對 Azure 中的 AlwaysOn 可用性群組設定內部負載平衡器](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)。

## <a name="see-also"></a>另請參閱
[開始設定網際網路面向的負載平衡器](load-balancer-get-started-internet-arm-ps.md)

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
