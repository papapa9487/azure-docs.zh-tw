不會立即偵測及複寫使用 Azure 入口網站、SMB 或 REST 對 Azure 檔案共用所做的變更，和伺服器端點的變更不一樣。 由於 Azure 檔案還沒有變更通知/日誌功能，因此無法在檔案變更時自動啟動同步工作階段，所以不會立即複寫變更。 在 Windows Server 上，Azure 檔案同步會使用 [Windows USN 日誌](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx)，在檔案變更時自動啟動同步工作階段。

若要偵測 Azure 檔案共用的變更，Azure 檔案同步已排程稱為「變更偵測作業」的作業，其中列舉檔案共用的每個檔案，並比較該檔案的同步版本。 當變更偵測作業判斷檔案已有變更時，Azure 檔案同步就會起始同步工作階段。 變更偵測作業會每隔 24 小時起始。 由於變更偵測作業的運作方式是列舉 Azure 檔案共用的每個檔案，因此大命名空間比小命名空間會耗費更長的時間。 這表示對這些命名空間而言，每隔 24 小時判斷哪些檔案已變更時，可能會耗費更長的時間。 

在長期運作中，我們想要在 Azure 檔案中新增變更偵測功能，即 Windows Server 上 USN 的類似功能。 投票給 [Azure 檔案 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)，協助我們排定此功能的進一步開發優先順序。