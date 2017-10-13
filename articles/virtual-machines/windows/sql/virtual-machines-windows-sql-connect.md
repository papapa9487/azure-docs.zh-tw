---
title: "連線到 SQL Server 虛擬機器 (資源管理員) | Microsoft Docs"
description: "了解如何連接在 Azure 虛擬機器上執行的 SQL Server。 本主題使用傳統部署模型。 案例會視網路組態和用戶端的位置而有所不同。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/14/2017
ms.author: jroth
ms.openlocfilehash: 67ba43f9456bbeffbf602067586143c4c68af672
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>連線到 Azure 上的 SQL Server 虛擬機器 (資源管理員)
> [!div class="op_single_selector"]
> * [資源管理員](virtual-machines-windows-sql-connect.md)
> * [傳統](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Overview

本主題說明如何連線到在 Azure 虛擬機器上執行的 SQL Server 執行個體。 其中涵蓋一些[一般連線案例](#connection-scenarios)並提供[在 Azure VM 中設定 SQL Server 連線的詳細步驟](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm)。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

如需有關佈建和連線能力的完整逐步解說，請參閱 [在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

## <a name="connection-scenarios"></a>連接案例

用戶端連線到在虛擬機器上執行的 SQL Server 方式，取決於用戶端的位置與網路組態。

如果您是在 Azure 入口網站中佈建 SQL Server VM，可以選擇指定 **SQL 連線能力**的類型。

![佈建期間的公用 SQL 連線能力選項](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

連線能力的選項包括：

| 選項 | 說明 |
|---|---|
| **公開** | 透過網際網路連線到 SQL Server |
| **私用** | 連接相同虛擬網路中的 SQL Server |
| **本機** | 在相同的虛擬機器上本機連線到 SQL Server | 

下列各節更詳細說明**公開**和**私用**選項。

## <a name="connect-to-sql-server-over-the-internet"></a>連接網際網路中的 SQL Server

如果您需要從網際網路連線到您的 SQL Server 資料庫引擎，請於佈建期間，在入口網站中針對 [SQL 連線能力]類型選取 [公開]。 入口網站會自動執行下列步驟：

* 啟用 SQL Server 的 TCP/IP 通訊協定。
* 設定防火牆規則，以開啟 SQL Server TCP 連接埠 (預設為 1433)。
* 可進行公用存取所需的 SQL Server 驗證。
* 將 VM 上的網路安全性群組設定為 SQL Server 連接埠上的所有 TCP 流量。

> [!IMPORTANT]
> SQL Server Developer 和 Express 版本的虛擬機器映像不會自動啟用 TCP/IP 通訊協定。 在 Developer 和 Express 版本中，您必須在建立 VM 之後，使用「SQL Server 組態管理員」來[手動啟用 TCP/IP 通訊協定](#manualtcp)。

任何能夠存取網際網路的用戶端都能藉由指定虛擬機器的公用 IP 位址或指派給該 IP 位址的任何 DNS 標籤，連線到 SQL Server 執行個體。 如果 SQL Server 連接埠是 1433，則不需要在連接字串中指定它。 下列連接字串會使用 SQL 驗證 (您也可以使用公用 IP 位址) 連線到具有 DNS 標籤的 SQL VM`sqlvmlabel.eastus.cloudapp.azure.com`。

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

用戶端雖然可透過網際網路連線，但這不表示任何人都可以連接您的 SQL Server。 外部用戶端必須要有正確的使用者名稱和密碼。 不過，為了增加安全性，您可以避免使用已知的連接埠 1433。 舉例來說，如果您將 SQL Server 設定為在連接埠 1500 進行接聽，並建立適當的防火牆和網路安全性群組規則，您就可以在伺服器名稱附加連接埠號碼來進行連線。 下列範例會將自訂連接埠號碼 **1500** 新增至伺服器名稱，來改變上一個：

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> 當您透過網際網路查詢 VM 中的 SQL Server 時，所有從 Azure 資料中心傳出的資料都會以一般[輸出資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)計費。

## <a name="connect-to-sql-server-within-a-virtual-network"></a>連線到相同虛擬網路內的 SQL Server

當您在入口網站中針對 [SQL 連線能力]類型選擇 [私用] 時，Azure 會將大部分設定設定為與**公用**的相同。 其中一項差異是沒有任何網路安全性群組規則可允許 SQL Server 連接埠 (預設為 1433) 上的外部流量。

> [!IMPORTANT]
> SQL Server Developer 和 Express 版本的虛擬機器映像不會自動啟用 TCP/IP 通訊協定。 在 Developer 和 Express 版本中，您必須在建立 VM 之後，使用「SQL Server 組態管理員」來[手動啟用 TCP/IP 通訊協定](#manualtcp)。

私用連線能力通常與[虛擬網路](../../../virtual-network/virtual-networks-overview.md)搭配使用，可進行數個情節。 您可以連接在相同虛擬網路中的 VM，即使這些 VM 位於不同的資源群組也可以。 [站對站 VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)可讓您建立能將 VM 連接至內部部署網路和電腦的混合式架構。

虛擬網路也可讓您將 Azure VM 加入網域。 這是在 SQL Server 使用的 Windows 驗證的唯一方式。 其他連接案例則需要使用者名稱和密碼進行 SQL 驗證。

假設您已在虛擬網路中設定 DNS，您便可以在連接字串中指定 SQL Server VM 電腦名稱來連線到 SQL Server 執行個體。 下列範例假設「Windows 驗證」也已設定妥當，且使用者已獲得存取 SQL Server 執行個體的權限。

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a>變更 SQL 連線能力設定

您可以在 Azure 入口網站中變更您 SQL Server 虛擬機器的連線能力設定。

1. 在 Azure 入口網站中，選取 [虛擬機器]。

2. 選取 SQL Server VM。

3. 在 [設定] 下，按一下 [SQL Server 組態]。

4. 將 **SQL 連線能力層級**變更為必要的設定。 可選擇性地使用此區域來變更 SQL Server 連接埠或 SQL 驗證設定。

   ![變更 SQL 連線能力](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. 稍候幾分鐘才能完成更新。

   ![SQL VM 更新通知](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> 針對 Developer 和 Express 版本啟用 TCP/IP

變更 SQL Server 連線能力連線時，Azure 不會對 SQL Server Developer 和 Express 版本自動啟用 TCP/IP 通訊協定。 以下步驟說明如何以手動方式啟用 TCP/IP，以便您經由 IP 位址從遠端連線。

首先，使用遠端桌面連線到 SQL Server 電腦。

> [!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

接下來，使用 **SQL Server 組態管理員**來啟用 TCP/IP 通訊協定。

> [!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>以 SSMS 連線

下列步驟示範如何建立 Azure VM 的選擇性 DNS 標籤，然後與 SQL Server Management Studio (SSMS) 連線。

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>後續步驟

若要查看佈建指示以及連線步驟，請參閱 [在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

如需在 Azure VM 中執行 SQL Server 的其他相關主題，請參閱 [Azure 虛擬機器上的 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)。