## <a name="deploy-template-from-cloud-shell"></a>從 Cloud Shell 部署範本

您可以使用 [Cloud Shell](../articles/cloud-shell/overview.md) 來部署您的範本。 不過，您必須先將範本載入 Cloud Shell 的檔案共用中。 如果您尚未使用 Cloud Shell，請參閱 [Azure Cloud Shell 概觀](../articles/cloud-shell/overview.md)以取得如何設定的相關資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取您的 Cloud Shell 資源群組。 名稱模式為 `cloud-shell-storage-<region>`。

   ![選取資源群組](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. 選取 Cloud Shell 的儲存體帳戶。

   ![選取儲存體帳戶](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. 選取 [檔案]。

   ![選取檔案](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. 選取 Cloud Shell 的檔案共用。 名稱模式為 `cs-<user>-<domain>-com-<uniqueGuid>`。

   ![選取檔案共用](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. 選取 [新增目錄]。

   ![新增目錄](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. 將它命名為 **templates**，然後選取 [確定]。

   ![命名目錄](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. 選取您的新目錄。

   ![選取目錄](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. 選取 [上傳] 。

   ![選取上傳](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. 尋找並上傳您的範本。

   ![上傳檔案](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. 開啟提示字元。

   ![開啟 Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
