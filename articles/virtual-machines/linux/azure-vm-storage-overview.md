---
title: "Azure Linux VM 與 Azure 儲存體 | Microsoft Docs"
description: "描述 Azure 標準和進階儲存體以及 Linux 虛擬機器的受控和非受控磁碟。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: d364c69e-0bd1-4f80-9838-bbc0a95af48c
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 2/7/2017
ms.author: rasquill
ms.openlocfilehash: 934580f6fcfdbff6e61626ed685459478559717d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2017
---
# <a name="azure-and-linux-vm-storage"></a>Azure 和 Linux VM 儲存體
Azure 儲存體是現代應用程式的雲端儲存體解決方案，這些應用程式仰賴持續性、可用性和可調整性來滿足其客戶的需求。  除了可讓開發人員打造支援全新案例的大規模應用程式之外，Azure 儲存體還針對 Microsoft 虛擬機器提供儲存基礎。

## <a name="managed-disks"></a>受控磁碟

現在可利用 [Azure 受控磁碟](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)取得 Azure VM，該功能可讓您建立 VM，而不需自行建立或管理任何 [Azure 儲存體帳戶](../../storage/common/storage-introduction.md)。 您可指定是否想要進階或標準儲存體、磁碟應該多大，而 Azure 會為您建立 VM 磁碟。 具有受控磁碟的 VM 都有許多重要的功能，包括︰

- 自動延展性支援。 Azure 會建立磁碟及管理基礎儲存體，可支援每個訂用帳戶多達 10,000 個磁碟。
- 提高可用性設定組的可靠性。 Azure 可確保可用性設定組內的 VM 磁碟會自動彼此隔離。
- 提高存取控制權。 受控磁碟會公開由 [Azure 角色型存取控制 (RBAC)](../../active-directory/role-based-access-control-what-is.md) 所控制的各種作業。

