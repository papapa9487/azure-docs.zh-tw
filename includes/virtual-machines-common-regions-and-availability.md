# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Azure 中虛擬機器的區域和可用性
Azure 在世界各地多個資料中心運作。 這些資料中心會依據地理區域分組，提供您彈性來選擇要建置應用程式的位置。 請務必了解虛擬機器 (VM) 在 Azure 中的運作方式和位置，以及可充分發揮效能、可用性和備援功能的選項。 本文提供 Azure 可用性和備援功能的概觀。

## <a name="what-are-azure-regions"></a>什麼是 Azure 區域？
您可以在定義的地理區域 (例如「美國西部」、「北歐」或「東南亞」) 中建立 Azure 資源。 您可以檢閱 [地區及其位置的清單](https://azure.microsoft.com/regions/)。 為了提供備援和可用性，每個區域內都有多個資料中心。 此方法可在您設計應用程式時提供彈性，以建立距離使用者最近的 VM，並滿足任何法務、合規性或稅務方面的目的。

## <a name="special-azure-regions"></a>特殊 Azure 區域
基於合規性或法務目的，Azure 有一些特殊區域，您在建置應用程式時可能會想要使用這些區域。 這些特殊區域包括：

* **美國維吉尼亞州政府**和**美國愛荷華州政府**
  * 由經篩選的美國人士所操作、適用於美國政府機構及其合作夥伴的實體與邏輯網路隔離 Azure 執行個體。 包含其他法規遵循認證，例如 [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) 和 [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA)。 深入了解 [Azure Government](https://azure.microsoft.com/features/gov/)。
* **中國東部**和**中國北部**
  * 這些區域是透過 Microsoft 與 21Vianet 之間的唯一合作關係提供，其中 Microsoft 不會直接維護資料中心。 深入了解 [位於中國的 Microsoft Azure](http://www.windowsazure.cn/)。
* **德國中部**和**德國東北部**
  * 這些區域透過資料信任者模型提供，其中客戶資料會留在德國受到 T-Systems 控管，這是 Deutsche Telekom 旗下的公司，扮演德國資料信任者的角色。

## <a name="region-pairs"></a>區域配對
每個 Azure 區域都會與相同地理位置 (例如美國、歐洲或亞洲) 內的另一個區域配對。 此方法允許複寫整個地理位置的資源 (例如 VM 儲存體)，而應能降低發生同時影響兩個區域之自然災害、社會動亂、電力中斷或實體網路中斷的可能性。 區域配對的其他優點包括：

* 如果發生更大範圍的 Azure 中斷狀況，會優先復原所有配對中的一個區域，以協助縮短應用程式的還原時間。 
* 計劃性 Azure 更新會以逐個區域的方式發行至配對的區域，以將停機時間縮到最短並將應用程式中斷風險降到最低。
* 資料會繼續駐留在與其配對相同的地理位置內 (巴西南部除外)，以符合稅務和執法管轄區的要求。

區域配對的範例包括：

| 主要 | 次要 |
|:--- |:--- |
| 美國西部 |美國東部 |
| 北歐 |西歐 |
| 東南亞 |東亞 |

您可以 [在這裡看到完整的區域配對清單](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)。

## <a name="feature-availability"></a>功能可用性
有些服務或 VM 功能只有在特定區域提供，例如特定的 VM 大小或儲存體類型。 也有一些不會要求您選取特定區域的通用 Azure 服務，例如 [Azure Active Directory](../articles/active-directory/active-directory-whatis.md)、[流量管理員](../articles/traffic-manager/traffic-manager-overview.md)或 [Azure DNS](../articles/dns/dns-overview.md)。 為了協助您設計應用程式環境，您可以查看 [每個區域的 Azure 服務可用性](https://azure.microsoft.com/regions/#services)。 

## <a name="storage-availability"></a>儲存體可用性
在您考慮可用的儲存體複寫選項時，了解 Azure 區域和地理位置會變得相當重要。 根據儲存體類型，您會有不同的複寫選項。

**Azure 受控磁碟**
* 本機備援儲存體 (LRS)
  * 此選項會在您建立儲存體帳戶的區域內複寫資料三次。

**儲存體帳戶型磁碟**
* 本機備援儲存體 (LRS)
  * 此選項會在您建立儲存體帳戶的區域內複寫資料三次。
* 區域備援儲存體 (ZRS)
  * 此選項會在單一區域或兩個區域內的兩個到三個設備複寫資料三次。
* 異地備援儲存體 (GRS)
  * 此選項會將資料複寫到與主要區域距離數百英哩的次要區域。
* 讀取權限異地備援儲存體 (RA-GRS)
  * 此選項會與 GRS 一樣將資料複寫到次要區域，但此外還提供對次要位置中資料的唯讀存取權。

下表提供儲存體複寫類型間差異的快速概觀︰

| 複寫策略 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 可跨多個設備複寫資料。 |否 |是 |是 |是 |
| 可從次要位置及主要位置讀取資料。 |否 |否 |否 |是 |
| 可在不同的節點上維護的資料副本數量。 |3 |3 |6 |6 |

您可以 [在這裡深入了解 Azure 儲存體複寫選項](../articles/storage/common/storage-redundancy.md)。 如需受控磁碟的詳細資訊，請參閱 [Azure 受控磁碟概觀](../articles/virtual-machines/windows/managed-disks-overview.md)。

### <a name="storage-costs"></a>儲存成本
價格會依據您選取的儲存體類型和可用性而有所不同。

**Azure 受控磁碟**
* 進階受控磁碟是由固態硬碟 (SSD) 所支援，標準受控磁碟則是由一般轉動式磁碟所支援。 進階和標準受控磁碟的收費是依據磁碟的佈建容量。

**非受控磁碟**
* 進階儲存體是由固態硬碟 (SSD) 所支援，收費方式是依據磁碟的容量。
* 標準儲存體是由一般轉動式磁碟支援，收費方式是依據使用的容量和所需的儲存體可用性。
  * 針對 RA-GRS，會有一個額外的「異地複寫資料傳輸」費用，此費用是將該資料複寫到另一個 Azure 區域中時的頻寬費用。

如需有關不同儲存體類型和可用性選項的定價資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/) 。

## <a name="availability-sets"></a>可用性設定組
可用性設定組是 VM 的邏輯群組，可讓 Azure 了解您應用程式的建置方式，以提供備援和可用性。 建議您在可用性設定組內建立兩個或多個 VM，以提供具高可用性的應用程式，以及符合 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 當單一 VM 是使用 [Azure 進階儲存體](../articles/storage/common/storage-premium-storage.md)時，非計劃性的維護事件適用 Azure SLA。 

可用性設定組是由可防止硬體故障及允許安全地套用更新的兩個額外群組所組成 - 容錯網域 (FD) 和更新網域 (UD)。

![更新網域和容錯網域組態的概念圖](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

您可以深入了解如何管理 [Linux VM](../articles/virtual-machines/linux/manage-availability.md) 或 [Windows VM](../articles/virtual-machines/windows/manage-availability.md)。

### <a name="fault-domains"></a>容錯網域
容錯網域是共用通用電源和網路交換器的基礎硬體邏輯群組，類似於內部部署資料中心內的機架。 當您在可用性設定組中建立 VM 時，Azure 平台會自動將您的 VM 分散於這些容錯網域。 此方法可限制潛在實體硬體錯誤、網路中斷或電源中斷的影響。

### <a name="update-domains"></a>更新網域
更新網域是可以同時進行維護或重新啟動的基礎硬體邏輯群組。 當您在可用性設定組中建立 VM 時，Azure 平台會自動將您的 VM 分散於這些更新網域。 此方法可確保當 Azure 平台進行定期維護時，您的應用程式至少有一個執行個體一直保持執行中。 重新啟動的更新網域順序可能不會在規劃的維護事件期間循序進行，而只會一次重新啟動一個更新網域。

### <a name="managed-disk-fault-domains"></a>受控磁碟容錯網域
若 VM 使用 [Azure 受控磁碟](../articles/virtual-machines/windows/faq-for-disks.md)，VM 會在使用受管理的可用性設定組時配合使用受控磁碟容錯網域。 此一配合行為可確保連接到 VM 的所有受控磁碟都位於相同的受控磁碟容錯網域。 在受管理的可用性設定組中只能建立使用受控磁碟的 VM。 受控磁碟容錯網域數目會依區域而異，每個區域會有兩個或三個受控磁碟容錯網域。

![受控磁碟 FD](./media/virtual-machines-common-manage-availability/md-fd.png)

> [!IMPORTANT]
> 受控可用性設定組的容錯網域數目會依區域而異，每個區域會有兩個或三個。 下表顯示每個區域擁有的數目：

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

## <a name="next-steps"></a>後續步驟
您現在可以開始使用這些可用性和備援功能來建置 Azure 環境。 如需最佳作法資訊，請參閱 [Azure 可用性最佳作法](../articles/best-practices-availability-checklist.md)。

