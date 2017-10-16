---
title: "Azure Log Analytics 中的 IT 服務管理連接器 | Microsoft Docs"
description: "使用 IT 服務管理連接器，在 Azure Log Analytics 中將 ITSM 工作項目集中監視及管理，並快速解決所有問題。"
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: v-jysur
ms.openlocfilehash: 411d6103852cbf534d3c420d5ea7b2146df5164e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>使用 IT 服務管理連接器 (預覽) 將 ITSM 工作項目集中管理

![IT 服務管理連接器符號](./media/log-analytics-itsmc/itsmc-symbol.png)

IT 服務管理連接器在支援的 IT 服務管理 (ITSM) 產品/服務和 Log Analytics 之間提供雙向整合。  透過此連線，您可以根據 Log Analytics 警示或記錄檔記錄，在 ITSM 產品中建立事件、警示或活動。 連接器也會從 ITSM 產品將諸如事件和變更要求等資料匯入 OMS Log Analytics。

利用 IT 服務管理連接器，您可以：

  - 在選擇的 ITSM 工具中，整合作業警示與事件管理作法。
    - 從 OMS 警示及透過記錄搜尋，在 ITSM 中建立工作項目 (例如警示、事件、活動)。
    - 透過動作群組中的 ITSM 動作，根據 Azure 活動記錄警示建立工作項目。 
  
  - 整合在組織內使用的監視、記錄及服務管理資料。
    - 將 ITSM 工具中的事件和變更要求資料，與 Log Analytics 工作區中的相關記錄資料相互關聯。   
    - 請檢視最上層儀表板，以獲得關於事件、變更要求及受影響系統的概觀。
    - 撰寫 Log Analytics 查詢來深入了解服務管理資料。
      
## <a name="adding-the-it-service-management-connector-solution"></a>新增 IT 服務管理連接器解決方案

使用[從方案庫新增 Log Analytics 解決方案](log-analytics-add-solutions.md)中所述的程序，將 IT 服務管理連接器新增至您的 Log Analytics 工作區。

如您在方案庫中所見的 [IT 服務管理連接器] 圖格︰

