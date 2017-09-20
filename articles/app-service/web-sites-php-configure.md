---
title: "在 Azure App Service Web Apps 中設定 PHP | Microsoft Docs"
description: "了解如何設定預設的 PHP 安裝，或是在 Azure App Service 中新增適用於 Web Apps 的自訂 PHP 安裝。"
services: app-service
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 52db4d82faa0db07224f48b6600e690e34808e91
ms.lasthandoff: 04/06/2017


---
# <a name="configure-php-in-azure-app-service-web-apps"></a>在 Azure App Service Web Apps 中設定 PHP
## <a name="introduction"></a>簡介
本指南將示範如何為 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)中的 Web Apps 設定內建的 PHP 執行階段、提供自訂的 PHP 執行階段，以及啟用擴充功能。 若要使用 App Service，請註冊 [免費試用]。 若要充分利用本指南，您應該先在 App Service 中建立 PHP Web 應用程式。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>作法：變更內建 PHP 版本
當您建立 App Service Web 應用程式時，預設會安裝 PHP 5.5 並立即可供使用。 若要查看可用的修訂版、其預設組態及啟用的擴充，最好的方法是部署呼叫 [phpinfo()] 函數的指令碼。

PHP 5.6 和 PHP 7.0 版本同樣可供使用，但預設並未啟用。 若要更新 PHP 版本，請遵循下列方法其中之一：

### <a name="azure-portal"></a>Azure 入口網站
1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的 Web 應用程式，然後按一下 [設定] 按鈕。
   
    ![Web 應用程式設定][settings-button]
2. 從 [設定] 刀鋒視窗中，選取 [應用程式設定]，並選擇新的 PHP 版本。
   
    ![應用程式設定][application-settings]
3. 按一下 [Web 應用程式設定] 刀鋒視窗頂端的 [儲存] 按鈕。
   
    ![儲存組態設定。][save-button]

### <a name="azure-powershell-windows"></a>Azure PowerShell (Windows)
1. 開啟 Azure PowerShell 並登入您的帳戶。
   
        PS C:\> Login-AzureRmAccount
2. 設定 Web 應用程式的 PHP 版本。
   
        PS C:\> Set-AzureWebsite -PhpVersion {5.5 | 5.6 | 7.0} -Name {app-name}
3. PHP 版本現在已設定完成。 您可確認這些設定：
   
        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-command-line-interface-linux-mac-windows"></a>Azure 命令列介面 (Linux、Mac、Windows)
若要使用 Azure 命令列介面，您必須在電腦上安裝 **Node.js** 。

1. 開啟 [終端機]，然後登入您的帳戶。
   
        azure login
2. 設定 Web 應用程式的 PHP 版本。
   
        azure site set --php-version {5.5 | 5.6 | 7.0} {app-name}

3. PHP 版本現在已設定完成。 您可確認這些設定：
   
        azure site show {app-name}

