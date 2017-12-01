---
title: "針對 Azure SQL 資料同步 (預覽) 進行疑難排解 | Microsoft Docs"
description: "了解如何對使用 Azure SQL 資料同步 (預覽) 的常見問題進行疑難排解。"
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 50cabbaa584671e52c1ea7efbd2ad990b8438272
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>針對 SQL 資料同步 (預覽) 的問題進行疑難排解

本文說明如何對 Azure SQL 資料同步 (預覽) 的常見問題進行疑難排解。 如果有問題的解決方案，即會在此處提供。

如需 SQL 資料同步 (預覽) 的概觀，請參閱[使用 Azure SQL 資料同步 (預覽)，跨多個雲端和內部部署資料庫同步資料](sql-database-sync-data.md)。

## <a name="sync-issues"></a>同步問題

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>與用戶端代理程式建立關聯的內部部署資料庫的入口網站 UI 同步失敗

#### <a name="description-and-symptoms"></a>描述和徵兆

在 SQL 資料同步 (預覽) 入口網站 UI 上，與代理程式相關聯之內部部署資料庫的同步失敗。 在執行代理程式的本機電腦上，您會在事件記錄檔看到 System.IO.IOException 錯誤。 錯誤說明磁碟空間不足。

#### <a name="resolution"></a>解決方案

在 %TEMP% 目錄所在的磁碟機上建立更多空間。

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>我的同步群組停頓於處理中狀態

#### <a name="description-and-symptoms"></a>描述和徵兆

SQL 資料同步 (預覽) 中的同步群組已處於處理中狀態一段時間。 它不會回應**停止**命令，而記錄中未顯示新的項目。

#### <a name="cause"></a>原因

下列任何一個狀況都可能導致同步群組停頓於處理中狀態：

-   **用戶端代理程式已離線**。 確定用戶端代理程式已上線，然後再試一次。

-   **用戶端代理程式已解除安裝或遺失**。 如果用戶端代理程式已解除安裝或以別的方式遺失：

    1. 如果檔案存在，請從 SQL 資料同步 (預覽) 安裝資料夾中移除代理程式的 XML 檔案。
    2. 在內部部署電腦上安裝代理程式 (可在相同或不同電腦上)。 然後提交在入口網站中為顯示為離線之代理程式所產生的代理程式金鑰。

-   **SQL 資料同步服務已停止**。

    1. 在 [開始] 功能表中，搜尋 [服務]。
    2. 在搜尋結果中，選取 [服務]。
    3. 尋找 **SQL 資料同步 (預覽)** 服務。
    4. 如果服務狀態是 [已停止]，請以滑鼠右鍵按一下服務名稱，然後選取 [啟動]。

#### <a name="resolution"></a>解決方案

如果上述資訊未讓您的同步群組脫離處理中狀態，Microsoft 支援人員可以重設您的同步群組的狀態。 若要讓您同步群組的狀態重設，請在 [Azure SQL Database 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)中建立文章。 在文章中，新增您的訂用帳戶識別碼和需要重設之群組的同步群組識別碼。 Microsoft 支援工程師會回應您的文章，並在已重設狀態時告訴您。

### <a name="i-see-erroneous-data-in-my-tables"></a>我在資料表中看到錯誤的資料

#### <a name="description-and-symptoms"></a>描述和徵兆

如果資料表有相同的名稱，但在同步中併入了來自不同資料庫結構描述，在同步之後，您會在資料表中看到發生錯誤的資料。

#### <a name="cause"></a>原因

SQL 資料同步 (預覽) 佈建程序對於具有相同名稱但位在不同結構描述的資料表會使用相同的追蹤資料表。 因此，來自這兩個資料表的變更會反映在相同的追蹤資料表。 這會導致在同步期間產生錯誤的資料變更。

#### <a name="resolution"></a>解決方案

