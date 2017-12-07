---
title: "使用 Azure Log Analytics 從內部部署 Linux 電腦收集資料 | Microsoft Docs"
description: "了解如何部署適用於 Linux 的 Log Analytics 代理程式，並啟用使用 Log Analytics 從該作業系統的資料收集。"
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 66748adc49ef921ab8adb5306b2a483234b076ae
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="collect-data-from-linux-computers-hosted-in-your-environment"></a>從您的環境中裝載的 Linux 電腦收集資料
[Azure Log Analytics](log-analytics-overview.md) 可將來自環境中實體或虛擬 Linux 電腦和其他資源的資料直接收集到單一儲存機制，以供詳細分析和相互關聯。  本快速入門向您示範如何以幾個簡單步驟來設定和收集 Linux 電腦的資料。  針對 Azure Linux VM，請參閱下列主題[收集關於 Azure 虛擬機器的資料](log-analytics-quick-collect-azurevm.md)。  

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure-portal"></a>登入 Azure 入口網站
登入 Azure 入口網站，網址為 [https://portal.azure.com](https://portal.azure.com)。 

## <a name="create-a-workspace"></a>建立工作區
1. 在 Azure 入口網站中，按一下左下角的 [更多服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics]。<br><br> ![Azure 入口網站](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. 按一下 [建立]，然後選取下列項目的選項：

  * 為新的 [OMS 工作區] 提供名稱，例如，*DefaultLAWorkspace*。 
  * 如果選取的預設值不合適，請從下拉式清單中選取要連結的 [訂用帳戶]。
  * 對於 [資源群組]，選取包含一或多個 Azure 虛擬機器的現有資源群組。  
  * 選取要部署 VM 的 [位置]。  如需詳細資訊，請查看 [Log Analytics 的可用區域](https://azure.microsoft.com/regions/services/)。
  * 您可以在 Log Analytics 中選擇三種不同的 [定價層]，但對於本快速入門，您要選取 [免費] 層。  如需特定層的詳細資訊，請參閱 [Log Analytics 價格詳細資料](https://azure.microsoft.com/pricing/details/log-analytics/)。

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. 在 [OMS 工作區] 窗格上提供必要資訊之後，按一下 [確定]。  

在驗證資訊及建立工作區時，您可以在功能表的 [通知] 底下追蹤其進度。 

## <a name="obtain-workspace-id-and-key"></a>取得工作區識別碼和金鑰
安裝 OMS Agent for Linux 之前，您需要 Log Analytics 工作區的工作區識別碼和索引鍵。  代理程式的包裝函式指令碼需要這項資訊，才能正確設定代理程式，並確定它能與 Log Analytics 順利進行通訊。  

1. 在 Azure 入口網站中，按一下左下角的 [更多服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics]。
2. 在 Log Analytics 工作區清單中，選取稍早建立的 *DefaultLAWorkspace*。
3. 選取 [進階設定]。<br><br> ![Log Analytics 進階設定](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. 選取 [連接的來源]，然後選取 [Linux 伺服器]。   
5. [工作區識別碼] 和 [主要金鑰] 右邊的值。 將兩者複製並貼到您最愛的編輯器。   

## <a name="install-the-agent-for-linux"></a>安裝 Agent for Linux
下列步驟會設定 Azure 和 Azure Government 雲端中 Log Analytics 代理程式的安裝程式。  

>[!NOTE]
>OMS agent for Linux 無法設定為回報多個 Log Analytics 工作區。  

1. 若要設定 Linux 電腦以連接到 Log Analytics，請執行下列命令，提供稍早複製的工作區識別碼和主索引鍵。  此命令會下載代理程式、驗證其總和檢查碼，並將它安裝。 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. 若要設定 Linux 電腦以連接到 Azure Government 雲端中的 Log Analytics，請執行下列命令，提供稍早複製的工作區識別碼和主索引鍵。  此命令會下載代理程式、驗證其總和檢查碼，並將它安裝。 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

## <a name="configure-agent-to-communicate-with-a-proxy-server"></a>設定代理程式以與 Proxy 伺服器通訊

如果您的 Linux 電腦需要透過 Proxy 伺服器與 Log Analytics 進行通訊，請執行下列步驟。  Proxy 組態值的語法如下：`[protocol://][user:password@]proxyhost[:port]`。

1. 藉由執行下列命令來編輯檔案 `/etc/opt/microsoft/omsagent/proxy.conf`，並將值變更為您的特定設定。

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. 執行下列命令來重新啟動代理程式： 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>收集事件和效能資料
Log Analytics 可以從 Linux Syslog 收集事件，和收集您指定要用於較長期分析和報告的效能計數器，並在偵測到特定條件時採取動作。  請遵循下列步驟來開始設定收集 Linux Syslog 的事件以及數個常用的效能計數器。  

1. 選取 [Syslog]。  
2. 您可以輸入記錄檔的名稱，來新增事件記錄檔。  輸入 **Syslog**，然後按一下加號 **+**。  
3. 在表格中，取消選取 [資訊]、[注意] 和 [偵錯] 嚴重性。 
4. 按一下頁面頂端的 [儲存] 來儲存設定。
5. 選取 [Linux 效能資料] 以啟用收集 Windows 電腦上的效能計數器。 
6. 當您第一次為新的 Log Analytics 工作區設定 Linux 效能計數器時，系統會提供選項，讓您快速建立數個常用的計數器。 這些計數器旁邊皆會列出核取方塊。<br><br> ![選取的預設 Windows 效能計數器](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png)。<br><br> 按一下 [新增選定的效能計數器]。  隨即會新增且收集取樣間隔時間的預設值為 10 秒。  
7. 按一下頁面頂端的 [儲存] 來儲存設定。

## <a name="view-data-collected"></a>檢視收集的資料
既然您已啟用資料收集，現在即可執行簡單的記錄搜尋範例，以查看來自目標電腦的一些資料。  

1. 在 Azure 入口網站中，瀏覽至 Log Analytics 並選取稍早建立的工作區。
2. 按一下 [記錄搜尋] 圖格，然後在 [記錄搜尋] 窗格的查詢欄位中輸入 `Perf`，再按 Enter 鍵，或按一下查詢欄位右邊的搜尋按鈕。<br><br> ![Log Analytics 記錄搜尋查詢範例](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> 例如，下圖中的查詢會傳回 735 筆效能記錄。<br><br> ![Log Analytics 記錄搜尋結果](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>清除資源
不再需要時，您可以從 Linux 電腦移除代理程式，並刪除 Log Analytics 工作區。  

若要移除代理程式，請執行下列步驟。

1. 將 Linux 代理程式[通用指令碼](https://github.com/Microsoft/OMS-Agent-for-Linux/releases)下載到電腦。
2. 在電腦上使用 *--purge* 引數執行搭售的 .sh 檔案，如此可將代理程式及其組態完全移除。

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

若要刪除工作區，請選取您先前建立的 Log Analytics 工作區，然後在資源頁面上，按一下 [刪除]。<br><br> ![刪除 Log Analytics 資源](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>後續步驟
既然您正在從內部部署 Linux 電腦收集作業和效能資料，即可輕鬆開始針對收集的資料「免費」進行探索、分析及採取行動。  

若要了解如何檢視和分析資料，請繼續進行本教學課程。   

> [!div class="nextstepaction"]
> [在 Log Analytics 中檢視或分析資料](log-analytics-tutorial-viewdata.md)
