---
title: "在 Azure Data Factory 中建立自我裝載整合執行階段 | Microsoft Docs"
description: "了解如何在 Azure Data Factory 中建立自我裝載整合執行階段，它可讓資料處理站存取私人網路中的資料存放區。"
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: abnarain
ms.openlocfilehash: 0fcc245369d90042066cbfc516a8c32db7272bd3
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2017
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>如何建立和設定自我裝載整合執行階段
整合執行階段 (IR) 是 Azure Data Factory 所使用的計算基礎結構，可提供跨不同網路環境的資料整合功能。 如需 IR 的詳細資訊，請參閱[整合執行階段概觀](concepts-integration-runtime.md)。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用 Data Factory 服務的 1 版 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-introduction.md)。

自我裝載整合執行階段可以在雲端資料存放區以及私人網路的資料存放區之間執行複製活動，並且可以在內部部署或 Azure 虛擬網路中針對計算資源分派轉換活動。 自我裝載整合執行階段必須安裝在內部部署電腦上，或安裝在位於私人網路內的虛擬機器上。  

本文件會介紹如何建立和設定自我裝載 IR。

## <a name="high-level-steps-to-install-self-hosted-ir"></a>安裝自我裝載 IR 的概略步驟
1.  建立自我裝載整合執行階段。 以下是 PowerShell 範例︰

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  下載並安裝自我裝載整合執行階段 (在本機電腦上)。
3.  擷取驗證金鑰，並使用該金鑰註冊自我裝載整合執行階段。 以下是 PowerShell 範例︰

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="command-flow-and-data-flow"></a>命令流程和資料流程
當您在內部部署和雲端之間移動資料時，活動會使用自我裝載整合執行階段將資料從內部部署資料來源傳輸到雲端，反之亦然。

利用自我裝載 IR 進行複製步驟的概略資料流程如下：

