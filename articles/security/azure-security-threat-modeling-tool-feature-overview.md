---
title: "Microsoft 威脅模型化工具 - Azure | Microsoft Docs"
description: "深入了解威脅模型化工具中可用的所有功能"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 621ff305d7e782f85eeaae6c3fb02031673549c6
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---

# <a name="threat-modeling-tool-feature-overview"></a>威脅模型化工具功能概觀

我們很高興您針對您的威脅模型化需求選擇使用威脅模型化工具！ 如果您尚未這麼做，請造訪**[威脅模型化工具使用者入門](./azure-security-threat-modeling-tool-getting-started.md)**以了解基本概念。

> 我們的工具會經常更新，因此請經常檢查本指南，以查看我們的最新功能和增強功能。

按一下 [建立新模型] 按鈕，空白起始分頁隨即開啟，類似以下映像：

![空白起始分頁](./media/azure-security-threat-modeling-tool/tmtstart.png)

使用我們的小組在**[使用者入門](./azure-security-threat-modeling-tool-getting-started.md)**範例中建立的威脅模型，讓我們馬上查看工具中的所有功能。

![基本威脅模型](./media/azure-security-threat-modeling-tool/basictmt.png)

## <a name="navigation"></a>導覽

在深入探究內建功能之前，看一下在工具中找到的主要元件

### <a name="menu-items"></a>功能表項目

體驗應該類似於其他 Microsoft 產品。 讓我們從最上層功能表項目開始：

![功能表項目](./media/azure-security-threat-modeling-tool/menuitems.png)

| 標籤                               | 詳細資料      |
| --------------------------------------- | ------------ |
| **檔案** | <ul><li>開啟、儲存並關閉檔案</li><li>登入/登出 OneDrive 帳戶</li><li>共用連結 (檢視 + 編輯)</li><li>檢視檔案資訊</li><li>將新的範本套用至現有的模型</li></ul> |
| **編輯** | 復原/重做動作，以及複製、貼上和刪除 |
| **檢視** | <ul><li>在**分析**和**設計**檢視之間切換</li><li>開啟已關閉的視窗 (例如，樣板、元素屬性和訊息)</li><li>將版面配置重設為預設設定</li></ul> |
| **圖表** | 新增/刪除圖表並且瀏覽圖表的「索引標籤」 |
| **報告** | 建立要與其他人共用的 HTML 報告 |
| **說明** | 可協助您使用工具的指南 |

圖示是最上層功能表的捷徑：

| 圖示                               | 詳細資料      |
| --------------------------------------- | ------------ |
| **開啟** | 開啟新的檔案 |
| **儲存** | 儲存目前的檔案 |
| **設計** | 便會進入設計檢視，您可以在其中建立模型 |
| **分析** | 顯示產生的威脅和其屬性 |
| **新增圖表** | 新增圖表 (類似於 Excel 中的新增索引標籤) |
| **刪除圖表** | 刪除目前的圖表 |
| **複製/剪下/貼上** | 複製/剪下/貼上元素 |
| **復原/重做** | 復原/重做動作 |
| **放大/縮小** | 放大和縮小圖表以獲得更清楚的檢視 |
| **意見反應** | 開啟 MSDN 論壇 |

### <a name="canvas"></a>畫布

您將元素拖放至其中的空間。 拖放是建立模型最快且最有效率的方式。 您也可以以滑鼠右鍵按一下，並從功能表中選取，新增您使用的泛型版本元素，如下所示。

#### <a name="dropping-the-stencil-on-the-canvas"></a>在畫布上置放樣板

![畫布置放](./media/azure-security-threat-modeling-tool/canvasdrop1.png)

#### <a name="clicking-on-the-stencil"></a>按一下樣板

![元素屬性](./media/azure-security-threat-modeling-tool/canvasdrop2.png)

### <a name="stencils"></a>樣板

您可以在其中根據選取的範本找到所有可用樣板的位置。 如果找不到適合的元素，請嘗試使用其他範本，或修改某個範本以符合您的需求。 一般而言，您應該能夠找到如下的類別組合：

| 樣板名稱                               | 詳細資料      |
| --------------------------------------- | ------------ |
| **處理程序** | 應用程式、瀏覽器外掛程式、執行緒、虛擬機器 |
| **外部互動者** | 驗證提供者、瀏覽器、使用者、Web 應用程式 |
| **資料存放區** | 快取、儲存體、設定檔、資料庫、登錄 |
| **資料流程** | 二進位、ALPC、HTTP、HTTPS/TLS/SSL、IOCTL、IPSec、具名管道、DCOM、SMB、UDP |
| **信任線條/框線界限** | 公司網路、網際網路、機器、沙箱、使用者/核心模式 |

### <a name="notesmessages"></a>附註/訊息

| 元件                               | 詳細資料      |
| --------------------------------------- | ------------ |
| **訊息** | 每當發生錯誤時 (例如元素之間沒有資料流程) 警示使用者的內部工具邏輯 |
| **注意事項** | 由工程小組在整個設計和檢閱流程中新增至檔案的手動附註 |

### <a name="element-properties"></a>元素屬性

會因選取的元素而異。 除了信任界限，其他所有元素會包含 3 個一般選取項目：

| 元素屬性                               | 詳細資料      |
| --------------------------------------- | ------------ |
| **名稱** | 適用於命名您的流程、存放區、互動者，且流量易於辨識 |
| **超出範圍** | 如果選取，元素會從威脅產生矩陣中移除 (不建議) |
| **超出範圍原因** | 理由欄位，讓使用者知道為什麼選取超出範圍 |

