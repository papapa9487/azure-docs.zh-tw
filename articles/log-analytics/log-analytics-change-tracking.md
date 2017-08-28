---
title: "使用 Azure Log Analytics 追蹤變更 | Microsoft Docs"
description: "Log Analytics 中的「變更追蹤」解決方案可協助您識別您環境中發生的軟體及 Windows 服務變更。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 80fd9ee9b9de5c7547b9f840ac78a60d52153a5a
ms.openlocfilehash: 57af000e47188786a77cdb84ebb6ffb5c50eafaa
ms.contentlocale: zh-tw
ms.lasthandoff: 08/14/2017

---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>使用變更追蹤解決方案來追蹤環境中的軟體變更

![變更追蹤符號](./media/log-analytics-change-tracking/change-tracking-symbol.png)

本文將協助您使用 Log Analytics 中的變更追蹤解決方案，輕鬆地找出您環境中的變更。 這個解決方案會追蹤 Windows 與 Linux 軟體、Windows 檔案和登錄機碼、Windows 服務以及 Linux 精靈的變更。 識別組態變更可協助您找出操作問題。

您需要安裝此方案以更新您已安裝的代理程式類型。 系統會讀取受監視伺服器上對已安裝程式、Windows 服務及 Linux 精靈所做的變更。 之後會將資料傳送至雲端的 Log Analytics 服務處理。 會將邏輯套用至接收的資料，且雲端服務會記錄資料。 使用 [變更追蹤] 儀表板上的資訊，您可以輕鬆地看到您的伺服器基礎結構中所做的變更。

## <a name="installing-and-configuring-the-solution"></a>安裝和設定方案
請使用下列資訊來安裝和設定方案。

* 您要監視變更的每部電腦上都必須有 [Windows](log-analytics-windows-agents.md)、[Operations Manager](log-analytics-om-agents.md) 或 [Linux](log-analytics-linux-agents.md)代理程式。
* 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview) 新增變更追蹤解決方案至您的 OMS 工作區。 也可以運用[從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)一文中提供的資訊來新增解決方案。 不需要進一步設定。

### <a name="configure-linux-files-to-track"></a>設定要追蹤的 Linux 檔案
使用下列步驟，設定要在 Linux 電腦上追蹤的檔案。

1. 在 OMS 入口網站中，按一下 **[設定]** \(齒輪符號)。
2. 在 [設定] 頁面上，按一下 [資料]，然後按一下 [Linux 檔案追蹤]。
3. 在 [Linux 檔案變更追蹤] 下，輸入整個路徑 (包含您要追蹤之檔案的檔案名稱)，然後按一下 [新增] 符號。 例如："/etc/*.conf"
4. 按一下 [儲存] 。  

> [!NOTE]
> Linux 檔案追蹤具有其他功能，包括目錄追蹤、透過目錄遞迴以及萬用字元追蹤。

### <a name="configure-windows-files-to-track"></a>設定要追蹤的 Windows 檔案
使用下列步驟，設定要在 Windows 電腦上追蹤的檔案。

