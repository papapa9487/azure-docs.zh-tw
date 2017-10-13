---
title: "最佳化 VM 網路輸送量 | Microsoft Docs"
description: "了解如何最佳化 Azure 虛擬機器網路輸送量。"
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: steveesp
ms.openlocfilehash: 914747983d4d974810836be66d6c6af343f58b60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>最佳化 Azure 虛擬機器的網路輸送量

Azure 虛擬機器 (VM) 有預設網路設定，可進一步針對網路輸送量進行最佳化。 此文章說明如何最佳化 Microsoft Azure Windows 和 Linux VM (包括如 Ubuntu、CentOS 和 Red Hat 等主要發行版本) 的網路輸送量。

## <a name="windows-vm"></a>Windows VM

如果您的 Windows VM 支援[加速的網路](virtual-network-create-vm-accelerated-networking.md)，啟用該功能會是最適合輸送量的設定。 針對所有其他的 Windows VM，相較於不使用接收端調整 (RSS) 的 VM，使用 RSS 的 VM 可達到更高的最大輸送量。 根據預設，Windows VM 中可能會停用 RSS。 完成下列步驟來判斷是否已啟用 RSS，以及在它已停用的情況下將它啟用。

1. 輸入 `Get-NetAdapterRss` PowerShell 命令來查看是否已針對網路介面卡啟用 RSS。 在從 `Get-NetAdapterRss` 傳回的下列範例輸出中，RSS 並未啟用。

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : False
    ```
2. 輸入下列命令以啟用 RSS：

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    上述命令沒有輸出。 此命令已變更 NIC 設定，導致約一分鐘的暫時性連線中斷。 連線中斷時隨即出現 [正在重新連線] 對話方塊。 第三次嘗試後，連線通常就會恢復。
3. 再次輸入 `Get-NetAdapterRss` 命令以確認 VM 中已啟用 RSS。 如果成功，則會傳回下列範例輸出：

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>Linux VM

根據預設，Azure Linux VM 中一律會啟用 RSS。 2017 年 1 月之後發行的 Linux 核心包含新的網路最佳化選項，它們可讓 Linux VM 達到更高的網路輸送量。

### <a name="ubuntu"></a>Ubuntu

若要獲得最佳化，請先更新至截至 2017 年 6 月推出的最新支援版本，也就是：
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
更新完成之後，請輸入下列命令以取得最新的核心︰

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

選擇性命令︰

`apt-get -y dist-upgrade`
#### <a name="ubuntu-azure-preview-kernel"></a>Ubuntu 的 Azure 預覽版核心
> [!WARNING]
> 此 Azure Linux 預覽版核心可能沒有與正式發行版本的 Marketplace 映像和核心相同層級的可用性和可靠性。 此功能未受支援、能力可能受限，並且可能不如預設核心可靠。 請勿將此核心使用於生產工作負載。

藉由安裝建議的 Azure Linux 核心，可獲得顯著的輸送量效能。 若要嘗試此核心，請將這一行加入至 /etc/apt/sources.list

```bash
#add this to the end of /etc/apt/sources.list (requires elevation)
deb http://archive.ubuntu.com/ubuntu/ xenial-proposed restricted main multiverse universe
```

然後以 root 身分執行這些命令。
```bash
apt-get update
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

若要獲得最佳化，請先更新至截至 2017 年 7 月推出的最新支援版本，也就是：
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
更新完成之後，請安裝最新的 Linux Integration Services (LIS)。
輸送量最佳化選項從 LIS 4.2.2-2 版開始提供。 輸入下列命令以安裝 LIS：

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

若要獲得最佳化，請先更新至截至 2017 年 7 月推出的最新支援版本，也就是：
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7.3"
"Version": "7.3.2017071923"
```
更新完成之後，請安裝最新的 Linux Integration Services (LIS)。
輸送量最佳化選項從 LIS 4.2 版開始提供。 輸入下列命令以下載並安裝 LIS：

```bash
mkdir lis4.2.2-2
cd lis4.2.2-2
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
tar xvzf lis-rpms-4.2.2-2.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

若要深入了解 Linux Integration Services for Hyper-V 4.2 版，請檢視[下載頁面](https://www.microsoft.com/download/details.aspx?id=55106)。

## <a name="next-steps"></a>後續步驟
* 現在，已最佳化 VM，請針對您的案例查看[測試 Azure VM 的頻寬/輸送量](virtual-network-bandwidth-testing.md)的結果。
* 深入了解 [Azure 虛擬網路常見問題集 (FAQ)](virtual-networks-faq.md)
