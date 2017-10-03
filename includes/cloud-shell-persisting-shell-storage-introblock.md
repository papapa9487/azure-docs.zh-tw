# <a name="persist-files-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中保存檔案
Cloud Shell 會運用 Azure 檔案儲存體在工作階段間保存檔案。

## <a name="set-up-a-clouddrive-file-share"></a>設定 clouddrive 檔案共用
在初始啟動時，Cloud Shell 會提示您關聯新的或現有的檔案共用，以在工作階段間保存檔案。

> [!NOTE]
> Bash 和 PowerShell 會共用相同的檔案共用。 只有一個檔案共用可以與 Cloud Shell 中的自動掛接相關聯。

### <a name="create-new-storage"></a>建立新的儲存體

當您使用基本設定並只選取訂用帳戶時，Cloud Shell 會代表您在距離您最近的支援區域中建立三個資源：
* 資源群組：`cloud-shell-storage-<region>`
* 儲存體帳戶：`cs<uniqueGuid>`
* 檔案共用：`cs-<user>-<domain>-com-<uniqueGuid>`

![訂用帳戶設定](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

檔案共用會在您的 `$Home` 目錄中掛接為 `clouddrive`。 這是一次性的動作，後續的工作階段會自動掛接檔案共用。 

在 Bash 中，檔案共用也包含 5 GB 的映像，這個為您建立的映像會自動保存 `$Home` 目錄中的資料。 

### <a name="use-existing-resources"></a>使用現有的資源

您可以使用進階選項來建立與現有資源的關聯。 當儲存體設定提示出現時，請選取 [顯示進階設定] 以檢視其他選項。 下拉式功能表會針對您指派的 Cloud Shell 區域以及本地備援儲存體和異地備援儲存體帳戶進行篩選。

在 Bash 中，現有的檔案共用會收到為您建立以便保存 `$Home` 目錄的 5 GB 映像。

![資源群組設定](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>使用 Azure 資源原則限制資源建立
您在 Cloud Shell 中建立的儲存體帳戶都會標記 `ms-resource-usage:azure-cloud-shell`。 如果您想要禁止使用者在 Cloud Shell 中建立儲存體帳戶，請建立這個特定標籤所觸發之[標籤的 Azure 資源原則](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags)。

## <a name="supported-storage-regions"></a>支援的儲存體區域
相關聯的 Azure 儲存體帳戶必須位於與所掛接之目標 Cloud Shell 電腦相同的區域中。

若要尋找您指派的區域，您可以：
* 檢視 [進階儲存體設定] 對話方塊上的附註
* 參考為您建立的儲存體帳戶的名稱 (例如：`cloud-shell-storage-westus`)
* 執行 `env` 並找出 `ACC_LOCATION` 變數

Cloud Shell 機器存在於下列區域：
|領域|區域|
|---|---|
|美洲|美國東部、美國中南部、美國西部|
|歐洲|北歐、西歐|
|亞太地區|印度中部、東南亞|

