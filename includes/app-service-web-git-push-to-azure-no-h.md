在本機終端視窗中，將 Azure 遠端新增至本機 Git 存放庫。 系統會在[建立 Web 應用程式](#create-a-web-app)為您建立此 Azure 遠端。

```bash
git remote add azure <URI from previous step>
```

推送到 Azure 遠端，使用下列命令來部署您的應用程式。 當系統提示輸入密碼時，務必輸入您在[設定部署使用者](#configure-a-deployment-user)中建立的密碼，而不是您用來登入 Azure 入口網站的密碼。

```bash
git push azure master
```

上述命令會顯示類似下列範例的資訊：
