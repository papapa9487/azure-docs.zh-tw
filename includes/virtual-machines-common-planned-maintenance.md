為提升虛擬機器之主機基礎結構的可靠性、效能和安全性，Azure 會定期執行更新。 這些更新的範圍，從在主控環境 (像是作業系統、Hypervisor 和各種在主機上部署的代理程式) 中修補軟體元件、升級網路元件，以至硬體解除委任。 這些更新大多數都會在不影響託管虛擬機器的情況下執行。 不過，更新在某些情況下確實會造成影響：

- 如果維護不需要重新開機，Azure 會在主機更新時使用就地移轉來暫停 VM。

- 如果維護需要重新開機，您會在規劃維護時收到通知。 在這些情況下，您也將獲得一個時間範圍，您可以在適合您的時間自行開始維護。

本頁面說明 Microsoft Azure 如何執行這兩種類型的維護。 如需非計劃性事件 (中斷) 的詳細資訊，請參閱〈管理 [Windows] (../articles/virtual-machines/windows/manage-availability.md) 或 [Linux](../articles/virtual-machines/linux/manage-availability.md) 虛擬機器的可用性〉。

在虛擬機器中執行的應用程式可以使用適用於 [Windows](../articles/virtual-machines/windows/instance-metadata-service.md)或 [Linux] (../articles/virtual-machines/linux/instance-metadata-service.md) 的Azure 中繼資料服務，蒐集近期更新的相關資訊。

## <a name="in-place-vm-migration"></a>就地 VM 移轉

當更新不需要完整重新開機時，則會使用就地即時移轉。 在更新期間，虛擬機器會暫停 30 秒並將記憶體保留在 RAM 中，而主控環境會套用必要的更新和修補程式。 虛擬機器會接著繼續執行，而系統會自動同步虛擬機器的時鐘。

對於可用性設定組中的 VM，更新網域會一次更新一個。 在計劃性維護移至下一個 UD 之前，一個更新網域 (UD) 中的所有 VM 會暫停、更新，而後繼續進行。

這些類型的更新會對某些應用程式造成影響。 執行即時事件處理 (如媒體串流處理或轉碼，或高輸送量網路服務案例) 的應用程式，其設計可能不會容許暫停 30 秒。 <!-- sooooo, what should they do? --> 


## <a name="maintenance-requiring-a-reboot"></a>維護需要重新開機

如果 VM 需要針對計劃性維護重新啟動，您會事先收到通知。 計劃性維護有兩個階段：自助式期間和排程維護期間。

**自助式期間**可讓您在您的 VM 上起始維護。 在此期間，您可以查詢每個 VM 來查看其狀態，並檢查上次維護要求的結果。

當您開始自助式維護時，VM 會移至已更新的節點，然後重新開機。 因為 VM 會重新開機，所以暫存磁碟會遺失，而系統會更新與虛擬網路介面關聯的動態 IP 位址。

如果您開始自助式維護且在此過程忠發生錯誤，此作業就會停止，系統不會更新 VM，而且會將它從計劃性維護反覆運算中移除。 稍後有新的排程時會連絡您，讓您有新的機會進行自助式維護。 

當自助式期間過後，**排程的維護期間**隨即開始。 在這段期間內，您仍然可以查詢維護期間，但是無法再自行開始維護。

## <a name="availability-considerations-during-planned-maintenance"></a>規劃維護期間的可用性考量 

如果您決定等到計劃性維護期間，維護 VM 的最高可用性時需考慮下列事項。 

### <a name="paired-regions"></a>配對的區域

每個 Azure 區域都會與相同地理位置內的另一個區域配對，以共同形成區域配對。 在計劃性維護期間，Azure 只會更新區域配對中單一區域的 VM。 舉例來說，Azure 在更新美國中北部的虛擬機器時，就不會同時更新任何在美國中南部的虛擬機器。 不過，像是北歐等其他區域可以和美國東部一同進行維護。 了解區域配對的運作方式可協助您將 VM 更妥善地分散於各區域。 如需詳細資訊，請參閱 [Azure 區域配對](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

### <a name="availability-sets-and-scale-sets"></a>可用性設定組和擴展集

在 Azure VM 上部署工作負載時，您可以在一個可用性設定組內建立 VM，以便為應用程式提供高可用性。 這可確保在中斷或維護事件期間，至少有一部虛擬機器可供使用。

在可用性設定組內，個別 VM 會散佈在多達 20 個更新網域 (UD)。 在規劃維護期間，在任何指定時間只有單一更新網域受影響。 請注意，受影響的更新網域順序不一定會循序發生。 

虛擬機器擴展集是一個可讓您以單一資源的形式，部署和管理一組相同 VM 的 Azure 計算資源。 擴展集會自動部署於更新網域，如可用性設定組中的 VM。 就如同可用性設定組，使用擴展集，在任何指定時間只有單一更新網域受影響。

如需設定虛擬機器以提供高可用性的詳細資訊，請參閱〈管理 Windows (../articles/virtual-machines/windows/manage-availability.md) 或 [Linux](../articles/virtual-machines/linux/manage-availability.md) 虛擬機器的可用性〉。