1. 在 OMS 入口網站中，按一下 **[設定]** \(齒輪符號)。
2. 在 **[設定]** 頁面上，按一下 **[資料]**，然後按一下 **[Windows 檔案追蹤]**。
3. 在 [Windows 檔案變更追蹤] 下，輸入整個路徑 (包含您要追蹤之檔案的檔案名稱)，然後按一下 [新增] 符號。 例如：C:\Program Files (x86)\Internet Explorer\iexplore.exe 或 C:\Windows\System32\drivers\etc\hosts。
4. 按一下 [儲存] 。  
   ![Windows 檔案變更追蹤](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="configure-windows-registry-keys-to-track"></a>設定要追蹤的 Windows 登錄機碼
使用下列步驟，設定要在 Windows 電腦上追蹤的登錄機碼。

1. 在 OMS 入口網站中，按一下 **[設定]** \(齒輪符號)。
2. 在 [設定] 頁面上，按一下 [資料]，然後按一下 [Windows 登錄追蹤]。
3. 在 [Windows 登錄變更追蹤] 下方，輸入您要追蹤的整個金鑰，然後按一下 [新增] 符號。
4. 按一下 [儲存] 。  
   ![Windows 登錄變更追蹤](./media/log-analytics-change-tracking/windows-registry-change-tracking.png)

### <a name="explanation-of-linux-file-collection-properties"></a>Linux 檔案收集屬性的說明
1. **類型**
   * **檔案** (報告檔案中繼資料 - 大小、修改日期、雜湊等)
   * **目錄** (報告目錄中繼資料 -大小、修改日期等)
2. **連結** (處理其他檔案或目錄的 Linux 符號連結參考)
   * **忽略** (忽略遞迴期間的符號連結，而不包含參考的檔案/目錄)
   * **遵循** (遵循遞迴期間的符號連結，以同時包含參考的檔案/目錄)
   * **管理** (遵循符號連結並變更所傳回內容的處理方式)

   > [!NOTE]   
   > 不建議選擇「管理」連結選項。 不支援檔案內容擷取。

3. **遞迴** (遞迴所有資料夾層級及追蹤符合路徑陳述式的所有檔案)
4. **Sudo** (能夠存取需要 sudo 權限的檔案或目錄)

### <a name="limitations"></a>限制
變更追蹤解決方案目前不支援下列項目︰

* Windows 檔案追蹤的資料夾 (目錄)
* Windows 檔案追蹤的遞迴
* Windows 檔案追蹤的萬用字元
* 路徑變數
* 網路檔案系統
* 檔案內容

其他限制：

* [最大檔案大小] 資料行和值未用於目前實作中。
* 如果您在 30 分鐘的收集週期收集超過 2500 個檔案，則解決方案效能可能會下降。
* 網路流量高時，變更記錄最多可能需要六個小時來顯示。
* 如果您在關閉電腦時修改組態，電腦可能提出隸屬先前組態的檔案變更。

## <a name="change-tracking-data-collection-details"></a>「變更追蹤」資料收集詳細資訊
變更追蹤會使用您已啟用的代理程式，來收集軟體清查和 Windows 服務中繼資料。

下表顯示變更追蹤的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | Operations Manager 代理程式 | Linux 代理程式 | Azure 儲存體 | 是否需要 Operations Manager？ | 透過管理群組傳送的 Operations Manager 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows 和 Linux | &#8226; | &#8226; | &#8226; |  |  | &#8226; | 視變更類型而定，5 分鐘到 50 分鐘。 如需詳細資訊，請參閱下列表格。 |


下表顯示各變更類型的資料收集頻率。

| **變更類型** | **頻率** | **代理程式****是否會傳送所找到的差異？** |
| --- | --- | --- |
| Windows 登錄 | 50 分鐘 | 否 |
| Windows 檔案 | 30 分鐘 | 是。 如果 24 小時內沒有任何變更，則會傳送快照集。 |
| Linux 檔案 | 15 分鐘 | 是。 如果 24 小時內沒有任何變更，則會傳送快照集。 |
| Windows 服務 | 30 分鐘 | 是，找到變更時，每隔 30 分鐘傳送一次。 每隔 24 小時傳送一次快照集 (不論是否有變更)。 因此，即使沒有任何變更也會傳送快照集。 |
| Linux 精靈 | 5 分鐘 | 是。 如果 24 小時內沒有任何變更，則會傳送快照集。 |
| Windows 軟體 | 30 分鐘 | 是，找到變更時，每隔 30 分鐘傳送一次。 每隔 24 小時傳送一次快照集 (不論是否有變更)。 因此，即使沒有任何變更也會傳送快照集。 |
| Linux 軟體軟體 | 5 分鐘 | 是。 如果 24 小時內沒有任何變更，則會傳送快照集。 |

### <a name="registry-key-change-tracking"></a>登錄機碼變更追蹤

Log Analytics 會使用「變更追蹤」解決方案來執行 Windows 登錄監視和追蹤。 監視登錄機碼變更的目的是找出第三方程式碼和惡意程式碼可啟用的擴充點。 下列清單顯示解決方案所追蹤的預設登錄機碼和其追蹤原因。

- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup
    - 監視在啟動時執行的指令碼。
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown
    - 監視在關機時執行的指令碼。
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run
    - 監視使用者登入其 Windows 帳戶之前載入的金鑰。 此金鑰供 64 位元電腦上執行的 32 位元程式使用。
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components
    - 監視應用程式設定的變更。
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers
    - 監視一般自動啟動項目，而這些項目直接連結到 Windows 檔案總管，而且通常會使用 Explorer.exe 以內含方式執行。
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers
    - 監視一般自動啟動項目，而這些項目直接連結到 Windows 檔案總管，而且通常會使用 Explorer.exe 以內含方式執行。
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Background\ShellEx\ContextMenuHandlers
    - 監視一般自動啟動項目，而這些項目直接連結到 Windows 檔案總管，而且通常會使用 Explorer.exe 以內含方式執行。
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - 監視圖示覆疊處理常式註冊。
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - 針對在 64 位元電腦上執行的 32 位元程式，監視圖示覆疊處理常式註冊。
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Internet Explorer 之新瀏覽器協助程式物件外掛程式的監視器。 用以存取當前頁面的文件物件模型 (DOM) 並控制瀏覽。
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Internet Explorer 之新瀏覽器協助程式物件外掛程式的監視器。 用以存取目前頁面的文件物件模型 (DOM) 並控制 64 位元電腦上執行之 32 位元程式的瀏覽。
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions
    - 監視新的 Internet Explorer 擴充功能，例如自訂工具功能表和自訂工具列按鈕。
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions
    - 針對在 64 位元電腦上執行的 32 位元程式，監視新的 Internet Explorer 擴充功能，例如自訂工具功能表和自訂工具列按鈕。
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    - 監視與 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 相關聯的 32 位元驅動程式。 類似 SYSTEM.INI 檔案中的 [drivers] 區段。
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    - 針對在 64 位元電腦上執行的 32 位元程式，監視與 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 相關聯的 32 位元驅動程式。 類似 SYSTEM.INI 檔案中的 [drivers] 區段。
- HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls
    - 監視已知或常用系統 DLL 清單；此系統可防止人員放入特洛伊木馬病毒版本的系統 DLL 來利用弱式應用程式目錄權限。
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
    - 監視可從 Winlogon 接收事件通知的套件清單，而 Winlogon 是 Windows 作業系統的互動式登入支援模型。


## <a name="use-change-tracking"></a>使用變更追蹤
安裝此方案之後，您可以在 OMS 的 [概觀] 頁面中使用 [變更追蹤] 圖格，檢視受監視伺服器的變更摘要。

![變更追蹤磚的影像](./media/log-analytics-change-tracking/change-tracking-tile.png)

您可以檢視基礎結構的變更，然後向下鑽研下列類別的詳細資訊：

* 軟體和 Windows 服務的組態類型所做的變更
* 應用程式的軟體變更和個別伺服器的更新
* 每個應用程式的軟體變更總數
* Linux 套件
* 個別伺服器的 Windows 服務變更
* Linux 精靈變更

![變更追蹤儀表板的影像](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![變更追蹤儀表板的影像](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>檢視任何變更類型的變更
1. 在 [概觀] 頁面上，按一下 [變更追蹤] 圖格。
2. 在 [變更追蹤] 儀表板上，檢閱其中一個變更類型刀鋒視窗中的摘要資訊，然後按一下其中一個，在 [記錄搜尋] 頁面中檢視詳細資訊。
3. 您可以在任何 [記錄搜尋] 頁面上，按時間、詳細結果和您的記錄搜尋記錄來檢視結果。 您也可以按 Facet 篩選以縮減結果。

## <a name="next-steps"></a>後續步驟
* 使用 [Log Analytics 中的記錄檔搜尋](log-analytics-log-searches.md) ，檢視詳細的變更追蹤資料。