![高階概述](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. 資料開發人員使用 PowerShell Cmdlet 在 Azure 資料處理站內建立自我裝載整合執行階段。 目前，Azure 入口網站不支援此功能。
2. 資料開發人員建立內部部署資料存放區的連結服務，方法是指定自我裝載整合執行階段執行個體 (此執行個體應該用來連線到資料存放區)。 在設定連結服務的過程中，資料開發人員會使用「認證管理員」應用程式 (目前還不支援) 來設定驗證類型和認證。 「認證管理員」應用程式對話方塊將會與資料存放區進行通訊，以測試連線與要儲存認證的自我裝載整合執行階段。
4.  自我裝載整合執行階段節點會使用 Windows 資料保護應用程式開發介面 (DPAPI) 將認證加密，並將它儲存在本機上。 如果有多個節點設定為高可用性，則該認證會進一步同步處理到其他節點。 每個節點都會使用 DPAPI 將認證加密，並將它儲存在本機上。 認證同步處理無需資料開發人員介入，並且由自我裝載 IR 處理。    
5.  Data Factory 服務會和自我裝載整合執行階段進行通訊，以透過使用共用 Azure 服務匯流排佇列的**控制通道**，進行作業的排程和管理。 當需要執行活動作業時，Data Factory 會將要求與任何認證資訊排入佇列 (如果認證尚未儲存在自我裝載整合執行階段上)。 輪詢佇列之後，自我裝載整合執行階段便會開始作業。
6.  自我裝載整合執行階段會根據複製活動在資料管線中的設定方式，將資料從內部部署存放區複製到雲端儲存體，反之亦然。 針對這個步驟，自我裝載整合執行階段會透過安全的 (HTTPS) 通道，直接與雲端式儲存體服務 (例如 Azure Blob 儲存體) 進行通訊。

## <a name="considerations-for-using-self-hosted-ir"></a>使用自我裝載 IR 的考量

- 單一的自我裝載整合執行階段可用於多個內部部署資料來源。 不過，**單一自我裝載整合執行階段只會繫結至一個 Azure 資料處理站**，不能與另一個資料處理站共用。
- 單一電腦上**只能安裝一個自我裝載整合執行階段執行個體**。 假設您有兩個需要存取內部部署資料來源的資料處理站，您就需要在兩部內部部署電腦上安裝自我裝載整合執行階段。 換句話說，自我裝載整合執行階段會繫結至特定的資料處理站
- **自我裝載整合執行階段不一定要在與資料來源相同的電腦上**。 不過，讓自我裝載整合執行階段更靠近資料來源，可以減少自我裝載整合執行階段連線到資料來源花費的時間。 建議您將自我裝載整合執行階段安裝在與裝載內部部署資料來源不同的電腦上。 當自我裝載整合執行階段和資料來源位於不同的電腦上時，自我裝載整合執行階段才不會與資料來源爭奪資源。
- 您可以有**多個自我裝載整合執行階段在不同電腦上，但連接至相同的內部部署資料來源**。 例如，您可能有兩個自我裝載整合執行階段用於為兩個資料處理站提供服務，但相同的內部部署資料來源都向這兩個資料處理站註冊。
- 若您已在電腦上安裝用於 **Power BI** 案例的閘道，請在另一部電腦上安裝**另一個用於 Azure Data Factory 的自我裝載整合執行階段**。
- 自我裝載整合執行階段必須用於支援 Azure 虛擬網路內的資料整合。
- 即使您使用 **ExpressRoute**，也應該將資料來源視為內部部署資料來源 (亦即在防火牆後面)。 請使用自我裝載整合執行階段來建立服務與資料來源之間的連線。
- 即使資料儲存在雲端中的 **Azure IaaS 虛擬機器**上，您也必須使用自我裝載整合執行階段。

## <a name="prerequisites"></a>必要條件

- 支援的**作業系統**版本包括 Windows 7 Service Pack 1、Windows 8.1、Windows 10、Windows Server 2008 R2 SP1、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016。 **不支援在網域控制站**上安裝自我裝載整合執行階段。
- 必須有 **.NET Framework 4.6.1 或更新版本**。 如果您要在 Windows 7 電腦上安裝自我裝載整合執行階段，請安裝 .NET Framework 4.6.1 或更新版本。 如需詳細資訊，請參閱 [.NET Framework 系統需求](/dotnet/framework/get-started/system-requirements) 。
- 建議的自我裝載整合執行階段電腦「組態」為至少 2 GHz、4 核心、8 GB RAM 和 80 GB 磁碟。
- 如果主機電腦休眠，自我裝載整合執行階段不會回應資料要求。 因此，安裝自我裝載整合執行階段之前，請先在電腦上設定適當的電源計劃。 如果電腦已設定為休眠，安裝自我裝載整合執行階段時會提示訊息。
- 您必須是電腦上的系統管理員，才能成功安裝和設定自我裝載整合執行階段。
- 因為複製活動執行會以特定的頻率發生，在電腦上的資源使用量 (CPU、記憶體) 也會遵循與尖峰和閒置時間相同的模式。 資源使用率也仰賴要移動的資料量。 如果有多個複製作業正在進行，您會看到資源使用量在尖峰時段增加。

## <a name="installation-best-practices"></a>安裝最佳做法
您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=39717)下載 MSI 安裝套件來安裝自我裝載整合執行階段。 如需逐步指示，請參閱[在內部部署與雲端之間移動資料一文](tutorial-hybrid-copy-powershell.md)。

- 為自我裝載整合執行階段設定主機電腦上的電源計劃，使電腦不休眠。 如果主機電腦休眠，自我裝載整合執行階段就會離線。
- 請定期備份與自我裝載整合執行階段相關聯的認證。

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>從下載中心安裝和註冊自我裝載 IR