確定同步牽涉的資料表名稱彼此不同，即使資料裝屬於資料庫中的不同結構描述。

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>我在同步成功之後看到不一致的主索引鍵資料

#### <a name="description-and-symptoms"></a>描述和徵兆

同步報告為成功，且記錄檔顯示沒有任何失敗或略過的資料列，但您發現主索引鍵資料在同步群組中的資料庫之間不一致。

#### <a name="cause"></a>原因

此結果是原先的設計。 任何主索引鍵資料行中的變更都會導致主索引鍵已變更的資料列中資料不一致。

#### <a name="resolution"></a>解決方案

若要避免這個問題，請確認主要索引鍵資料行中的資料都未變更。

在發生問題之後，若要修正此問題，請從同步群組中的所有端點刪除具有不一致資料的資料列。 然後，重新插入資料列。

### <a name="i-see-a-significant-degradation-in-performance"></a>我看到效能顯著降低

#### <a name="description-and-symptoms"></a>描述和徵兆

您的效能大幅降低，可能會達到甚至無法開啟資料同步 UI 的程度。

#### <a name="cause"></a>原因

最可能的原因是同步迴圈。 當同步群組 A 的同步觸發同步群組 B 的同步，然後觸發同步群組 A 的同步，即發生同步迴圈。實際情況可能更為複雜，而且它也可能在迴圈中牽涉到兩個以上的同步群組。 問題在於同步有因同步群組彼此重疊造成的循環觸發。

#### <a name="resolution"></a>解決方案

最佳的修正方式就是預防。 確保您的同步群組中沒有循環參考。 由某個同步群組同步的任何資料列不能同步至另一個同步群組。

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>我看見錯誤訊息：「無法將 NULL 值插入資料行 \<column\>。 資料行不允許 Null。」 這是什麼意思，該如何修正？ 
此錯誤訊息表示發生了下列兩種問題的其中之一：
-  資料表沒有主索引鍵。 若要修正此問題，請將主索引鍵新增至要同步的所有資料表。
-  在 CREATE INDEX 陳述式中可能有 WHERE 子句。 資料同步 (預覽) 不會處理這個狀況。 若要修正此問題，請移除 WHERE 子句或手動變更所有資料庫。 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>資料同步 (預覽) 如何處理循環參考？ 也就是，相同的資料已在多個同步群組中同步，且因此持續變更？
資料同步 (預覽) 不會處理循環參考。 請務必避免。 

## <a name="client-agent-issues"></a>用戶端代理程式問題

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>用戶端代理程式安裝、解除安裝或修復失敗

#### <a name="cause"></a>原因

許多情節可能會造成這個失敗。 若要判斷此失敗的特定原因，請查看記錄。

#### <a name="resolution"></a>解決方案

若要尋找失敗的特定原因，請產生並查看 Windows Installer 記錄。 您可以在命令提示字元開啟記錄。 例如，如果下載的 AgentServiceSetup.msi 檔案為 LocalAgentHost.msi，請使用下列命令列來產生並檢查記錄：

-   安裝：`msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   解除安裝：`msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

您也可以為 Windows Installer 所執行的所有安裝開啟記錄。 Microsoft 知識庫文章[如何啟用 Windows Installer 記錄](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging)提供開啟 Windows Installer 記錄的單鍵解決方案。 它也提供記錄檔的位置。

### <a name="my-client-agent-doesnt-work"></a>我的用戶端代理程式無法運作

#### <a name="description-and-symptoms"></a>描述和徵兆

當您嘗試使用用戶端代理程式時，您得到下列訊息：

「同步處理失敗並發生例外狀況。嘗試將參數 www.microsoft.com/.../05:GetBatchInfoResult 還原序列化時發生錯誤。 如需詳細資料，請參閱 InnerException。

「內部例外狀況訊息：類型 'microsoft.synchronization.changebatch ' 不是有效的集合類型，因為它沒有預設建構函式。」

#### <a name="cause"></a>原因