> [!NOTE] 
> 等同於上述程式碼的 [Azure CLI 2.0](https://github.com/Azure/azure-cli) 命令為︰
>
>

    az login
    az appservice web config update --php-version {5.5 | 5.6 | 7.0} -g {resource-group-name} -n {app-name}
    az appservice web config show -g {resource-group-name} -n {app-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>作法：變更內建 PHP 組態
對於任一個內建的 PHP 執行階段，您可以遵循下列步驟，來變更任何組態選項。 (如需 php.ini 指示詞的資訊，請參閱 [php.ini 指示詞的清單](英文))。

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>變更 PHP\_INI\_USER、PHP\_INI\_PERDIR、PHP\_INI\_ALL 組態設定
1. 將 [.user.ini] 檔案新增至根目錄。
2. 使用在 `php.ini` 檔案中使用的相同語法，將組態設定新增至 `.user.ini` 檔案。 例如，如果您要啟動 `display_errors` 設定，並將 `upload_max_filesize` 設定設為 10M，則 `.user.ini` 檔案將包含下列文字：
   
        ; Example Settings
        display_errors=On
        upload_max_filesize=10M
   
        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
3. 部署 Web 應用程式。
4. 重新啟動 Web 應用程式。 (必須重新啟動，因為 PHP 讀取 `.user.ini` 檔案的頻率是由 `user_ini.cache_ttl` 設定所控制，這是系統層級設定，預設為 300 秒 (5 分鐘)。 重新啟動 Web 應用程式將強制 PHP 讀取 `.user.ini` 檔案中的新設定。)

除了使用 `.user.ini` 檔案之外，您也可以在指令碼中使用 [ini_set()] 函數，設定非系統層級指示詞的組態選項。

### <a name="changing-phpinisystem-configuration-settings"></a>變更 PHP\_INI\_SYSTEM 組態設定
1. 使用機碼 `PHP_INI_SCAN_DIR` 和值 `d:\home\site\ini` 將應用程式設定新增至 Web 應用程式
2. 在 `d:\home\site\ini` 目錄中使用 Kudo 主控台 (http://&lt;site-name&gt;.scm.azurewebsite.net) 建立 `settings.ini` 檔案。
3. 使用在 php.ini 檔案中使用的相同語法，將組態設定新增至 `settings.ini` 檔案。 例如，如果要將 `curl.cainfo` 設定指向 `*.crt` 檔案並將 'wincache.maxfilesize' 設定設定為 512K，您的 `settings.ini` 檔案應該包含以下文字：
   
        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
4. 重新啟動 Web 應用程式以載入變更。

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>作法：在預設 PHP 執行階段中啟用擴充
如同上一個小節所述，若要查看預設 PHP 版本、其預設組態及啟用的擴充，最好的方法是部署呼叫 [phpinfo()]函數的指令碼。 若要啟用擴充，請依照下列步驟執行：

### <a name="configure-via-ini-settings"></a>透過 ini 設定來設定
1. 將 `ext` 目錄新增至 `d:\home\site` 目錄。
2. 將 `.dll` 擴充檔放入 `ext` 目錄中 (例如，`php_xdebug.dll`)。 請確定擴充功能與預設的 PHP 版本相容，並且與 VC9 及非執行緒安全 (nts) 相容。
3. 使用機碼 `PHP_INI_SCAN_DIR` 和值 `d:\home\site\ini` 將應用程式設定新增至 Web 應用程式
4. 在名為 `extensions.ini` 的 `d:\home\site\ini` 中建立 `ini` 檔案。
5. 使用在 php.ini 檔案中使用的相同語法，將組態設定新增至 `extensions.ini` 檔案。 例如，如果您要啟用 MongoDB 和 XDebug 擴充，您的 `extensions.ini` 檔案應該包含以下文字：
   
        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
6. 重新啟動 Web 應用程式以載入變更。

### <a name="configure-via-app-setting"></a>透過應用程式設定來設定
1. 將 `bin` 目錄新增至根目錄。
2. 將 `.dll` 擴充檔放入 `bin` 目錄中 (例如，`php_xdebug.dll`)。 請確定擴充功能與預設的 PHP 版本相容，並且與 VC9 及非執行緒安全 (nts) 相容。
3. 部署 Web 應用程式。
4. 在 Azure 入口網站中瀏覽至您的 Web 應用程式，然後按一下 [設定]  按鈕。
   
    ![Web 應用程式設定][settings-button]
5. 從 [設定] 刀鋒視窗中選取 [應用程式設定]，然後捲動至 [應用程式設定] 區段。
6. 在 [應用程式設定] 區段中，建立 **PHP_EXTENSIONS** 索引鍵。 此索引鍵的值是相對於網站根目錄的路徑：**bin\your-ext-file**。
   
    ![啟用應用程式設定中的擴充][php-extensions]
7. 按一下 [Web 應用程式設定] 刀鋒視窗頂端的 [儲存] 按鈕。
   
    ![儲存組態設定。][save-button]

Zend 擴充功能也支援使用 **PHP_ZENDEXTENSIONS** 索引鍵。 若要啟用多個擴充功能，請針對應用程式設定值包含以逗號分隔的 `.dll` 檔案清單。

## <a name="how-to-use-a-custom-php-runtime"></a>做法：使用自訂 PHP 執行階段
除了預設的 PHP 執行階段之外，App Service Web Apps 也可以使用您提供的 PHP 執行階段來執行 PHP 指令碼。 您提供的執行階段可以由也是您提供的 `php.ini` 檔案加以設定。 若要使用自訂 PHP 執行階段搭配 Web Apps，請依照下列步驟執行。

1. 取得 PHP for Windows 的非安全執行緒 VC9 或 VC11 相容版本。 可以在下列網址找到最新版 PHP for Windows： [http://windows.php.net/download/]。 在下列封存中可以找到舊版： [http://windows.php.net/downloads/releases/archives/]。
2. 為您的執行階段修改 `php.ini` 檔案。 請注意，Web Apps 將忽略僅系統層級指示詞的任何組態設定 (如需僅系統層級指示詞的資訊，請參閱 [php.ini 指示詞的清單] (英文))。
3. 或者，將擴充功能新增至 PHP 執行階段，並且在 `php.ini` 檔案中啟用這些擴充功能。
4. 將 `bin` 目錄新增至根目錄，並在其中放入包含 PHP 執行階段的目錄 (例如，`bin\php`)。
5. 部署 Web 應用程式。
6. 在 Azure 入口網站中瀏覽至您的 Web 應用程式，然後按一下 [設定]  按鈕。
   
    ![Web 應用程式設定][settings-button]
7. 從 [設定] 刀鋒視窗中選取 [應用程式設定]，然後捲動至 [處理常式對應] 區段。 將 `*.php` 新增至 [副檔名] 欄位，然後將路徑加入 `php-cgi.exe` 可執行檔。 如果您將 PHP 執行階段放入應用程式根目錄內的 `bin` 目錄中，該路徑將是 `D:\home\site\wwwroot\bin\php\php-cgi.exe`。
   
    ![指定處理常式對應中的處理常式][handler-mappings]
8. 按一下 [Web 應用程式設定] 刀鋒視窗頂端的 [儲存] 按鈕。
   
    ![儲存組態設定。][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>做法︰在 Azure 中啟用編輯器自動化
App Service 預設不會對 composer.json (如果您 PHP 專案中有的話) 執行任何操作。 如果您使用 [Git 部署](app-service-deploy-local-git.md)，您可以透過啟用「編輯器」擴充功能，在 `git push` 期間啟用 composer.json 處理。

> [!NOTE]
> 您可以 [在這裡投票選擇 App Service 中的頂級編輯器支援](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)！
> 
> 

1. 在 [Azure 入口網站](https://portal.azure.com)的 PHP Web 應用程式刀鋒視窗中，按一下 [工具] > [擴充功能]。
   
    ![可在 Azure 中啟用「編輯器」自動化的「Azure 入口網站」設定刀鋒視窗](./media/web-sites-php-configure/composer-extension-settings.png)
2. 按一下 [新增]，然後按一下 [編輯器]。
   
    ![新增「編輯器」擴充功能以在 Azure 中啟用「編輯器」自動化](./media/web-sites-php-configure/composer-extension-add.png)
3. 按一下 [確定]  以接受法律條款。 再按一次 [確定]  以新增擴充功能。
   
    [已安裝的擴充功能]  刀鋒視窗現在將顯示編輯器擴充功能。  
    ![接受法律條款以在 Azure 中啟用「編輯器」自動化](./media/web-sites-php-configure/composer-extension-view.png)
4. 現在，和上一節一樣執行 `git add`、`git commit` 和 `git push`。 您就會立即看到編輯器正在安裝 composer.json 中定義的相依性。
   
    ![使用 Azure 中的「編輯器」自動化來進行 Git 部署](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱 [PHP 開發人員中心](/develop/php/)。

> [!NOTE]
> 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](https://azure.microsoft.com/try/app-service/)，即可在 App Service 中立即建立短期入門 Web 應用程式。 不需要信用卡；無需承諾。
> 
> 

[免費試用]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[php.ini 指示詞的清單]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png