![連接器圖格](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

新增成功之後，您會在 [OMS] > [設定] > [連接的來源] 下看到 IT 服務管理連接器。

![ITSMC 已連線](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> 根據預設，IT 服務管理連接器每 24 小時會重新整理一次連線的資料。 若要針對所做的任何編輯或範本更新立即重新整理連線的資料，按一下連線旁顯示的重新整理按鈕。

 ![ITSMC 重新整理](./media/log-analytics-itsmc/itsmc-connection-refresh.png)


## <a name="configuring-the-connection-with-your-itsm-software"></a>設定與 ITSM 軟體的連線

IT 服務管理連接器解決方案支援連線至 **System Center Service Manager**、**ServiceNow**、**Provance** 及 **Cherwell**。 設定與下列各項的連線

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>使用解決方案

在您使用 ITSM 軟體詳細資料設定 IT 服務股臉連接器之後，該連接器會開始從連線的 ITSM 產品/服務收集資料。 根據 ITSM 產品/服務中的事件數量和變更要求，應會在數分鐘內完成初始同步處理。 

> [!NOTE]
> - IT 服務管理連接器解決方案從 ITSM 產品匯入的資料會出現在 Log Analytics 中，作為類型 **ServiceDesk_CL** 的記錄檔記錄。
> - 記錄檔記錄包含名為 **ServiceDeskWorkItemType_s** 的欄位，其從 ITSM 產品匯入的兩種資料，事件或變更要求

## <a name="data-synced-from-itsm-product"></a>從 ITSM 產品同步的資料
事件和變更要求會從 ITSM 產品同步處理到 Log Analytics 工作區。 下列資訊說明 IT 服務管理連接器所收集之資料的範例︰

> [!NOTE]
> 根據匯入 Log Analytics 的工作項目類型，**ServiceDesk_CL** 會包含下列欄位︰

**工作項目︰****事件**  
ServiceDeskWorkItemType_s="Incident"

**欄位**

- ServiceDeskConnectionName
- 服務台識別碼
- State
- 急迫性
- 影響
- 優先順序
- 升級
- 建立者
- 解決者
- 關閉者
- 來源
- 指派對象
- 類別
- 課程名稱
- 說明
- 建立日期
- 關閉日期
- 解決日期
- 上次修改日期
- 電腦


**工作項目︰****變更要求**

ServiceDeskWorkItemType_s="ChangeRequest"

**欄位**
- ServiceDeskConnectionName
- 服務台識別碼
- 建立者
- 關閉者
- 來源
- 指派對象
- Title
- 類型
- 類別
- State
- 升級
- 衝突狀態
- 急迫性
- 優先順序
- 風險
- 影響
- 指派對象
- 建立日期
- 關閉日期
- 上次修改日期
- 要求日期
- 計劃開始日期
- 計劃結束日期
- 工作開始日期
- 工作結束日期
- 說明
- 電腦

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 事件的輸出資料

| OMS 欄位 | ITSM 欄位 |
|:--- |:--- |
| ServiceDeskId_s| 數字 |
| IncidentState_s | State |
| Urgency_s |急迫性 |
| Impact_s |影響|
| Priority_s | 優先順序 |
| CreatedBy_s | 開啟者 |
| ResolvedBy_s | 解決者|
| ClosedBy_s  | 關閉者 |
| Source_s| 連絡人型別 |
| AssignedTo_s | 指派對象  |
| Category_s | 類別 |
| Title_s|  簡短說明 |
| Description_s|  注意事項 |
| CreatedDate_t|  已開啟 |
| ClosedDate_t| 關閉|
| ResolvedDate_t|已解決|
| 電腦  | 設定項目 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 變更要求的輸出資料

| OMS 欄位 | ITSM 欄位 |
|:--- |:--- |
| ServiceDeskId_s| 數字 |
| CreatedBy_s | 要求者 |
| ClosedBy_s | 關閉者 |
| AssignedTo_s | 指派對象  |
| Title_s|  簡短說明 |
| Type_s|  類型 |
| Category_s|  類別 |
| CRState_s|  State|
| Urgency_s|  急迫性 |
| Priority_s| 優先順序|
| Risk_s| 風險|
| Impact_s| 影響|
| RequestedDate_t  | 要求的日期 |
| ClosedDate_t | 關閉日期 |
| PlannedStartDate_t  |     計劃開始日期 |
| PlannedEndDate_t  |   計劃結束日期 |
| WorkStartDate_t  | 實際開始日期 |
| WorkEndDate_t | 實際結束日期|
| Description_s | 說明 |
| 電腦  | 設定項目 |

**ITSM 資料的範例 Log Analytics 畫面︰**

![Log Analytics 畫面](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>IT 服務管理連接器 – 與其他 OMS 解決方案整合

IT 服務管理連接器目前支援與服務對應解決方案整合。

服務對應可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。 如果您想到您的伺服器，ADM 可讓您以互連系統 (提供重要服務) 的形式檢視它們。 不需要進行任何設定，只要安裝了代理程式，服務對應就會顯示橫跨任何 TCP 連接架構的伺服器、處理程序和連接埠之間的連接。 詳細資訊︰[服務對應](../operations-management-suite/operations-management-suite-service-map.md)。

如果您也使用服務對應解決方案，則可檢視 ITSM 解決方案中建立的服務台項目，如下列範例中所示︰

![ServiceMap 整合](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>建立 OMS 警示的 ITSM 工作項目

在使用了 ITSM 連接器解決方案之後，您可以下列方式來設定 OMS 警示，觸發在您連線的 ITSM 工作中建立工作項目：

1. 從 [記錄搜尋] 視窗中，執行記錄搜尋查詢來檢視資料。 查詢結果為工作項目的來源。
2. 在 [記錄搜尋] 中，按一下 [警示] 可開啟 [新增警示規則] 頁面。

    ![Log Analytics 畫面](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. 在 **[新增警示規則]** 視窗中，提供 **[名稱]** 、 **[嚴重性]** 、 **[搜尋查詢]** 和 **[警示準則]** \(時間範圍/計量的度量單位) 的必要詳細資料。
4. 在 [ITSM 動作] 選取 [是]。
5. 從 [選取連線] 清單中選取您的 ITSM 連線。
6. 提供所需的詳細資料。
7. 若要為這項警示的每個記錄項目建立個別的工作項目，請選取 [為每個記錄項目建立個別的工作項目] 核取方塊。

    或

    將此核取方塊取消選取，可在此警示下對任意數目的記錄項目只建立一個工作項目。

7. 按一下 [儲存] 。

您可以在 [設定]>[警示]下方，看到所建立的 OMS 警示。 符合所指定警示的條件時，會建立對應 ITSM 連線的工作項目。

## <a name="create-itsm-work-items-from-oms-logs"></a>從 OMS 記錄建立 ITSM 工作項目

您也可以下列方式，直接從記錄檔記錄在連線的 ITSM 來源中建立工作項目：

1. 從 [記錄搜尋] 中，搜尋必要的資料、選取詳細資料，然後按一下 [建立工作項目]。

    [建立 ITSM 工作項目] 視窗隨即出現︰

    ![Log Analytics 畫面](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   新增下列詳細資料︰

  - **工作項目標題**︰工作項目的標題。
  - **工作項目描述**︰新工作項目的描述。
  - **受影響電腦**︰找到此記錄資料的電腦之名稱。
  - **選取連線**：您要在其中建立此工作項目的 ITSM 連線。
  - **工作項目**︰工作項目的類型。

3. 若要使用事件的現有工作項目範本，請在 根據範本產生工作項目 選項下按一下 是，然後按一下建立。

    或者，

    如果您想要提供自訂的值，請按一下 [否]。

4. 在 連絡人類型、影響、急迫性、類別 和 子類別 文字方塊中提供適當的值，然後按一下建立。

## <a name="create-itsm-work-items-from-azure-alerts"></a>從 Azure 警示建立 ITSM 工作項目
ITSM 連接器現在可與動作群組整合。 [動作群組](../monitoring-and-diagnostics/monitoring-action-groups.md)提供以模組化和可重複使用的方式來針對 Azure 警示觸發動作。 動作群組中的 ITSM 動作會使用現有的 ITSM 連接器解決方案，在 ITSM 產品中建立工作項目。

1. 在 Azure 入口網站中，按一下 [監視]
2. 在左窗格中，按一下 [動作群組]

    ![動作群組](media/log-analytics-itsmc/ActionGroups.png)

3. 提供動作群組的 [名稱] 與 [簡稱]。 選取您要在其中建立動作群組的 [資源群組] 與 [訂用帳戶]。

    ![動作群組詳細資料](media/log-analytics-itsmc/ActionGroupsDetail.png)

4. 在 [動作] 清單中，從 [動作類型] 的下拉式清單選取 [ITSM]。 提供動作的 [名稱]，然後按一下 [編輯詳細資料]。


5. 選取 Log Analytics 工作區所在位置的 [訂用帳戶]。 選取 [連線]，也就是 ITSM 連接器的名稱，後面接著工作區的名稱。 例如，"MyITSMMConnector(MyWorkspace)"。

    ![ITSM 動作詳細資料](./media/log-analytics-itsmc/ITSMActionDetails.png)

6. 從下拉式清單選取 [工作項目] 類型。
7. 選擇使用現有範本，或填滿 ITSM 產品所需的欄位。
8. 按一下 [檔案] &gt; [新增] &gt; [專案] 

建立/編輯 Azure 警示規則時，請使用其中有 ITSM 動作的動作群組。 警示觸發時，會在 ITSM 工具中建立工作項目。 

>[!NOTE]
>目前只有活動記錄警示支援 ITSM 動作。 對於其他 Azure 的警示，這是無作業動作。
>


## <a name="troubleshoot-itsm-connections-in-oms"></a>針對 OMS 中的 ITSM 連線進行疑難排解
1.  如果從已連線的來源 UI 連線失敗，並顯示 [儲存連線時發生錯誤] 訊息，請執行下列步驟：
 - 對於 ServiceNow、 Cherwell 和 Provance 連線，
    - 請確定您已正確輸入每個連線的使用者名稱、密碼、用戶端識別碼及用戶端密碼。
    - 請檢查您在對應的 ITSM 產品中是否擁有足夠權限來進行連線。
 - 對於服務管理連線，
     - 請確定已成功部署 Web 應用程式，且已建立混合式連線。 若要確認是否已成功與內部部署 Service Manager 機器建立連線，請瀏覽 Web 應用程式 URL，如文件中針對建立[混合式連線](log-analytics-itsmc-connections.md#configure-the-hybrid-connection)所述。
     
2.  如果 ServiceNow 的資料未同步處理至 Log Analytics，請確定 ServiceNow 執行個體並非處在睡眠中。 ServiceNow 開發人員執行個體閒置很長的時間時，有時會進入睡眠狀態。 否則，請回報問題。
3.  如果引發 OMS 警示，但未在 ITSM 產品中建立工作項目，或未將設定項目建立為/連結至工作項目，或需任何其他一般資訊，請查看下列位置：
 -  **IT 服務管理連接器解決方案**：解決方案可顯示連線/工作項目/電腦等的摘要。按一下顯示 [連接器狀態] 的圖格，這會利用相關的查詢帶您前往 [記錄搜尋]。 如需詳細資訊，請查看 ERROR 為 LogType_S 的記錄檔記錄。
 - 或者，使用查詢 *Type=ServiceDeskLog_CL* 直接在 [記錄搜尋] 頁面中檢視錯誤/相關資訊。

## <a name="troubleshoot-service-manager-web-app-deployment"></a>針對 Service Manager Web 應用程式部署進行疑難排解
1.  如果您碰到有關 Web 應用程式部署的問題，請確定您在所述的訂用帳戶中擁有足夠權限來建立/部署資源。
2.  當您執行[指令碼](log-analytics-itsmc-service-manager-script.md)時，如果收到「物件參考未設定為物件的執行個體」錯誤訊息，請確定您已在 [使用者設定] 區段中輸入有效值。
3.  如果您無法建立服務匯流排轉送命名空間，請確定所需的資源提供者已在訂用帳戶中註冊。 如果未註冊，請以手動方式從 Azure 入口網站建立服務匯流排轉送命名空間。 您也可以在建立它時，同時從 Azure 入口網站[建立混合式連線](log-analytics-itsmc-connections.md#configure-the-hybrid-connection)。


## <a name="contact-us"></a>與我們連絡

如需關於 IT 服務管理連接器的任何查詢或意見反應，請與我們連絡：[omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com)。

## <a name="next-steps"></a>後續步驟
[將 ITSM 產品/服務新增至 IT 服務管理連接器](log-analytics-itsmc-connections.md)。