這是 SQL 資料同步 (預覽) 安裝的已知問題。 此訊息的最可能原因是下列其中一項：

-   您正在執行 Windows 8 Developer Preview。
-   您已安裝.NET Framework 4.5。

#### <a name="resolution"></a>解決方案

確定您在未執行 Windows 8 Developer Preview 且未安裝 .NET Framework 4.5 的電腦上安裝用戶端代理程式。

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>我取消解除安裝之後，我的用戶端代理程式無法運作

#### <a name="description-and-symptoms"></a>描述和徵兆

用戶端代理程式無法運作，即使您取消其解除安裝亦然。

#### <a name="cause"></a>原因

SQL 資料同步 (預覽) 用戶端代理程式不會儲存認證，因此發生此問題。

#### <a name="resolution"></a>解決方案

您可以嘗試這兩種解決方案：

-   使用 services.msc 重新輸入用戶端代理程式的認證。
-   解除安裝此用戶端代理程式，然後安裝新的用戶端代理程式。 從[下載中心](http://go.microsoft.com/fwlink/?linkid=221479)下載並安裝最新的用戶端代理程式。

### <a name="my-database-isnt-listed-in-the-agent-list"></a>我的資料庫未列在代理程式清單

#### <a name="description-and-symptoms"></a>描述和徵兆

當您嘗試將現有的 SQL Server 資料庫新增至同步群組時，資料庫不會顯示在代理程式的清單中。

#### <a name="cause"></a>原因

這些情節可能會造成此問題：

-   用戶端代理程式和同步群組位於不同的資料中心。
-   用戶端代理程式的資料庫清單不是最新的。

#### <a name="resolution"></a>解決方案

解決方式視原因而定。

- **用戶端代理程式和同步群組位於不同的資料中心**

    用戶端代理程式和同步群組必須在相同資料中心。 若要設定此情況，您有兩個選項：

    -   在與同步群組所在的相同資料中心中建立新的代理程式。 然後向該代理程式註冊資料庫。
    -   刪除目前的同步處理群組。 然後，在代理程式所在的資料中心重新建立同步群組。

- **用戶端代理程式的資料庫清單不是最新的**

    停止然後再重新啟動用戶端代理程式服務。

    本機代理程式只會在第一次提交代理程式金鑰時下載相關聯的資料庫清單。 在後續的代理程式金鑰提交時，它不會下載相關聯資料庫的清單。 在代理程式移動期間註冊的資料庫不會出現在原始代理程式執行個體上。

### <a name="client-agent-doesnt-start-error-1069"></a>用戶端代理程式無法啟動 (錯誤 1069)

#### <a name="description-and-symptoms"></a>描述和徵兆

您發現代理程式未在裝載 SQL Server 的電腦上執行。 當您嘗試以手動方式啟動代理程式時，您會看到對話方塊顯示錯誤訊息：「錯誤 1069：登入失敗所以服務無法啟動。」

![資料同步處理錯誤 1069 對話方塊](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>原因

此錯誤的可能原因是，在本機伺服器上的密碼在您建立代理程式及代理程式密碼之後已變更。

#### <a name="resolution"></a>解決方案

將代理程式的密碼更新成目前的伺服器密碼：

1. 找出 SQL 資料同步 (預覽) 用戶端代理程式預覽服務。  
    a. 選取 [開始]。  
    b. 在搜尋方塊中輸入 **services.msc**。  
    c. 在搜尋結果中，選取 [服務]。  
    d. 在 [服務] 視窗中，捲動至 [SQL 資料同步 (預覽) Agent Preview] 的項目。  
2. 在 [SQL 資料同步 (預覽) Agent Preview] 上按一下滑鼠右鍵，然後選取 [停止]。
3. 在 [SQL 資料同步 (預覽) Agent Preview] 上按一下滑鼠右鍵，然後選取 [內容]。
4. 在 [SQL 資料同步 (預覽) Agent Preview 內容] 上，選取 [登入] 索引標籤。
5. 在 [密碼] 方塊中，輸入您的密碼。
6. 在 [確認密碼] 方塊中，重新輸入密碼。
7. 選取 [套用]，然後選取 [確定]。
8. 在 [服務] 視窗中，以滑鼠右鍵按一下 [SQL 資料同步 (預覽) Agent Preview] 服務，然後按一下 [啟動]。
9. 關閉 [服務] 視窗。

### <a name="i-cant-submit-the-agent-key"></a>我無法提交代理程式金鑰

#### <a name="description-and-symptoms"></a>描述和徵兆

您建立或重新建立代理程式金鑰之後，嘗試透過 SqlAzureDataSyncAgent 應用程式提交該金鑰。 提交無法完成。

![[同步錯誤] 對話方塊 - 無法提交代理程式金鑰](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

在繼續之前，請檢查下列條件：

-   SQL 資料同步 (預覽) Windows 服務正在執行。  
-   SQL 資料同步 (預覽) Preview Windows 服務的服務帳戶具有網路存取權。    
-   用戶端代理程式能夠連絡 Locator Service。 確認下列登錄機碼具有 https://locator.sync.azure.com/LocatorServiceApi.svc 值：  
    -   在 x86 電腦上：`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`  
    -   在 x64 電腦上：`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>原因

代理程式金鑰可唯一識別每個本機代理程式。 金鑰必須符合兩個條件：

-   SQL 資料同步 (預覽) 伺服器和本機電腦上的用戶端代理程式金鑰必須相同。
-   用戶端代理程式金鑰只能使用一次。

#### <a name="resolution"></a>解決方案

如果您的代理程式無法運作，這是因為不符合這些條件之一或兩者皆不符合。 讓代理程式重新運作：

1. 產生新的金鑰。
2. 將新的金鑰套用至代理程式。

若要將新的金鑰套用至代理程式：

1. 在檔案總管中，移至您的代理程式安裝目錄。 預設安裝目錄為 C:\\Program Files (x86)\\Microsoft SQL Data Sync。
2. 按兩下 bin 子目錄。
3. 開啟 SqlAzureDataSyncAgent 應用程式。
4. 選取 [提交代理程式金鑰]。
5. 在提供的空間中，貼上剪貼簿中的金鑰。
6. 選取 [確定] 。
7. 關閉程式。

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>如果無法連線到與用戶端代理程式相關聯的內部部署資料庫，則無法從入口網站刪除用戶端代理程式

#### <a name="description-and-symptoms"></a>描述和徵兆

如果無法連線到向 SQL 資料同步 (預覽) 用戶端代理程式註冊的本機端點 (也就是資料庫)，則無法刪除用戶端代理程式。

#### <a name="cause"></a>原因

無法刪除本機代理程式，因為無法連線的資料庫仍向代理程式註冊。 當您嘗試刪除代理程式時，刪除程序會嘗試連接資料庫，但是失敗。

#### <a name="resolution"></a>解決方案

使用「強制刪除」以刪除無法連線的資料庫。

> [!NOTE]
> 如果「強制刪除」之後，同步中繼資料資料表仍然存在，請使用 deprovisioningutil.exe 來清除它們。

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>本機同步代理程式應用程式無法連線至本機同步服務

#### <a name="resolution"></a>解決方案

請嘗試下列步驟：

1. 結束應用程式。  
2. 開啟 [元件服務] 畫面。  
    a. 在工作列上的 [搜尋] 方塊中，輸入 **services.msc**。  
    b. 在搜尋結果中按兩下 [服務]。  
3. 停止 [SQL 資料同步 (預覽) Preview] 服務。
4. 重新啟動 **SQL 資料同步 (預覽) Preview** 服務。  
5. 重新開啟應用程式。

## <a name="setup-and-maintenance-issues"></a>設定和維護問題

### <a name="i-get-a-disk-out-of-space-message"></a>我收到「磁碟空間不足」的訊息

#### <a name="cause"></a>原因

如果必須刪除剩餘的檔案，可能會出現「磁碟空間不足」訊息。 這可能是因為防毒軟體，或是嘗試進行刪除作業時檔案開啟。

#### <a name="resolution"></a>解決方案

手動刪除 %temp% 資料夾 (`del \*sync\* /s`) 中的同步檔案。 然後，刪除 %temp% 資料夾中的子目錄。

> [!IMPORTANT]
> 請勿於同步正在進行時刪除任何檔案。

### <a name="i-cant-delete-my-sync-group"></a>我無法刪除我的同步群組

#### <a name="description-and-symptoms"></a>描述和徵兆

您嘗試刪除同步群組失敗。

#### <a name="causes"></a>原因

任何下列的情節可能會導致無法刪除同步群組：

-   用戶端代理程式已離線。
-   用戶端代理程式已解除安裝或遺失。 
-   資料庫已離線。 
-   同步群組正在佈建或同步。 

#### <a name="resolution"></a>解決方案

若要解決刪除同步群組失敗：

-   確保用戶端代理程式已上線，然後再試一次。
-   如果用戶端代理程式已解除安裝或以別的方式遺失：  
    a. 如果檔案存在，請從 SQL 資料同步 (預覽) 安裝資料夾中移除代理程式的 XML 檔案。  
    b. 在內部部署電腦上安裝代理程式 (可在相同或不同電腦上)。 然後提交在入口網站中為顯示為離線之代理程式所產生的代理程式金鑰。
-   確保 SQL 資料同步 (預覽) 服務正在執行：  
    a. 在 [開始] 功能表中，搜尋 [服務]。  
    b. 在搜尋結果中，選取 [服務]。  
    c. 尋找 **SQL 資料同步 (預覽) Preview** 服務。  
    d. 如果服務狀態是 [已停止]，請以滑鼠右鍵按一下服務名稱，然後選取 [啟動]。
-   確保您的 SQL Database 和 SQL Server 資料庫都在線上。
-   等候佈建或同步程序完成，然後重試刪除同步群組。

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>我無法取消註冊內部部署 SQL Server 資料庫

#### <a name="cause"></a>原因

最有可能的原因是您正在嘗試取消註冊已刪除的資料庫。

#### <a name="resolution"></a>解決方案

若要取消註冊內部部署 SQL Server 資料庫，請選取資料庫，然後按一下 [強制刪除]。

如果這項作業無法從同步群組移除資料庫：

1. 停止然後再重新啟動用戶端代理程式主機服務：  
    a. 選取 [開始] 功能表。  
    b. 在搜尋方塊中輸入 **services.msc**。  
    c. 在搜尋結果窗格的 [程式] 區段中按兩下 [服務]。  
    d. 在 [SQL 資料同步 (預覽)] 服務上按一下滑鼠右鍵。  
    e. 如果服務正在執行，請將它停止。  
    f. 以滑鼠右鍵按一下服務，然後選取 [啟動]。  
    g. 檢查資料庫是否仍註冊。 如果它不再為已註冊，則作業完成。 否則，請繼續下一個步驟。
2. 開啟用戶端代理程式應用程式 (SqlAzureDataSyncAgent)。
3. 選取 [編輯認證]，然後輸入資料庫的認證。
4. 繼續進行取消註冊。

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>我沒有足夠的權限可以啟動系統服務

#### <a name="cause"></a>原因

有兩種情況會發生此錯誤：
-   使用者名稱及/或密碼不正確。
-   指定的使用者帳戶沒有足夠的權限，無法以服務方式登入。

#### <a name="resolution"></a>解決方案

授與以服務方式登入的服務認證給使用者帳戶：

1. 移至 [開始] > [控制台] > [系統管理工具] > [本機安全性原則] > [本機原則] > [使用者權限管理]。
2. 選取 [以服務方式登入]。
3. 在 [內容] 對話方塊中，新增使用者帳戶。
4. 選取 [套用]，然後選取 [確定]。
5. 關閉所有視窗。

### <a name="a-database-has-an-out-of-date-status"></a>資料庫具有「過期」狀態

#### <a name="cause"></a>原因

SQL 資料同步 (預覽) 會從服務移除已離線 45 天以上 (從資料庫離線的時開始計算) 的資料庫。 如果資料庫離線 45 天以上，然後再恢復上線，其狀態是**過期**。

#### <a name="resolution"></a>解決方案

您可以藉由確定沒有任何資料庫離線 45 天以上來避免**過期**狀態。

如果資料庫的狀態是**過期**：

1. 從同步群組的移除具有**過期**狀態的資料庫。
2. 將資料庫新增回到同步群組。

> [!WARNING]
> 您遺失此資料庫離線時對它的所有變更。

### <a name="a-sync-group-has-an-out-of-date-status"></a>同步群組具有「過期」的狀態

#### <a name="cause"></a>原因

如果一或多個變更無法套用於 45 天的整個保留期限，同步群組可能會過期。

#### <a name="resolution"></a>解決方案

若要避免同步群組的**過期**狀態，請定期在歷程記錄檢視器中檢查同步工作的結果。 調查並解決無法套用的任何變更。

如果同步群組的狀態為**過期**，請刪除同步群組，然後重新建立。

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>解除安裝或停止代理程式三分鐘內無法刪除同步群組

#### <a name="description-and-symptoms"></a>描述和徵兆

您無法在解除安裝或停止相關聯之 SQL 資料同步 (預覽) 用戶端代理程式的三分鐘內，刪除同步群組。

#### <a name="resolution"></a>解決方案

1. 在相關聯的同步處理代理程式為線上時移除同步群組 (建議選項)。
2. 如果代理程式為離線但已安裝，請讓它在內部部署電腦上連線。 等候代理程式在 SQL 資料同步 (預覽) 入口網站顯示為**線上**狀態。 然後，移除同步群組。
3. 如果代理程式因為已解除安裝而離線：  
    a.  如果檔案存在，請從 SQL 資料同步 (預覽) 安裝資料夾中移除代理程式的 XML 檔案。  
    b.  在內部部署電腦上安裝代理程式 (可在相同或不同電腦上)。 然後提交在入口網站中為顯示為離線之代理程式所產生的代理程式金鑰。  
    c. 嘗試刪除同步群組。

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>當我還原遺失或損毀的資料庫時，會發生什麼情況？

如果您從備份還原遺失或損毀的資料庫，資料庫所屬的同步群組中可能有無法聚合的資料。

## <a name="next-steps"></a>後續步驟
如需有關 SQL 資料同步 (預覽) 的詳細資訊，請參閱：

-   [使用 Azure SQL 資料同步 (預覽)，跨多個雲端和內部部署資料庫同步資料](sql-database-sync-data.md)  
-   [設定 Azure SQL 資料同步 (預覽)](sql-database-get-started-sql-data-sync.md)  
-   [Azure SQL 資料同步 (預覽) 最佳做法](sql-database-best-practices-data-sync.md)  
-   [使用 OMS Log Analytics 監視 Azure SQL 資料同步 (預覽)](sql-database-sync-monitor-oms.md)  
-   示範如何設定 SQL 資料同步 (預覽) 的完整 PowerShell 範例：  
    -   [使用 PowerShell 在多個 Azure SQL Database 之間進行同步處理](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [使用 PowerShell 設定「資料同步」在內部部署的 Azure SQL Database 和 SQL Server 之間進行同步處理](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [下載 SQL 資料同步 (預覽) REST API 文件](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

如需有關 SQL Database 的詳細資訊，請參閱：

-   [SQL Database 概觀](sql-database-technical-overview.md)
-   [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