每個元素類別下的屬性都會變更。 按一下每個元素來檢查可用的選項，或開啟範本以深入了解。 讓我們深入功能。

## <a name="welcome-screen"></a>歡迎使用畫面

歡迎畫面是當您開啟應用程式時，看到的第一個畫面。

### <a name="open-a-model"></a>開啟模型

將游標移到 [開啟模型] 按鈕會為您顯示 2 個隱藏選項：[從這部電腦開啟] 和 [從 OneDrive 開啟]。 第一個選項會開啟 [開啟舊檔] 畫面，而第二個選項會引導您進行 OneDrive 的登入程序，讓您在成功驗證之後選取資料夾和檔案。

![開啟模型](./media/azure-security-threat-modeling-tool/openmodel.png)

![從電腦或 OneDrive 開啟](./media/azure-security-threat-modeling-tool/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>意見反應、建議和問題

選取此選項會帶您前往 SDL 工具的 MSDN 論壇。 它是查看其他人對工具有什麼意見 （包括因應措施和新的想法) 的好方法。

![意見反應](./media/azure-security-threat-modeling-tool/feedback.png)

## <a name="design-view"></a>設計檢視

每當您開啟或建立新的模型時，您會進入設計檢視。

### <a name="adding-elements"></a>新增元素

有 2 種方式可以在格線上新增元素：

- **拖放** – 將所需的元素拖曳到格線，然後使用元素屬性來提供其他資訊。
- **按一下滑鼠右鍵** – 以滑鼠右鍵按一下格線上的任何位置，並從下拉式功能表中選取。 該元素的泛型表示法會出現在畫面上。

### <a name="connecting-elements"></a>連線元素

有 2 種方式可以連接工具中的元素：

- **拖放** – 將所需的資料流程拖曳到格線中，然後將兩端連接到適當的元素。
- **按一下 + Shift** – 按一下第一個元素 (傳送資料)，按住 Shift 鍵，然後選取第二個元素 (接收資料)。 按一下滑鼠右鍵，然後選取 [連接]。 如果您使用雙向資料流程，順序並不重要。

### <a name="properties"></a>屬性

顯示可以在放置於圖表中的樣板上修改的所有屬性。 若要查看屬性，只要按一下樣板，資訊就會據以填入。 以下範例會示範「資料庫」樣板拖曳到圖表上的前後：

#### <a name="before"></a>先前

![先前](./media/azure-security-threat-modeling-tool/properties1.png)

#### <a name="after"></a>後續

![後續](./media/azure-security-threat-modeling-tool/properties2.png)

### <a name="messages"></a>訊息

如果您建立威脅模型，但是忘記將資料流程連接到元素，訊息視窗會通知您執行動作。您可以選擇忽略它，或遵循指示以修正問題。 

![訊息](./media/azure-security-threat-modeling-tool/messages.png)

### <a name="notes"></a>注意事項

從 [訊息] 索引標籤切換至 [附註] 索引標籤，可讓您將附註新增至您的圖表，以擷取您的想法

## <a name="analysis-view"></a>分析檢視

一旦您完成建置您的圖表，切換到分析檢視，方法是移至上層功能表選取項目，然後選擇調色盤旁邊的放大鏡。

![分析檢視](./media/azure-security-threat-modeling-tool/analysisview.png)

### <a name="generated-threat-selection"></a>產生的威脅選取項目

當您按一下威脅時，您可以利用三個唯一的函式：

| 功能                               | 資訊      |
| --------------------------------------- | ------------ |
| **讀取指標** | <p>威脅現在已標示為讀取，可以輕鬆地協助您追蹤您已經瀏覽的項目</p><p>![讀取/未讀取指標](./media/azure-security-threat-modeling-tool/readmode.png)</p> |
| **互動焦點** | <p>圖表中屬於該威脅的互動會反白顯示</p><p>![互動焦點](./media/azure-security-threat-modeling-tool/interactionfocus.png)</p> |
| **威脅屬性** | <p>威脅的其他資訊已填入威脅屬性視窗中</p><p>![威脅屬性](./media/azure-security-threat-modeling-tool/threatproperties.png)</p> |

### <a name="priority-change"></a>優先順序變更

變更每個產生的威脅的優先順序層級也會變更其色彩，以便於識別高度、中度與低度優先順序威脅。

![優先順序變更](./media/azure-security-threat-modeling-tool/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>威脅屬性可編輯欄位

如同在以上映像所見，使用者可以變更工具所產生的資訊，也可以將資訊新增至特定欄位，例如理由。 這些欄位是由範本產生，因此如果您需要每個威脅的詳細資訊，我們鼓勵您進行修改。

![威脅屬性](./media/azure-security-threat-modeling-tool/threatproperties.png)

## <a name="reports"></a>報告

一旦您完成變更優先順序，並且更新每個產生之威脅的狀態，您可以儲存檔案和/或列印報告，方法是移至 [報告]，然後移至 [建立完整報告]。 系統會要求您命名報告，一旦您這樣做，您應該會看到類似以下映像的項目：

![報告](./media/azure-security-threat-modeling-tool/report.png)

## <a name="next-steps"></a>後續步驟

若要為社群貢獻範本，請移至我們的 **[GitHub](https://github.com/Microsoft/threat-modeling-templates)** 分頁。 **[下載](https://aka.ms/tmtpreview)**工具並且立即開始使用。

