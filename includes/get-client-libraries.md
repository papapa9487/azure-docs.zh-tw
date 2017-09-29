### <a name="install-via-composer"></a>透過編輯器安裝
1. 在專案的根目錄中建立名為 **composer.json** 的檔案，並新增下列程式碼：
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. 將 **[composer.phar][composer-phar]** 下載到專案根目錄中。
3. 開啟命令提示字元，在專案根目錄中執行下列命令
   
    ```
    php composer.phar install
    ```

或者，移至 GitHub 上的 [Azure 儲存體 PHP 用戶端程式庫][php-sdk-github]來複製原始程式碼。

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
