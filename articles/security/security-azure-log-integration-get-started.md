---
title: "開始使用 Azure 記錄整合 | Microsoft Docs"
description: "學習如何安裝 Azure 記錄整合服務，和來自 Azure 儲存體、Azure 稽核記錄檔以及 Azure 資訊安全中心警示的整合記錄檔。"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 07/26/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9d39ecd513386b75b4b640721f80991caaf9ade8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>使用 Azure 診斷記錄和 Windows 事件轉送進行 Azure 記錄整合
Azure 記錄整合 (AzLog) 可讓您將來自 Azure 資源的未經處理記錄，整合到內部部署安全性資訊及事件管理 (SIEM) 系統內。 這項整合提供您內部部署或在雲端中所有資產統一的儀表板，以便您彙總、相互關聯、分析和警示與應用程式相關聯的安全性事件。
>[!NOTE]
如需有關 Azure 記錄整合的詳細資訊，您可以檢閱 [Azure 記錄整合概觀](https://docs.microsoft.com/azure/security/security-azure-log-integration-overview)。

本文將著重 Azlog 服務的安裝和使用 Azure 診斷整合服務，協助您開始使用 Azure 記錄整合。 Azure 記錄整合服務便能自部署於 Azure IaaS 的虛擬機器的 Windows 安全性事件頻道中，收集 Windows 事件記錄檔資訊。 這非常類似於您可能已經用於內部部署的「事件轉送」。

>[!NOTE]
>SIEM 本身會將 Azure 記錄整合的輸出帶入 SIEM 中。 如需詳細資訊，請參閱文章[使用內部部署 SIEM 整合 Azure 記錄整合](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)。

明確而言，Azure 記錄整合服務會在使用 Windows Server 2008 R2 或以上作業系統 (Windows Server 2012 R2 或 Windows Server 2016 較佳) 的實體或虛擬電腦上執行。

實體電腦可以在內部或在主機服務提供者的網站上執行。 如果您選擇在虛擬機器上執行 Azure 記錄整合服務，該虛擬機器可位於內部或公用雲端，例如 Microsoft Azure 中。

執行 Azure 記錄整合服務的實體或虛擬機器需要網路連線至 Azure 公用雲端。 本文中的步驟提供組態詳細資料。

## <a name="prerequisites"></a>必要條件
AzLog 安裝至少需要下列項目︰
* **Azure 訂用帳戶**。 如果您沒有訂用帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。
* 可用於 Windows Azure 診斷記錄的**儲存體帳戶** (您可以使用預先設定的儲存體帳戶，或建立一個新帳戶，我們稍後將在本文示範如何設定儲存體帳戶)
  >[!NOTE]
  根據您的情況，可能不需要儲存體帳戶。 而本文所涵蓋的 Azure 診斷案例則需要。
* **兩個系統**︰將執行 Azure 記錄整合服務的電腦，以及將受監視且會傳送記錄資訊到 Azlog 服務機器的電腦。
   * 您想要監視的電腦 – 這是以 [Azure 虛擬機器](../virtual-machines/virtual-machines-windows-overview.md)執行的 VM 
   * 將執行 Azure 記錄整合服務的電腦。此電腦會收集稍後將匯入您的 SIEM 的所有記錄檔資訊。
    * 這個系統可以是內部部署上或在 Microsoft Azure 中。  
    * 系統必須執行 x64 版本的 Windows server 2008 R2 SP1 或更高版本，並安裝 .NET 4.5.1。 您可以參閱標題為[如何判斷安裝的 .NET Framework 版本 (How to: Determine Which .NET Framework Versions Are Installed)](https://msdn.microsoft.com/library/hh925568)，判斷安裝的.NET 版本  
    系統必須能夠連線到用於 Azure 診斷記錄的 Azure 儲存體帳戶。 我們稍後在本文將提供確認連線方式的指示。

如需如何使用 Azure 入口網站建立虛擬機器之程序的快速示範，請觀看下列影片。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]



## <a name="deployment-considerations"></a>部署考量
測試 Azure 記錄整合時，您可以使用任何符合最低作業系統需求的系統。 不過，在生產環境中，負載可能需要您規劃向上或向外擴充。

如果事件磁碟區很大，您可以執行 Azure 記錄整合服務的多個執行個體 (每部實體或虛擬機器執行一個執行個體)。 此外，您可以針對 Windows 的 Azure 診斷儲存體帳戶 (WAD) 進行負載平衡，提供給執行個體的訂用帳戶數目應根據您的容量。
>[!NOTE]
目前我們針對何時向外擴充 Azure 記錄整合電腦的執行個體 (亦即正在執行 Azure 記錄整合服務的電腦)、儲存體帳戶或訂用帳戶並沒有特定建議。 應根據您在每個區域中的效能觀察調整決策。

您也可以擴充 Azure 記錄整合服務，以協助改善效能。 下列效能度量能協助您調整您選擇執行 Azure 記錄整合服務的電腦︰
* 在 8 個處理器 (核心) 的電腦上，單一的 Azlog 整合器每日可以處理大約 2400 萬個事件 (~1 百萬個/小時)。

* 在 4 個處理器 (核心) 的電腦上，單一的 Azlog 整合器每日可以處理大約 1500 萬個事件 (~6 萬 2 千 5 百個/小時)。

## <a name="install-azure-log-integration"></a>安裝 Azure 記錄檔整合
若要安裝 Azure 記錄整合，您必須下載 [Azure 記錄整合](https://www.microsoft.com/download/details.aspx?id=53324)安裝檔案。 執行設定常式，並決定您是否想要將遙測資訊提供給 Microsoft。  

![勾選遙測方塊的安裝畫面](./media/security-azure-log-integration-get-started/telemetry.png)

*
> [!NOTE]
> 我們建議您允許 Microsoft 收集遙測資料。 您可以取消核取此選項，以關閉遙測資料的收集。
>


Azure 記錄整合服務會從其安裝所在的電腦收集遙測資料。  

所收集的遙測資料是︰

* Azure 記錄檔整合的執行期間所發生的例外狀況
* 所處理之查詢和事件數目的相關度量
* 正在使用哪些 Azlog.exe 命令列選項的相關統計資料

下列影片涵蓋安裝程序。
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]



## <a name="post-installation-and-validation-steps"></a>張貼安裝和驗證步驟
完成基本的設定常式之後，您便可以執行後續安裝和驗證步驟︰
1. 開啟提升權限的 PowerShell 視窗並瀏覽至 **c:\Program Files\Microsoft Azure Log Integration**
2. 您必須採取的第一個步驟是匯入 AzLog Cmdlet。 您可以執行指令碼 **LoadAzlogModule.ps1** (請注意下列命令中的「.\」)。 輸入 **.\LoadAzlogModule.ps1** 後按 [ENTER]。  
您應該會看到類似下圖的內容： </br></br>勾選遙測方塊的安裝畫面 
![](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. 現在您需要設定 AzLog，以使用特定的 Azure 環境。 「Azure 環境」是您想要使用的 Azure 雲端資料中心的「類型」。 雖然現在有數個 Azure 環境，目前相關的選項都是 **AzureCloud** 或 **AzureUSGovernment**。   在提高權限的 PowerShell 環境，請確定您是在 **c:\program files\Microsoft Azure Log Integration\** </br></br>
    確定後，執行命令： </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud`` (適用於 Azure 商業)

      >[!NOTE]
      命令成功時，您不會收到任何回應。  如果您想要使用美國政府的 Azure 雲端，您需要使用 **AzureUSGovernment** (適用於-名稱變數) 來使用美國政府雲端。 目前不支援其他 Azure 雲端。  
4. 可以監視系統之前，您需要使用中的儲存體帳戶名稱以進行 Azure 診斷。  在 Azure 入口網站中，瀏覽至**虛擬機器**，並尋找您要監視的虛擬機器。 在**屬性**區段中，選擇 [診斷設定]。  按一下 [代理程式] 並記下指定的儲存體帳戶名稱。 您需要此帳戶的名稱以進行後續步驟。
![Azure 診斷設定](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

      ![Azure 診斷設定](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)
      >[!NOTE]
      如果虛擬機器建立期間未啟用監視，您可以選擇啟用，如上所示。
5. 現在我們回來談 Azure 記錄整合機器。 我們需要確認您已從安裝 Azure 記錄整合系統的位置連線至儲存體帳戶。 執行 Azure 記錄整合服務的實體電腦或虛擬機器需要存取儲存體帳戶，以擷取 Azure 診斷依照每個受監視系統上的設定所記錄的資訊。  
  1. 您可以從[這裡](http://storageexplorer.com/)下載 Azure 儲存體總管。
  2. 執行設定常式
  3. 安裝完成時，按一下 [下一步]，並保持勾選 [啟動 Microsoft Azure 儲存體總管]方塊。  
  4. 登入 Azure。
  5. 請確認您可以看到您設定 Azure 診斷的儲存體帳戶。  
![儲存體帳戶](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
   6. 請注意，儲存體帳戶之下有幾個選項。 其中一個是**資料表**。 在**資料表**下您應該會看到 **WADWindowsEventLogsTable** 。 </br></br>
   ![儲存體帳戶](./media/security-azure-log-integration-get-started/storage-explorer.png) </br>

## <a name="integrate-azure-diagnostic-logging"></a>整合 Azure 診斷記錄
在此步驟中，您將設定執行 Azure 記錄整合服務的電腦，使其連接至包含記錄檔的儲存體帳戶。
若要完成此步驟，我們需要幾個項目。  
* **FriendlyNameForSource：**您可以使用這個好記的名稱，為您已設定虛擬機器用來儲存 Azure 診斷資訊的儲存體帳戶命名。
* **StorageAccountName：**這是設定 Azure 診斷時，您指定的儲存體帳戶的名稱。  
* **StorageKey：**這是為此虛擬機器儲存 Azure 診斷資訊的儲存體帳戶的儲存體金鑰。  

執行下列步驟來取得儲存體金鑰︰
 1. 瀏覽至 [Azure 入口網站](http://portal.azure.com)。
 2. 在 Azure 主控台瀏覽窗格中，捲動至底部，按一下 [更多服務]。

 ![更多服務](./media/security-azure-log-integration-get-started/more-services.png)
 3. 在**篩選**文字方塊中輸入**儲存體**。 按一下 [儲存體帳戶] \(這會在您輸入**儲存體**之後顯示)

   ![篩選方塊](./media/security-azure-log-integration-get-started/filter.png)
 4. 將顯示儲存體帳戶清單，按兩下您指派給記錄儲存體的帳戶。

   ![儲存體帳戶清單](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. 按一下**設定**一節中的 [存取金鑰]。

  ![access keys](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. 複製**key1**並將其放在您可以存取的安全位置，以便下一步進行。

   ![二個存取金鑰](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. 在已安裝 Azure 記錄整合的伺服器上，開啟提升權限的命令提示字元（請注意，此處使用的是提升權限的命令提示字元視窗，而不是提高權限的 PowerShell 主控台）。
 8. 瀏覽至 **c:\Program Files\Microsoft Azure Log Integration**
 9. 執行 ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> `` </br> 例如，``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``如果您想要訂用帳戶識別碼顯示在事件 XML 中，請將訂用帳戶識別碼附加至易記名稱︰``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>``或例如，``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]  
您最多等候 60 分鐘，然後檢視從儲存體帳戶提取的事件。 若要檢視，請在 Azlog 整合器上開啟 [事件檢視器] > [Windows 記錄] > [轉送的事件]。

您可以在這裡觀看上方所涵蓋步驟的影片。
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>要是資料未顯示在 [轉送的事件] 資料夾呢？
如果一個小時之後資料未顯示在 [轉送的事件]  資料夾，然後︰

1. 請檢查執行 Azure 記錄整合服務的電腦，確認電腦可以存取 Azure。 若要測試連線，請嘗試從瀏覽器開啟 [Azure 入口網站](http://portal.azure.com) 。
2. 請確定使用者帳戶 **Azlog** 具有 [users\azlog]  資料夾的寫入權限。
  <ol type="a">
   <li>開啟 [Windows 檔案總管] </li>
  <li> 瀏覽至 **c:\users** </li>
  <li> 以滑鼠右鍵按一下 [c:\users\Azlog] </li>
  <li> 按一下 [安全性]  </li>
  <li> 按一下 [NT Service\Azlog]  並檢查帳戶的權限。 如果帳戶不在此索引標籤中，或適當的權限目前未顯示您可以授與此索引標籤中的帳戶權限。</li>
  </ol>
3. 請確定執行 **Azlog source list** 命令時，會列出 **Azlog source add** 命令中新增的儲存體帳戶。
4. 移至 [事件檢視器] > [Windows 記錄] > [應用程式]，以查看是否有任何來自 Azure 記錄整合的錯誤報告。


如果您在安裝和設定期間遇到任何問題，請開啟[支援要求](../azure-supportability/how-to-create-azure-support-request.md)，選取 [記錄整合]  作為您要求支援的服務。

其他支援選項是 [Azure 記錄整合 MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration)。 社群能針對如何充分利用 Azure 記錄整合，透過分享問題、 解答、 秘訣和技巧支援彼此。 此外，Azure 記錄整合小組會監視這個論壇，並且會盡全力提供協助。

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure 記錄整合，請參閱下列文件：

* [Azure 記錄檔的 Microsoft Azure 記錄整合](https://www.microsoft.com/download/details.aspx?id=53324) – Azure 記錄整合詳細資料、系統需求和安裝指示的下載中心。
* [Azure 記錄整合簡介](security-azure-log-integration-overview.md) – 這份文件為您介紹 Azure 記錄整合、其主要功能以及運作方式。
* [合作夥伴設定步驟](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 此部落格文章說明如何設定 Azure 記錄整合，以搭配使用合作夥伴解決方案 Splunk、HP ArcSight 和 IBM QRadar。 這是我們目前有關如何設定 SIEM 元件的指引。 如需其他詳細資料，請先洽詢您的 SIEM 廠商。
* [Azure 記錄整合常見問題集 (FAQ)](security-azure-log-integration-faq.md) - 此常見問題集會回答有關 Azure 記錄整合的問題。
* [以 Azure 記錄整合來整合資訊安全中心警示](../security-center/security-center-integrating-alerts-with-log-integration.md) - 這份文件說明如何將資訊安全中心警示以及 Azure 診斷和 Azure 活動記錄所收集的虛擬機器安全性事件，與您的 Log Analytics 或 SIEM 方案進行同步處理。
* [Azure 診斷和 Azure 稽核記錄檔的新功能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – 此部落格文章為您介紹 Azure 稽核記錄檔和其他功能，協助您深入了解您的 Azure 資源的作業。
