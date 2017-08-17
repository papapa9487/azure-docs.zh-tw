使用 [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git) 命令設定 Web 應用程式的本機 Git 部署。

App Service 支援數種將內容部署至 Web 應用程式的方式，例如 FTP、本機 Git、GitHub、Visual Studio Team Services 和 Bitbucket。 針對這個快速入門，您要使用本機 Git 來進行部署。 這表示您要使用 Git 命令來進行部署，從本機存放庫推送到 Azure 中的存放庫。 

在下列命令中，使用您 Web 應用程式的名稱取代 *\<app_name>*。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

輸出具備下列格式：

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

`<username>` 是您在上一個步驟中建立的[部署使用者](#configure-a-deployment-user)。

複製顯示的 URI；您在下一個步驟中將會用到它。
