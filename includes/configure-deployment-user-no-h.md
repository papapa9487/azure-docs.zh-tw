在 Cloud Shell 中，使用 [az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) 命令來建立部署認證。 需要部署使用者，才能將 FTP 和本機 Git 部署至 Web 應用程式。 使用者名稱和密碼是帳戶等級。 _它們與您的 Azure 訂用帳戶認證不同。_

在下列範例中，以新的使用者名稱和密碼取代 *\<username>* 和 *\<password>* (包括括弧)。 使用者名稱必須是唯一的。 密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字、符號。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

如果您收到 ` 'Conflict'. Details: 409` 錯誤，請變更使用者名稱。 如果您收到 ` 'Bad Request'. Details: 400` 錯誤，請使用更強的密碼。

您只需建立此部署使用者一次；可以用於所有 Azure 部署。

> [!NOTE]
> 記下使用者名稱和密碼。 您稍後需要它們來部署 Web 應用程式。
>
>
