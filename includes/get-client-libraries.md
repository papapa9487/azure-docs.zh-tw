### <a name="install-via-composer"></a>透過編輯器安裝
1. [安裝 Git][install-git]。 請注意在 Windows 中，您也必須將 Git 可執行檔新增至 PATH 環境變數。 
2. 在專案的根目錄中建立名為 **composer.json** 的檔案，並新增下列程式碼：
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "^0.4"
      }
    }
    ```
3. 將 **[composer.phar][composer-phar]** 下載到專案根目錄中。
4. 開啟命令提示字元，在專案根目錄中執行下列命令
   
    ```
    php composer.phar install
    ```

[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