受控磁碟與非受控磁碟的價格不同。 如需該資訊，請參閱[受控磁碟的價格和計費](../windows/managed-disks-overview.md#pricing-and-billing)。

您可以透過 [az vm convert](/cli/azure/vm#convert)，將使用非受控磁碟的現有 VM 轉換成使用受控磁碟。 如需詳細資訊，請參閱[如何將 Linux VM 從非受控磁碟轉換為 Azure 受控磁碟](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 如果非受控磁碟位於使用 (或曾經使用) [Azure 儲存體服務加密 (SSE)](../../storage/common/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 加密的儲存體帳戶，您就無法將非受控磁碟轉換為受控磁碟。 下列步驟將詳細說明如何轉換位於 (曾經位於) 已加密儲存體帳戶中的非受控磁碟︰

- 使用 [az storage blob copy start](/cli/azure/storage/blob/copy#start)，將虛擬硬碟 (VHD) 複製到從未針對 Azure 儲存體服務加密啟用的儲存體帳戶。
- 建立使用受控磁碟的 VM，並在透過 [az vm create](/cli/azure/vm#create) 建立期間指定該 VHD 檔案，或
- 使用 [az vm disk attach](/cli/azure/vm/disk#attach)，將複製的 VHD 附加至具有受控磁碟的執行中 VM。


## <a name="azure-storage-standard-and-premium"></a>Azure 儲存體：標準和進階
Azure VM (不論是使用受控或非受控磁碟) 可以標準儲存體磁碟或進階儲存體磁碟做為建置基礎。 使用入口網站選擇您的 VM 時，您必須切換 [基本] 畫面上的下拉式清單以檢視標準和進階磁碟。 切換為 SSD 時，只會顯示已啟用 VM 的進階儲存體，均受到 SSD 磁碟機支援。  切換至 HDD 時，會顯示轉動型磁碟機所支援之已啟用標準儲存體的 VM，以及由 SSD 支援的進階儲存體。

從 `azure-cli` 建立 VM，透過 `-z` 或 `--vm-size` cli 旗標選擇 VM 大小時，您可以在標準和進階之間選擇。

## <a name="creating-a-vm-with-a-managed-disk"></a>建立具有受控磁碟的 VM

下列範例需要 Azure CLI 2.0，您可以[在此安裝](/cli/azure/install-azure-cli)。

首先，使用 [az group create](/cli/azure/group#create) 建立資源群組來管理資源：

```azurecli
az group create --location westus --name myResourceGroup
```

現在，使用 [az vm create](/cli/azure/vm#create) 建立 VM。 指定唯一的 `--public-ip-address-dns-name` 引數，因為可能接受 `mypublicdns`。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns
```

前一個範例在標準儲存體帳戶中建立具有受控磁碟的 VM。 若要使用進階儲存體帳戶，請新增 `--storage-sku Premium_LRS` 引數，如下列範例所示︰

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns \
    --storage-sku Premium_LRS
```

## <a name="standard-storage"></a>標準儲存體
Azure 標準儲存體是預設的儲存體類型。  標準儲存體符合成本效益同時仍然能夠發揮效能。  

## <a name="premium-storage"></a>進階儲存體
針對執行時需要大量 I/O 之工作負載的虛擬機器，「Azure 進階儲存體」可提供高效能、低延遲的磁碟支援。 使用「進階儲存體」的虛擬機器 (VM) 會將資料儲存在固態硬碟 (SSD) 上。 您可以將應用程式的 VM 磁碟移轉到「Azure 進階儲存體」，以利用這些磁碟的速度和效能。

進階儲存體功能：

* 進階儲存體磁碟：Azure 進階儲存體支援可連接到 DS、DSv2 或 GS 系列 Azure VM 的 VM 磁碟。
* 進階儲存體 Blob：進階儲存體可支援 Azure 分頁 Blob，其用於保存適用於 Azure 虛擬機器 (VM) 的永續性磁碟。
* 進階本地備援儲存體：進階儲存體帳戶僅支援本地備援儲存體 (LRS) 作為複寫選項，並在單一區域內會保留三份資料。
* [進階儲存體](../windows/premium-storage.md)

## <a name="premium-storage-supported-vms"></a>進階儲存體支援的 VM
「進階儲存體」支援 DS 系列、DSv2 系列、GS 系列及 Fs 系列的 Azure 虛擬機器 (VM)。 您可以將「標準」和「進階」儲存體磁碟與「進階儲存體」支援的 VM 搭配使用。 但是不能將「進階儲存體」磁碟與非「進階儲存體」相容的 VM 系列搭配使用。

以下是我們驗證能使用 Premium 儲存體的 Linux 散發套件。

| 配送映像 | 版本 | 支援的核心 |
| --- | --- | --- |
| Ubuntu |12.04 |3.2.0-75.110+ |
| Ubuntu |14.04 |3.13.0-44.73+ |
| Debian |7.x、8.x |3.16.7-ckt4-1+ |
| SLES |SLES 12 |3.12.36-38.1+ |
| SLES |SLES 11 SP4 |3.0.101-0.63.1+ |
| CoreOS |584.0.0+ |3.18.4+ |
| Centos |6.5, 6.6, 6.7, 7.0, 7.1 |3.10.0-229.1.2.el7+ |
| RHEL |6.8+、7.2+ | |

## <a name="azure-file-storage"></a>Azure 檔案儲存體
Azure 檔案儲存體可在雲端中使用標準的 SMB 通訊協定提供檔案共用。 使用 Azure 檔案，您可以將依賴檔案伺服器的企業應用程式移轉至 Azure。 在 Azure 中執行的應用程式可以從執行 Linux 的 Azure 虛擬機器輕鬆地掛接檔案共用。 有了最新版本的檔案儲存體後，您也可以從支援 SMB 3.0 的內部部署應用程式掛接檔案共用。  由於檔案共用為 SMB 共用，因此您可以透過標準檔案系統 API 存取它們。

檔案儲存體是使用與 Blob、資料表和佇列儲存體相同的技術建置，因此檔案儲存體能夠提供可用性、持續性、延展性和建置於 Azure 儲存體平台內的異地備援。 如需有關檔案儲存體效能目標和限制的詳細資訊，請參閱「Azure 儲存體延展性和效能目標」。

* [如何搭配使用 Azure 檔案儲存體與 Linux](../../storage/files/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>經常性存取儲存體
Azure 經常性存取儲存層已最佳化，可用於儲存經常存取的資料。  經常性存取儲存體是 blob 存放區的預設儲存體類型。

## <a name="cool-storage"></a>非經常性儲存體
Azure 非經常性存取儲存層已最佳化，可用於儲存不常存取且長期存留的資料。 非經常性儲存體的使用案例範例包括備份、媒體內容、科學資料、規範和封存資料。 一般情況下，很少存取的任何資料是非經常性儲存體的完美候選項目。

|  | 經常性存取儲存層 | 非經常性存取儲存層 |
|:--- |:---:|:---:|
| Availability |99.9% |99% |
| 可用性 (RA-GRS 讀取) |99.99% |99.9% |
| 使用費用 |儲存成本較高 |儲存成本較低 |
| 較低的存取 |較高的存取 | |
| 和交易成本 |和交易成本 | |

## <a name="redundancy"></a>備援性
Microsoft Azure 儲存體帳戶中的資料一律會進行複寫以確保持久性及高可用性，即使在面對暫時性的硬體故障時，仍可滿足 Azure 儲存體 SLA。

建立儲存體帳戶時，您必須選取下列其中一個複寫選項：

* 本機備援儲存體 (LRS)
* 區域備援儲存體 (ZRS)
* 異地備援儲存體 (GRS)
* 讀取權限異地備援儲存體 (RA-GRS)

### <a name="locally-redundant-storage"></a>本地備援儲存體
本地備援儲存體 (LRS) 會在您建立儲存體帳戶的區域內複寫資料。 若要達到最高持久性，針對儲存體帳戶資料所提出的每個要求都會複寫三次。 這三個複本會各自位於不同的容錯網域和升級網域中。  只有當要求已寫入這三個複本時，系統才會成功傳回該要求。

### <a name="zone-redundant-storage"></a>區域備援儲存體
區域備援儲存體 (ZRS) 會在單一區域，或兩個區域內的二或三個設備中複寫資料，以提供比 LRS 更高的持久性。 如果您的儲存體帳戶已啟用 ZRS，則即使其中一個設備發生故障，您的資料仍會是永久性。

### <a name="geo-redundant-storage"></a>異地備援儲存體
異地備援儲存體 (GRS) 會將資料複寫到與主要區域距離數百英哩的次要區域。 如果您的儲存體帳戶已啟用 GRS，即使發生主要區域因全區中斷或嚴重損壞而無法復原的情況，您的資料仍會是永久性。

### <a name="read-access-geo-redundant-storage"></a>讀取權限異地備援儲存體
除了 GRS 所提供的跨兩個區域複寫之外，讀取權限異地備援儲存體 (RA-GRS) 會透過提供次要位置資料的唯讀權限，最大化儲存體帳戶的可用性。 如果發生在主要區域中無法使用資料的情況，您的應用程式可以從次要區域讀取資料。

針對 Azure 儲存體備援性的深入探討，請參閱︰

* [Azure 儲存體複寫](../../storage/common/storage-redundancy.md)

## <a name="scalability"></a>延展性
Azure 儲存體可大幅擴充，因此您可以儲存並處理數百 TB 的資料，藉此支援科學、財務分析和媒體應用程式等所需的巨量資料案例。 或者您可以儲存小型企業網站所需的少量資料。 無論您的需求屬於何者，只需要為您儲存的資料付費。 Azure 儲存體目前儲存了數十兆的獨特客戶物件，且平均每秒處理上百萬個要求。

標準儲存體帳戶：標準儲存體帳戶的總要求率上限為 20000 IOPS。 在標準儲存體帳戶中，所有虛擬機器磁碟的 IOPS 總數不得超過此限。

進階儲存體帳戶：進階儲存體帳戶的總輸送量速率上限為 50 Gbps。 所有 VM 磁碟的總輸送量不應該超過此限。

## <a name="availability"></a>Availability
我們保證能成功處理從讀取權限異地備援儲存體 (RA-GRS) 帳戶讀取資料的要求，至少須達 99.99% (非經常性存取層為 99.9%)，但前提是從主要區域讀取資料失敗後，會轉往次要地區的 Microsoft Cloud 服務的差異化選項，為德國、歐盟 (EU) 和歐洲自由貿易聯盟 (EFTA) 重試。

我們保證能成功處理從本地備援儲存體 (LRS)、區域備援儲存體 (ZRS) 及異地備援儲存體 (GRS) 帳戶讀取資料之要求的時間，至少須達 99.9% (非經常性存取層為 99%)。

我們保證能成功處理處理將資料寫入本地備援儲存體 (LRS)、區域備援儲存體 (ZRS)、異地備援儲存體 (GRS) 帳戶，以及讀取權限異地備援儲存體 (RA-GRS) 帳戶之要求的時間，至少須達 99.9% (非經常性存取層為 99%)。

* [儲存體的 Azure SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)

## <a name="regions"></a>區域
Azure 已在全球 30 個區域正式運作，也宣佈計畫增加 4 個區域。 擴展地理位置對於 Azure 而言是首要目標，因為這樣會讓我們的客戶能達到更佳的效能，並支援資料位置相關的需求及喜好設定。  最新啟動 Azure 的區域是德國。

Microsoft Cloud Germany 提供已在歐洲可用的 Microsoft Cloud 服務的差異化選項，為德國、歐盟 (EU) 和歐洲自由貿易聯盟 (EFTA) 中相當穩固的合作夥伴和客戶建立創新和經濟成長的機會。

在這些新資料中心 (馬格德堡和法蘭克福) 中的客戶資料，是在資料信任者 T-Systems International (一家獨立的德國公司和 Deutsche Telekom 的子公司) 的控制之下受到管理。 這些資料中心的 Microsoft 商業雲端服務遵守德國的資料處理法規，並為客戶提供資料處理方式和位置的其他選項。

* [Azure 區域對應](https://azure.microsoft.com/regions/)

## <a name="security"></a>安全性
Azure 儲存體提供一組完整的安全性功能，讓開發人員能夠共同建置安全應用程式。 您可以使用角色型存取控制與 Azure Active Directory 來保護儲存體帳戶本身。 您可以使用用戶端加密、HTTP 或 SMB 3.0，在應用程式和 Azure 之間進行傳輸時保護資料的安全。 使用儲存體服務加密 (SSE)寫入 Azure 儲存體時，可將資料設定為自動加密。 您可以使用 Azure 磁碟加密，將虛擬機器所使用的作業系統和資料磁碟設定為加密。 Azure 儲存體中資料物件的委派存取權可以使用「共用存取簽章」來授與。

### <a name="management-plane-security"></a>管理平面安全性
管理平面包含用來管理儲存體帳戶的資源。 本節將討論 Azure Resource Manager 部署模型，以及如何使用角色型存取控制 (RBAC) 來控制存取儲存體帳戶。 我們也會討論如何管理儲存體帳戶金鑰，以及重新產生這類金鑰的方法。

### <a name="data-plane-security"></a>資料平面安全性
本節將探討如何在儲存體帳戶 (例如 Blob、檔案、查詢及表格) 中，允許使用共用存取簽章和預存存取原則來存取實際的資料物件。 我們將涵蓋服務層級的 SAS 和帳戶層級的 SAS。 我們也會看到如何限制存取特定的 IP 位址 (或 IP 位址範圍)、如何限制用於 HTTPS 的通訊協定，以及如何撤銷共用存取簽章，而不需等到它過期。

## <a name="encryption-in-transit"></a>傳輸中加密
本節討論如何在將資料傳輸至 Azure 儲存體或從中傳出時提供保護。 我們將討論 HTTPS 的建議用法，以及 SMB 3.0 針對 Azure 檔案共用所使用的加密。 同時也會探討用戶端加密，可讓您在將資料傳輸至用戶端應用程式中的儲存體之前加密資料，以及自儲存體傳出後解密資料。

## <a name="encryption-at-rest"></a>待用加密
我們將討論儲存體服務加密 (SSE)，以及如何為儲存體帳戶啟用此功能，讓您的區塊 Blob、分頁 Blob 和附加 Blob 可以在寫入 Azure 儲存體時自動加密。 同時也將探討如何使用 Azure 磁碟加密，並探索磁碟加密與 SSE 與用戶端加密之間的基本差異和案例。 我們將簡短探討美國政府電腦適用的 FIPS 相符性。

* [Azure 儲存體安全性指南](../../storage/common/storage-security-guide.md)

## <a name="temporary-disk"></a>暫存磁碟
每個 VM 都包含一個暫存磁碟。 暫存磁碟為應用程式和處理程序提供短期的儲存空間，且僅供用來儲存分頁檔之類的資料。 暫存磁碟上的資料可能會在[維護事件](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime)期間或當您[重新佈署 VM](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 時遺失。 在 VM 的標準重新開機期間，暫存磁碟上的資料會保留。

在 Linux 虛擬機器上，這個磁碟通常是 **/dev/sdb**，並且會由「Azure Linux 代理程式」將它格式化並裝載至 **/mnt/**。 暫存磁碟的大小會依據虛擬機器的大小而改變。 如需詳細資訊，請參閱 [Linux 虛擬機器的大小](sizes.md)。

如需有關 Azure 如何使用暫存磁碟的詳細資訊，請參閱 [Understanding the temporary drive on Microsoft Azure Virtual Machines (了解 Microsoft Azure 虛擬機器上的暫存磁碟機)](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="cost-savings"></a>節省成本
* [儲存成本](https://azure.microsoft.com/pricing/details/storage/)
* [儲存成本計算機](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>儲存體限制
* [儲存體服務限制](../../azure-subscription-service-limits.md#storage-limits)
