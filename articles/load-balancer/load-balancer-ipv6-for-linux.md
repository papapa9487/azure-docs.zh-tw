---
title: "設定 Linux VM 的 DHCPv6 | Microsoft Docs"
description: "如何設定 Linux VM 的 DHCPv6。"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
keywords: "ipv6, azure load balancer, 雙重堆疊, 公用 ip, 原生 ipv6, 行動, iot"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5c591e7f1838c86ca74caea9dd3a5e8f874fd8a7
ms.contentlocale: zh-tw
ms.lasthandoff: 03/29/2017

---

# <a name="configuring-dhcpv6-for-linux-vms"></a>設定 Linux VM 的 DHCPv6

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure Marketplace 中的一些 Linux 虛擬機器映像沒有預設的 DHCPv6 設定。 若要支援 IPv6，在您使用的 Linux OS 散發套件內必須設定 DHCPv6。 不同 Linux 散發套件的 DHCPv6 設定方式不同，因為它們使用不同的套件。

> [!NOTE]
> Azure Marketplace 中最新的 SUSE Linux 和 CoreOS 映像已有預先設定 DHCPv6。 使用這些映像不需要再進行額外的變更。

本文件說明如何啟用 DHCPv6 使您的 Linux 虛擬機器取得 IPv6 位址。

> [!WARNING]
> 不當編輯網路組態檔可能會導致您失去 VM 的網路存取權。 我們建議您先在非生產系統上測試組態變更。 本文中的指示已經過在 Azure Marketplace 中最新版 Linux 映像上測試過。 如需您所用 Linux 版本的詳細指示，請參閱其文件。

## <a name="ubuntu"></a>Ubuntu

1. 編輯 `/etc/dhcp/dhclient6.conf` 檔案，新增以下這一行：

        timeout 10;

2. 編輯下列組態的 eth0 介面網路組態︰

   * 在 **Ubuntu 12.04 和 14.04** 上，編輯 `/etc/network/interfaces.d/eth0.cfg` 檔
   * 在 **Ubuntu 16.04** 上，編輯 `/etc/network/interfaces.d/50-cloud-init.cfg` 檔

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. 更新 IPv6 位址︰

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. 編輯 `/etc/dhcp/dhclient6.conf` 檔案，新增以下這一行：

        timeout 10;

2. 編輯 `/etc/network/interfaces` 檔案，新增以下組態：

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. 更新 IPv6 位址︰

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. 編輯 `/etc/sysconfig/network` 檔案，新增以下參數：

        NETWORKING_IPV6=yes

2. 編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，新增以下兩個參數：

        IPV6INIT=yes
        DHCPV6C=yes

3. 更新 IPv6 位址︰

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 & openSUSE 13

在 Azure 中最新的 SLES 和 openSUSE 映像已預先設定 DHCPv6。 使用這些映像不需要再進行額外的變更。 如果您的 VM 是以較舊或自訂 SUSE 映像建置而成，請使用下列步驟︰

1. 如有需要，安裝 `dhcp-client` 套件：

    ```bash
    sudo zypper install dhcp-client
    ```

2. 編輯 `/etc/sysconfig/network/ifcfg-eth0` 檔案，新增以下參數：

        DHCLIENT6_MODE='managed'

3. 更新 IPv6 位址︰

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 和 openSUSE Leap

在 Azure 中最新的 SLES 和 openSUSE 映像已預先設定 DHCPv6。 使用這些映像不需要再進行額外的變更。 如果您的 VM 是以較舊或自訂 SUSE 映像建置而成，請使用下列步驟︰

1. 編輯 `/etc/sysconfig/network/ifcfg-eth0` 檔案，取代此參數

        #BOOTPROTO='dhcp4'

    為下列值：

        BOOTPROTO='dhcp'

2. 將下列參數新增至 `/etc/sysconfig/network/ifcfg-eth0`：

        DHCLIENT6_MODE='managed'

3. 更新 IPv6 位址︰

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

在 Azure 中最新的 SLES 映像已預先設定 DHCPv6。 使用這些映像不需要再進行額外的變更。 如果您的 VM 是以較舊或自訂 CoreOS 映像建置而成，請使用下列步驟︰

1. 編輯 `/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. 更新 IPv6 位址︰

    ```bash
    sudo systemctl restart systemd-networkd
    ```

