---
title: Red Hat Update Infrastructure (RHUI) | Microsoft Docs
description: "了解適用於 Microsoft Azure 中隨選 Red Hat Enterprise Linux 執行個體的 Red Hat Update Infrastructure (RHUI)"
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/10/2017
ms.author: borisb
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 43d9095bcbd490abc6d01c214a17b8586f92ed1e
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>適用於 Azure 中隨選 Red Hat Enterprise Linux VM 的 Red Hat Update Infrastructure (RHUI)
 [Red Hat 更新基礎結構](https://access.redhat.com/products/red-hat-update-infrastructure)允許雲端提供者 (例如 Azure) 鏡像 Red Hat 代管的存放庫內容、建立具有 Azure 特定內容的自訂存放庫，以及讓它可供使用者 VM 使用。

Red Hat Enterprise Linux (RHEL) 預付型方案 (PAYG) 映像預先設定為存取 Azure RHUI。 您不需要進行任何額外設定，只需在 RHEL 執行個體準備好接收最新更新之後執行 `sudo yum update` 即可。 此服務包含在 RHEL PAYG 軟體費用中。

## <a name="important-information-about-azure-rhui"></a>Azure RHUI 的重要資訊

1. RHUI 目前僅支援指定 RHEL 系列 (RHEL6 或 RHEL7) 中的最新次要版本。 當您執行 `sudo yum update` 時，連接至 RHUI 的 RHEL VM 執行個體會升級為最新的次要版本。 

    例如，如果您從 RHEL 7.2 PAYG 映像佈建 VM，並且執行 `sudo yum update`，最終會得到 RHEL 7.4 VM (RHEL7 系列中的目前最新次要版本)。

    若要避免此行為，您必須如[針對 Azure 建立及上傳以 Red Hat 為基礎的虛擬機器](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)文章中所述，建置自己的映像，並將它連接至不同的更新基礎結構 ([直接至 Red Hat 內容傳遞伺服器](https://access.redhat.com/solutions/253273)或 Red Hat 附屬伺服器)。

2. 對 Azure 代管之 RHUI 的存取，包含在 RHEL PAYG 映像價格中。 從 Azure 代管的 RHUI 將 PAYG RHEL VM 取消註冊並不會將虛擬機器轉換成自備授權 (BYOL) 類型的虛擬機器。 如果您以另一個更新來源註冊相同的 VM，可能會產生_間接_雙重費用：第一次是針對 Azure RHEL 軟體費用，而第二次是針對先前已購買的 Red Hat 訂用帳戶。 如果您持續需要使用非 Azure 代管 RHUI 的更新基礎結構，請考慮建立及部署您自己的 (BYOL 類型) 映像，如[建立及上傳適用於 Azure 的 Red Hat 型虛擬機器](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)所述。

3. Azure 中兩個類別的 RHEL PAYG 映像 (RHEL for SAP HANA，RHEL for SAP Business Applications) 會連接至專用 RHUI 通道，維持 SAP 憑證所需的特定 RHEL 次要版本。 

4. 只有 [Microsoft Azure Datacenter IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)中的 VM 能夠存取 Azure 代管的 RHUI。 如果您透過內部部署網路基礎結構來 Proxy 所有 VM 流量，您可以需要為 RHEL PAYG VM 設定使用者定義的路由，以存取 Azure RHUI。

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUI 內容傳遞伺服器的 IP
在所有可使用 RHEL 隨選映像的地區，皆可使用 RHUI。 目前包含 [Azure 狀態儀表板](https://azure.microsoft.com/status/)頁面上所列的所有公用區域 (Azure 美國政府和 Azure 德國區域)。 

如果您使用網路組態來進一步限制來自 RHEL PAYG VM 的存取，請確定已允許下列 IP，如此 `yum update` 才能依據您所在的環境運作。 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>RHUI Azure 基礎結構更新

我們已在 2016 年 9 月部署更新的 Azure RHUI，並且在 2017 年 4 月關閉舊的 Azure RHUI。 如果您從 2016 年 9 月以後使用 RHEL PAYG 映像 (或其快照集)，系統會將您自動連線至新的 Azure RHUI。 不過，如果您擁有舊版快照集/VM，則其設定必須手動更新，才能如下所述存取 Azure RHUI。

新的 Azure RHUI 伺服器的部署將會透過 [Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)執行，讓任何 VM (不論是哪個區域) 都可以使用單一端點 (rhui-1.microsoft.com)。 

### <a name="troubleshooting-connection-problems-to-azure-rhui"></a>針對 Azure RHUI 連線問題進行疑難排解
如果您從 Azure RHEL PAYG VM 連線到 RHUI 時發生問題，請依照下列步驟操作
1. 檢查 Azure RHUI 端點的 VM 組態

    檢查 `/etc/yum.repos.d/rh-cloud.repo` 檔案之 `[rhui-microsoft-azure-rhel*]` 區段的 baseurl 中是否包含對 `rhui-[1-3].microsoft.com` 的參考。 如果是，您使用的便是新 Azure RHUI。

    如果它指向具有 `mirrorlist.*cds[1-4].cloudapp.net` 模式的位置，則需要進行組態更新。 您使用舊的 VM 快照集，請更新它以指向新的 Azure RHUI。

2. 只有 [Microsoft Azure Datacenter IP 範圍] 中的 VM 能夠存取 Azure 代管的 RHUI (https://www.microsoft.com/download/details.aspx?id=41653)。
 
3. 如果您使用的是新設定，已驗證 VM 是從 Azure IP 範圍連線，但仍無法連接到 Azure RHUI，請向 Microsoft 或 Red Hat 提出支援案例。

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>使用 Azure RHUI 伺服器的手動更新程序
此程序僅提供作為參考，RHEL PAYG 映像已經有連線到 Azure RHUI 的正確設定。

下載 (透過 curl) 公開金鑰簽章

```bash
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

驗證已下載的金鑰

```bash
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

檢查輸出，驗證 `keyid` 和 `user ID packet`：

```bash
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

安裝公開金鑰

```bash
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

下載、驗證及安裝用戶端 RPM。 

> [!NOTE]
> 套件版本變更，而且如果您要以手動方式連線到 Azure RHUI，您可以藉由從資源庫佈建最新的映像，找到每個 RHEL 系列的最新版本用戶端套件。
> 

下載：適用於 RHEL 6

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
```

適用於 RHEL 7

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
```

驗證：

```bash
rpm -Kv azureclient.rpm
```

確認輸出中的套件簽章正確

```bash
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

安裝 RPM

```bash
sudo rpm -U azureclient.rpm
```

完成時，請確認您可以從 VM 存取 Azure RHUI。

## <a name="next-steps"></a>後續步驟
若要從 Azure Marketplace 隨用隨付映像建立 Red Hat Enterprise Linux VM 並利用 Azure 代管的 RHUI，請移至 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/)。 
