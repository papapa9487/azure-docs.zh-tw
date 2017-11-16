## <a name="test-the-function"></a>測試函式

在 Mac 或 Linux 電腦上使用 cURL 來測試已部署的函式，在 Windows 上則請使用 Bash。 執行下列 cURL 命令時，但請將其中的 `<app_name>` 預留位置改為函式應用程式的名稱。 將查詢字串 `&name=<yourname>` 附加至 URL。

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![瀏覽器顯示的函式回應。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

如果您的命令列無法使用 cURL，在網頁瀏覽器的位址中輸入相同 URL 即可。 同樣地，請將 `<app_name>` 預留位置改為函式應用程式的名稱，然後對 URL 附加查詢字串 `&name=<yourname>` 並執行要求。 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![瀏覽器顯示的函式回應。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
