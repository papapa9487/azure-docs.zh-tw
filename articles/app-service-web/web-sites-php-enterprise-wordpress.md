---
title: "Azure 上的企業級 WordPress | Microsoft Docs"
description: "了解如何在 Azure App Service 上裝載企業級 WordPress 網站"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 22d68588-2511-4600-8527-c518fede8978
ms.service: app-service-web
ms.devlang: php
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 21281955458a2632d96a91d884cab13803f4d296
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="enterprise-class-wordpress-on-azure"></a>Azure 上的企業級 WordPress
Azure App Service 針對關鍵的大規模 [WordPress][wordpress] 網站，提供可調整、安全又容易使用的環境。 Microsoft 本身經營企業級網站，例如 [Office][officeblog] 和 [Bing][bingblog] 部落格。 本文將說明如何使用 Microsoft Azure App Service 的 Web Apps 功能來建立與維護可處理大量訪客的企業級雲端型 WordPress 網站。

## <a name="architecture-and-planning"></a>架構與規劃
基本 WordPress 安裝僅有兩項需求：

* **MySQL 資料庫**：本需求可透過 [Azure Marketplace 中的 ClearDB][cdbnstore] 取得。 或者，您可以使用 [Windows][mysqlwindows] 或 [Linux][mysqllinux]，自行管理 Azure 虛擬機器上的 MySQL 安裝。

  > [!NOTE]
  > ClearDB 提供數種 MySQL 組態。 每個組態具有不同的效能特性。 如需 Azure 市集所提供產品的相關資訊，請參閱 [Azure 市集][cdbnstore]，或直接參考 [ClearDB 網站](http://www.cleardb.com/pricing.view)。
  >
  >
* **PHP 5.2.4 或更高版本**：Azure App Service 目前提供 [PHP 5.4、5.5 和 5.6 版][phpwebsite]。

  > [!NOTE]
  > 建議您一律執行最新版本的 PHP，以便擁有最新的安全性問題修正。
  >
  >

### <a name="basic-deployment"></a>基本部署
如果您只使用基本需求，可在 Azure 區域內建立基本的解決方案。

![Azure Web 應用程式與 MySQL 資料庫裝載於單一 Azure 區域中][basic-diagram]

雖然這樣能讓您建立多個網站的 Web Apps 執行個體來相應放大您的應用程式，但是所有項目都會託管於特定地理區域的資料中心。 此區域以外的訪客在使用網站時，回應可能較為緩慢。 如果此區域中的資料中心停機，您的應用程式也會隨之停止執行。

### <a name="multi-region-deployment"></a>多重區域部署
您可以使用 Azure [流量管理員][trafficmanager]，在多個地理區域之間調整您的 WordPress 網站，並為所有訪客提供相同的 URL。 所有透過流量管理員進入的訪客，之後會根據負載平衡組態，路由傳送至某個區域。

![裝載於多個區域中的 Azure Web 應用程式，使用 CDBR 高可用性路由器，跨區域路由傳送至 MySQL][multi-region-diagram]

在每個區域中，仍會跨多個 Web Apps 執行個體調整 WordPress 網站，但這是特定區域內的調整。 高流量區域和低流量區域的調整可能會有所不同。

若要複寫並將流量路由傳送至多個 MySQL 資料庫，您可以使用 [ClearDB 高可用性路由器 (CDBR)][cleardbscale] (如左側所示) 或 [MySQL Cluster Carrier Grade Edition (CGE)][cge]。

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>包含媒體儲存體和快取的多重區域部署
如果網站接受上傳或託管媒體檔案，請使用 Azure Blob 儲存體。 如果您需要快取，請考慮 [Redis cache][rediscache]、[Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/)、[MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) 或 [Azure 市集](http://azure.microsoft.com/gallery/store/)中的其中一個快取產品。

![裝載於多個區域中的 Azure Web 應用程式，搭配受管理的快取、Blob 儲存體和內容傳遞網路，為 MySQL 使用 CDBR 高可用性路由器][performance-diagram]

依預設，Blob 儲存體會在不同區域中異地發佈，因此您無需擔心在所有網站中複寫檔案。 您也可以針對 Blob 儲存體啟用 Azure [內容傳遞網路][cdn]，這會將檔案分散到接近您訪客的結束節點。

### <a name="planning"></a>規劃
#### <a name="additional-requirements"></a>其他需求
| 作法... | 目的... |
| --- | --- |
| **上傳或儲存大型檔案** |[使用 Blob 儲存體的 WordPress 外掛程式][storageplugin] |
| **傳送電子郵件** |[SendGrid][storesendgrid] 和[使用 SendGrid 的 WordPress 外掛程式][sendgridplugin] |
| **自訂網域名稱** |[在 Azure App Service 中設定自訂網域名稱][customdomain] |
| **HTTPS** |[針對 Azure App Service 中的 Web 應用程式啟用 HTTPS][httpscustomdomain] |
| **生產前驗證** |[針對 Azure App Service 中的 Web 應用程式設定預備環境][staging] <p>當您將 Web 應用程式從預備環境移至生產環境時，您也會移動 WordPress 組態。 在您將預備應用程式移至生產環境之前，請確定所有設定都已針對您的生產環境應用程式的需求更新。</p> |
| **監視與疑難排解** |[針對 Azure App Service 中的 Web 應用程式啟用診斷記錄功能][log]及[監視 Azure App Service 中的 Web Apps][monitor] |
| **部署您的網站** |[在 Azure App Service 中部署 Web 應用程式][deploy] |

#### <a name="availability-and-disaster-recovery"></a>可用性和災難復原
| 作法... | 目的... |
| --- | --- |
| **負載平衡網站**或**異地發佈網站** |[使用 Azure 流量管理員路由傳送流量][trafficmanager] |
| **備份與還原** |[備份 Azure App Service 中的 Web 應用程式][backup]及[還原 Azure App Service 中的 Web 應用程式][restore] |

#### <a name="performance"></a>效能
雲端效能主要是透過快取和相應放大來達成。不過，也應該要考量 Web Apps 主機的記憶體、頻寬與其他屬性。

| 作法... | 目的... |
| --- | --- |
| **了解 App Service 執行個體功能** |[定價詳細資料，包括 App Service 層的功能][websitepricing] |
| **快取資源** |[Redis cache][rediscache]、[Memcache Cloud](/gallery/store/garantiadata/memcached/)、[MemCachier](/gallery/store/memcachier/memcachier/) 或 [Azure 市集](/gallery/store/)中的其中一個快取產品 |
| **調整您的應用程式** |[調整 Azure App Service 中的 Web 應用程式][websitescale]及 [ClearDB 高可用性路由][cleardbscale]。 如果您選擇主控與管理自己的 MySQL 安裝，您應考量可相應放大的 [MySQL Cluster CGE][cge]。 |

#### <a name="migration"></a>移轉
將現有的 WordPress 網站移轉成 Azure App Service 的方法有兩種：

* **[WordPress 匯出][export]**：此方法會匯出您部落格的內容。 您可以接著將內容匯入 Azure App Service 上新的 WordPress 網站，方法是使用 [WordPress Importer 外掛程式][import]。

  > [!NOTE]
  > 此程序可讓您移轉內容，但它無法移轉任何外掛程式、主題或其他自訂。 您必須手動重新安裝這些元件。
  >
  >
* **手動移轉**：[備份您的網站][wordpressbackup]與[資料庫][wordpressdbbackup]，然後手動將它還原到 Azure App Service 中的 Web 應用程式，並與 MySQL 資料庫相關聯。 此方法可用來移轉高度自訂的網站，因為它避免了冗長的手動安裝外掛程式、主題和其他自訂。

## <a name="step-by-step-instructions"></a>逐步指示
### <a name="create-a-wordpress-site"></a>建立 WordPress 網站
1. 使用 [Azure Marketplace][cdbnstore]，在您將主控網站的區域 (或多個區域) 中建立 MySQL 資料庫，其大小為您在[架構與規劃](#planning)一節中所識別的大小。
2. 請依照[在 Azure App Service 中建立 WordPress Web 應用程式][createwordpress]中的步驟，建立 WordPress Web 應用程式。 當您建立 Web 應用程式時，請選取 [使用現有的 MySQL 資料庫]，然後選取您在步驟 1 中建立的資料庫。

如果您打算移轉現有的 WordPress 網站，在建立新的 Web 應用程式後，請參閱[將現有的 WordPress 網站移轉至 Azure](#Migrate-an-existing-WordPress-site-to-Azure)。

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>將現有的 WordPress 網站移轉至 Azure
如[架構與規劃](#planning)一節所提到的，移轉 WordPress 網站的方法有兩種：

* 對於沒有太多自訂或者您只想要移動內容的網站，請**使用匯出和匯入**。
* 對於具有許多自訂，並且要移動所有項目的網站，請**使用備份與還原**。

使用下列其中一個區段來移轉網站。

#### <a name="the-export-and-import-method"></a>匯出與匯入方法
1. 使用 [WordPress 匯出][export]來匯出您的現有網站。
2. 使用[建立 WordPress 網站](#Create-a-new-WordPress-site)一節中的步驟建立 Web 應用程式。
3. 在 [Azure 入口網站][mgmtportal]上登入您的 WordPress 網站，然後按一下 [外掛程式] > [新增]。 搜尋與安裝 [WordPress Importer] 外掛程式。
4. 在您安裝 WordPress Importer 外掛程式之後，按一下 [工具] > [匯入]，然後按一下 **WordPress** 以使用 WordPress Importer 外掛程式。
5. 在 [Import WordPress] 頁面上，按一下 [選擇檔案]。 尋找從您現有 WordPress 網站匯出的 WXR 檔案，然後按一下 [上傳檔案和匯入]。
6. 按一下 [提交] 。 系統將提示您已成功匯入。
7. 完成所有這些步驟之後，在 [Azure 入口網站][mgmtportal]中，從 [應用程式服務] 刀鋒視窗重新啟動您的網站。

匯入網站後，您可能需要執行下列步驟，以啟用不在匯入檔案中的設定。

| 如果使用... | 執行此動作... |
| --- | --- |
| **固定連結** |從新網站的 WordPress 儀表板中，依序按一下 [設定] > [固定連結]，然後更新固定連結結構。 |
| **影像/媒體連結** |如果要更新新位置的連結，請使用 [Velvet Blues Update URLs 外掛程式][velvet] (此為搜尋和取代工具)，或手動在資料庫中更新。 |
| **佈景主題** |移至 [外觀] > [佈景主題]，然後視需要更新網站佈景主題。 |
| **功能表** |如果您的主題支援功能表，前往首頁的連結可能仍然內嵌舊的子目錄。 移至 [外觀] > [功能表]，然後加以更新。 |

#### <a name="the-backup-and-restore-method"></a>備份與還原方法
1. 使用 [WordPress 備份][wordpressbackup]上的資訊，以備份現有的 WordPress 網站。
2. 使用[備份您的資料庫][wordpressdbbackup]上的資訊，以備份現有的資料庫。
3. 建立資料庫並還原備份。

   1. 從 [Azure Marketplace][cdbnstore] 中購買新的資料庫，或在 [Windows][mysqlwindows] 或 [Linux][mysqllinux] 虛擬機器上設定 MySQL 資料庫。
   2. 使用 MySQL 用戶端 (如 [MySQL Workbench][workbench]) 連線到新的資料庫，並匯入您的 WordPress 資料庫。
   3. 更新資料庫以將網域項目變更為新的 Azure App Service 網域，例如 mywordpress.azurewebsites.net。 使用[搜尋與取代 WordPress 資料庫指令碼][searchandreplace]，以安全的方式變更所有執行個體。
4. 在 Azure 入口網站中建立 Web 應用程式，並發佈 WordPress 備份。

   1. 若要建立具有資料庫的 Web 應用程式，請在 [Azure 入口網站][mgmtportal]中，按一下 [新增] > [Web + 行動] > [Azure Marketplace] > [Web Apps] > [Web 應用程式 + SQL] \(或者 [Web 應用程式 + MySQL]) > [建立]。 設定所有必要的設定，來建立空的 Web 應用程式。
   2. 在您的 WordPress 備份中，尋找 **wp-config.php** 檔案，並在編輯器中開啟該檔案。 使用新 MySQL 資料庫的資訊來取代下列項目：

      * **DB_NAME**：資料庫的使用者名稱。
      * **DB_USER**：用來存取資料庫的使用者名稱。
      * **DB_PASSWORD**：使用者密碼。

        變更這些項目之後，請儲存並關閉 **wp-config.php** 檔案。
   3. 使用[在 Azure App Service 中部署 Web 應用程式][deploy]中的資訊，啟用您想要使用的部署方法，然後將 WordPress 備份部署到 Azure App Service 中您的 Web 應用程式。
5. 在部署 WordPress 網站後，您應能夠使用網站的 *.azurewebsite.net URL 來存取新網站 (當做 App Service Web 應用程式)。

### <a name="configure-your-site"></a>設定網站
在建立或移轉 WordPress 網站後，請使用下列資訊以提升效能或啟用其他功能。

| 作法... | 目的... |
| --- | --- |
| **設定 App Service 計劃模式、大小，以及啟用調整規模** |[在 Azure App Service 中調整 Web 應用程式規模][websitescale]。 |
| **啟用持續資料庫連線** |依預設，WordPress 不會使用持續資料庫連線，因為在多個連線後，此選項會造成資料庫連線進入流速控制狀態。 若要啟用持續連線，請安裝[持續連線配接器外掛程式 (英文)](https://wordpress.org/plugins/persistent-database-connection-updater/installation/)。 |
| **提升效能** |<ul><li><p><a href="https://azure.microsoft.com/en-us/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/">停用 ARR Cookie</a>，這可以在 WordPress 於多個 Web Apps 執行個體上執行時改善效能。</p></li><li><p>啟用快取。 您可以使用 <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis cache</a> (預覽版) 搭配 <a href="https://wordpress.org/plugins/redis-object-cache/">Redis 物件快取 WordPress 外掛程式</a>，或者您可以使用 <a href="/gallery/store/">Azure 市集</a>的其中一個快取產品。</p></li><li><p>[利用 Wincache 讓 WordPress 變得更快](https://wordpress.org/plugins/w3-total-cache/)。 Web 應用程式預設已啟用 Wincache。 搭配使用 WinCache 與動態快取時，請關閉 WinCache 的檔案快取，但保留啟用使用者和工作階段快取。 若要關閉檔案快取，請在系統層級的 .ini 檔案中，設定下列值：<br/><code>wincache.fcenabled = 0</code></p></li><li><p>[在 Azure App Service 中調整 Web 應用程式規模][websitescale]並使用 <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB 高可用性路由</a>或 <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a>。</p></li></ul> |
| **使用 Blob 進行儲存** |<ol><li><p>[建立 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)。</p></li><li><p>了解如何[使用內容發佈網路 (CDN)](../cdn/cdn-create-new-endpoint.md) 來異地發佈儲存在 Blob 中的資料。</p></li><li><p>安裝和設定 <a href="https://wordpress.org/plugins/windows-azure-storage/">WordPress 外掛程式的 Azure 儲存體</a>。</p><p>如需此外掛程式的詳細安裝和設定資訊，請參閱<a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">使用者指南</a>。</p> </li></ol> |
| **啟用電子郵件** |使用 Azure 市集來啟用 <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a>。 安裝 WordPress 的 <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">SendGrid 外掛程式</a>。 |
| **設定自訂網域名稱** |[在 Azure App Service 中設定自訂網域名稱][customdomain]。 |
| **啟用自訂網域名稱的 HTTPS** |[針對 Azure App Service 中的 Web 應用程式啟用 HTTPS][httpscustomdomain]。 |
| **負載平衡或異地發佈您的網站** |[使用 Azure 流量管理員路由傳送流量][trafficmanager]。 如果您打算使用自訂網域，請參閱[在 Azure App Service 中設定自訂網域名稱][customdomain]，以取得搭配自訂網域名稱使用流量管理員的相關資訊。 |
| **啟用自動備份** |[在 Azure App Service 中備份 Web 應用程式][backup]。 |
| **啟用診斷記錄** |[在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能][log]。 |

## <a name="next-steps"></a>後續步驟
* [WordPress 最佳化 (英文)](http://codex.wordpress.org/WordPress_Optimization)
* [在 Azure App Service 中將 WordPress 轉換成多站台](web-sites-php-convert-wordpress-multisite.md)
* [適用於 Azure 的 ClearDB 升級精靈 (英文)](http://www.cleardb.com/store/azure/upgrade)
* [將 WordPress 裝載到 Azure App Service 中 Web 應用程式的子資料夾中 (英文)](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)
* [逐步解說：使用 Azure 建立 WordPress 網站](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)
* [在 Azure 上裝載現有的 WordPress 部落格 (英文)](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)
* [在 WordPress 中啟用美化的固定連結 (英文)](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)
* [如何在 Azure App Service 上移轉與執行 WordPress 部落格 (英文)](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)
* [如何在 Azure App Service 上免費執行 WordPress (英文)](http://architects.dzone.com/articles/how-run-wordpress-azure)
* [在兩分鐘內完成在 Azure 上的 WordPress (英文)](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)
* [WordPress 部落格移至 Azure - 第 1 部：在 Azure 上建立 WordPress 部落格 (英文)](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)
* [WordPress 部落格移至 Azure - 第 2 部：傳輸內容 (英文)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)
* [WordPress 部落格移至 Azure - 第 3 部：設定自訂網域 (英文)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)
* [WordPress 部落格移至 Azure - 第 4 部：美化永固定結與 URL 重新寫入規則 (英文)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)
* [將 WordPress 部落格移至 Azure - 第 5 部分：從子資料夾移至根目錄 (英文)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)
* [如何在 Azure 帳戶中設定 WordPress Web 應用程式 (英文)](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)
* [在 Azure 上維持 WordPress (英文)](http://www.johnpapa.net/wordpress-on-azure/)
* [在 Azure 上的 WordPress 秘訣 (英文)](http://www.johnpapa.net/azurecleardbmysql/)

> [!NOTE]
> 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](https://azure.microsoft.com/try/app-service/)，即可在 App Service 中立即建立短期的入門 Web 應用程式。 不需要信用卡，無需承諾。
>
>

## <a name="whats-changed"></a>變更的項目
如需從網站變更為 App Service 的指南，請參閱 [Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)。

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: app-service-web-tutorial-custom-domain.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: app-service-web-tutorial-custom-ssl.md
[mysqlwindows]:../virtual-machines/windows/classic/mysql-2008r2.md
[mysqllinux]:../virtual-machines/linux/classic/mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: /powershell/azureps-cmdlets-docs
[Azure CLI]:../cli-install-nodejs.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md

