
下列限制適用於 Azure 備份。

| 限制識別碼 | 預設限制 |
| --- | --- |
| 可針對每個保存庫註冊的伺服器/電腦數目 |如為 Windows Server/用戶端/SCDPM 則有 50 個， <br/> 如為 IaaS VM 則有 200 個 |
| 在 Azure 保存庫儲存體中所儲存資料的資料來源大小 |最大 54400 GB<sup>1</sup> |
| 可以在每個 Azure 訂用帳戶中建立的備份保存庫的數目 |25 (備份保存庫) <br/> 每個區域 25 個復原服務保存庫 |
| 每日可以排程的備份次數 |Windows 伺服器/用戶端每日 3 次 <br/> 對於 SCDPM 每日 2 次 <br/> IaaS VM 每日一次 |
| 連接到 Azure 虛擬機器進行備份的資料磁碟 |16 |
| 連接到 Azure 虛擬機器進行備份的個別資料磁碟大小| 1023 GB <sup>2</sup>|

* <sup>1</sup>54400 GB 上限並不適用於 IaaS VM 備份。
* <sup>2</sup> 我們有[私人預覽](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0)以支援最多 4TB 之未受管理的磁碟。 

