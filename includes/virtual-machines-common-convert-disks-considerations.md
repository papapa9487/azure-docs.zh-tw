
* 轉換需要重新啟動 VM，因此請在預先存在的維護期間排定 VM 移轉。 

* 轉換是無法復原的。 

* 請務必先進行轉換測試。 在生產環境中執行移轉之前，請先移轉測試虛擬機器。

* 在轉換期間，您會解除配置 VM。 在轉換後啟動 VM 時，VM 會接收新的 IP 位址。 如有必要，您可以對 VM [指派靜態 IP 位址](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)。

* VM 在傳換前使用的原始 VHD 和儲存體帳戶不會遭到刪除。 這些項目會繼續產生費用。 若要避免為這些成果支付費用，請在確認轉換完成之後，刪除原始的 VHD Blob。