1. 瀏覽至 [Microsoft 整合執行階段下載頁面](https://www.microsoft.com/download/details.aspx?id=39717)。
2. 按一下 [下載]，選取適當的版本 (**32 位元**與**64 位元**)，然後按 [下一步]。
3. 直接執行 **MSI** 或將它儲存至您的硬碟並執行。
4. 在 [歡迎] 頁面上，選取一個**語言**，然後按 [下一步]。
5. **接受**使用者授權合約，然後按 [下一步]。
6. 選取 [資料夾] 來安裝自我裝載整合執行階段，並按一下 [下一步]。
7. 在 [準備安裝] 頁面上，按一下 [安裝]。
8. 按一下 [完成]  來完成安裝。
9. 使用 Azure PowerShell 取得驗證金鑰。 擷取驗證金鑰的 PowerShell 範例：

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. 在您電腦上執行的 Microsoft 整合執行階段組態管理員 [註冊整合執行階段 (自我裝載)] 頁面上，執行下列步驟：
    1. 將**驗證金鑰**貼上到文字區域。
    2. (選擇性) 按一下 [顯示驗證金鑰] 以查看金鑰文字。
    3. 按一下 [註冊] 。


## <a name="high-availability-and-scalability"></a>高可用性和延展性
自我裝載的 Integration Runtime 可以與多部內部部署電腦相關聯。 這些電腦稱為節點。 您最多可以將四個節點關聯到一個自我裝載整合執行階段。 讓邏輯閘道擁有多個節點 (安裝了閘道的內部部署機器) 的好處如下：
1. 自我裝載整合執行階段的高可用性能夠確保最多 4 個節點的持續性，如此一來它就不再是您的巨量資料解決方案或 Azure Data Factory 雲端資料整合的單一失敗點。
2. 提升在內部部署和雲端資料存放區之間移動資料時的效能和輸送量。 如需詳細資訊，請參閱[效能比較](copy-activity-performance.md)。

您可以關聯多個節點，方法是直接從[下載中心](https://www.microsoft.com/download/details.aspx?id=39717)安裝自我裝載 Integration Runtime 軟體，並使用從 New-AzureRmDataFactoryV2IntegrationRuntimeKey Cmdlet 取得的驗證金鑰來註冊它，如[教學課程](tutorial-hybrid-copy-powershell.md)中所述。

> [!NOTE]
> 您不需要建立新的自我裝載整合執行階段來關聯每個節點。

## <a name="system-tray-icons-notifications"></a>系統匣圖示/通知
如果您將游標放在系統匣圖示/通知訊息上，您會看到自我裝載整合執行階段狀態的詳細資料。

![系統匣通知](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>連接埠和防火牆
有兩個您需要考量的防火牆：在組織的中央路由器上執行的**公司防火牆**，以及在已安裝自我裝載整合執行階段的本機電腦上設定為精靈的 **Windows 防火牆**。

![防火牆](media\create-self-hosted-integration-runtime\firewall.png)

在**公司防火牆**層級，您需要設定下列網域和連出連接埠：

網域名稱 | 連接埠 | 說明
------------ | ----- | ------------
*.servicebus.windows.net | 443、80 | 用於與「資料移動服務」後端進行通訊
*.core.windows.net | 443 | 用於使用 Azure Blob 的分段複製 (如果已設定)
*.frontend.clouddatahub.net | 443 | 用於與「資料移動服務」後端進行通訊

**Windows 防火牆**層級 (電腦層級) 通常會啟用這些連出連接埠。 如果沒有，您可以在自我裝載整合執行階段的電腦上相應地設定網域和連接埠。

> [!NOTE]
> 視您的來源/接收器而定，您可能需要將額外的網域和輸出連接埠加到您公司/Windows 防火牆的白名單中。
>
> 對於某些雲端資料庫 (例如：Azure SQL Database、Azure Data Lake 等)，您可能需要將自我裝載整合執行階段電腦的 IP 位址加到其防火牆設定的白名單中。

### <a name="copy-data-from-a-source-to-a-sink"></a>將資料從來源複製到接收器
請確定在公司防火牆、自我裝載整合執行階段電腦上的 Windows 防火牆，及資料存放區本身都已正確啟用防火牆規則。 啟用這些規則可讓自我裝載整合執行階段成功連線到來源和接收器。 請為複製作業所涉及的每個資料存放區啟用規則。

例如，若要**從內部部署資料存放區複製到 Azure SQL Database 接收器或 Azure SQL 資料倉儲接收器**，請執行下列步驟︰

- 在 Windows 防火牆和公司防火牆的通訊埠 **1433** 上都允許進行輸出 **TCP** 通訊。
- 設定 Azure SQL 伺服器的防火牆設定，將自我裝載整合執行階段電腦的 IP 位址新增到允許的 IP 位址清單。

> [!NOTE]
> 如果您的防火牆不允許使用連出連接埠 1433，自我裝載整合執行階段將無法直接存取 Azure SQL。 在此情況下，您可以使用[分段複製](copy-activity-performance.md)來移到 SQL Azure Database/SQL Azure DW。 在此案例中，您只需要 HTTPS (連接埠 443) 即可進行資料移動。


## <a name="proxy-server-considerations"></a>Proxy 伺服器考量
如果您的公司網路環境使用 Proxy 伺服器來存取網際網路，請將自我裝載整合執行階段設定為使用適當的 Proxy 設定。 您可以在初始註冊階段期間設定 Proxy。

![指定 Proxy](media\create-self-hosted-integration-runtime\specify-proxy.png)

自我裝載整合執行階段會使用 Proxy 伺服器來連線到雲端服務。 進行初始設定時，按一下 [變更] 連結。 您會看到 [Proxy 設定] 對話方塊。

![設定 Proxy](media\create-self-hosted-integration-runtime\set-http-proxy.png)

有三個組態選項：

- **不使用 Proxy**︰自我裝載整合執行階段不會明確地使用任何 Proxy 來連線到雲端服務。
- **使用系統 Proxy**：自我裝載整合執行階段會使用 diahost.exe.config 和 diawp.exe.config 中設定的 Proxy 設定。如果 diahost.exe.config 和 diawp.exe.config 中未設定任何 Proxy，自我裝載整合執行階段就會直接連線到雲端服務而不經由 Proxy。
- **使用自訂 Proxy**：設定要用於自我裝載整合執行階段的 HTTP Proxy 設定，而不使用 diahost.exe.config 和 diawp.exe.config 中的設定。必須指定「IP 位址」和「連接埠」。 「使用者名稱」和「密碼」則為選擇性，需視您的 Proxy 驗證設定而定。 自我裝載整合執行階段上的所有設定都會使用 Windows DPAPI 加密，並儲存在本機電腦上。

在您儲存已更新的 Proxy 設定之後，自我裝載整合執行階段主機服務會自動重新啟動。

在成功註冊自我裝載整合執行階段之後，如果您想要檢視或更新 Proxy 設定，請使用「整合執行階段組態管理員」。

1.  啟動 [Microsoft 整合執行階段組態管理員]。
2.  切換到 [設定]  索引標籤。
3.  按一下 [HTTP Proxy] 區段中的 [變更] 連結，以啟動 [設定 HTTP Proxy] 對話方塊。
4.  按 [下一步] 按鈕之後，您會看到一個警告對話方塊，此對話方塊會向您請求權限來儲存 Proxy 設定及重新啟動「整合執行階段主機服務」。

您可以使用「組態管理員」工具來更新 HTTP Proxy。

![檢視 Proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> 如果您為 Proxy 伺服器設定了 NTLM 驗證，「整合執行階段主機服務」就會以網域帳戶執行。 如果您稍後變更網域帳戶的密碼，請記得更新服務的組態設定並相應地將它重新啟動。 基於這項需求，建議您使用不需要經常更新密碼的專用網域帳戶來存取 Proxy 伺服器。

### <a name="configure-proxy-server-settings"></a>設定 Proxy 伺服器設定

如果您為 HTTP Proxy 選取 [使用系統 Proxy] 設定，自我裝載整合執行階段就會使用 diahost.exe.config 和 diawp.exe.config 中的 Proxy 設定。如果 diahost.exe.config 和 diawp.exe.config 中未指定任何 Proxy，自我裝載整合執行階段就會直接連線到雲端服務而不經由 Proxy。 下列程序說明如何更新 diahost.exe.config 檔案。

1. 在「檔案總管」中，建立一份 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config 的安全複本來備份原始檔案。
2. 以系統管理員身分啟動 Notepad.exe，並開啟文字檔 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config。您會在以下程式碼中看見 system.net 的預設標籤：

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    接著，您可以新增 Proxy 伺服器詳細資料，如以下範例所示：

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    在 Proxy 標記內可以有其他屬性，用以指定必要的設定，例如 scriptLocation。 請參閱 [Proxy 項目 (網路設定)](https://msdn.microsoft.com/library/sa91de1e.aspx) 以了解語法。

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. 將設定檔儲存到原始位置中，然後重新啟動「自我裝載整合執行階段主機服務」以套用變更。 重新啟動服務：使用 [控制台] 中的 [服務] 小程式，或是從 [整合執行階段組態管理員] > 按一下 [停止服務] 按鈕，然後按一下 [啟動服務]。 如果服務未啟動，可能因為在已編輯的應用程式組態檔中加入了不正確的 XML 標記語法。

> [!IMPORTANT]
> 別忘了同時更新 diahost.exe.config 和 diawp.exe.config。

除了這幾點以外，您也必須確定 Microsoft Azure 包含在公司的允許清單中。 如需有效的 Microsoft Azure IP 位址清單，可從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=41653)下載。

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>防火牆和 Proxy 伺服器相關問題的可能徵兆
如果發生如下錯誤，有可能是因為防火牆或 Proxy 伺服器的設定不正確，使得自我裝載整合執行階段無法連線到 Data Factory 來進行自我驗證。 請參閱上一節，以確保您的防火牆和 Proxy 伺服器的設定皆正確。

1.  當您嘗試註冊自我裝載整合執行階段時，您會收到下列錯誤：「無法註冊此整合執行階段節點! 請確認驗證金鑰有效，且整合執行階段主機服務正在這部電腦上執行。 "
2.  當您開啟「整合執行階段組態管理員」時，您會看到「已中斷連線」或「正在連線」狀態。 檢視 Windows 事件記錄檔時，在 [事件檢視器] > [應用程式和服務記錄檔] > [Microsoft 整合執行階段] 下，您會看到如下錯誤訊息：

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="open-port-8060-for-credential-encryption"></a>開啟用於認證加密的連接埠 8060
當您在 Azure 入口網站中設定了內部部署連結服務時，「設定認證」應用程式 (目前還不支援) 會使用連入連接埠 8060 將認證轉送到自我裝載整合執行階段。 在自我裝載整合執行階段安裝期間，根據預設，自我裝載整合執行階段安裝會在自我裝載整合執行階段電腦上開啟它。

如果您使用協力廠商的防火牆，則可以手動開啟連接埠 8050。 如果您在設定自我裝載整合執行階段時遇到防火牆問題，您可以嘗試使用下列命令來安裝自我裝載整合執行階段，而不設定防火牆。

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

如果您選擇不開啟自我裝載整合執行階段電腦上的連接埠 8060，則請使用「設定認證」****應用程式以外的機制來設定資料存放區認證。 例如，您可以使用 New-AzureRmDataFactoryV2LinkedServiceEncryptCredential PowerShell Cmdlet。 若要了解如何設定資料存放區認證，請參閱＜設定認證和安全性＞一節。


## <a name="next-steps"></a>後續步驟
如需逐步指示，請參閱下列教學課程：[教學課程：將內部部署資料複製到雲端](tutorial-hybrid-copy-powershell.md)。
