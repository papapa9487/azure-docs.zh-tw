---
title: "什麼是 Azure Analysis Services | Microsoft Docs"
description: "了解 Azure 中的 Analysis Services 全貌。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: c6be396f22ee364e7746038b2243162e775c8c54
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2017
---
# <a name="what-is-azure-analysis-services"></a>什麼是 Azure Analysis Services？
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services 在雲端提供企業級資料模型化。 這是完全受管理的平台即服務 (PaaS)，與 Azure 資料平台服務整合。 

使用 Analysis Services，您可以混搭和結合來自多個來源的資料、定義計量，以及在單一受信任的語意資料模型中保護您的資料。 資料模型提供更方便、更快速的方式，以供使用者透過用戶端應用程式 (例如 Power BI、Excel、Reporting Services、第三方和自訂應用程式) 瀏覽大量資料。

![資料來源](./media/analysis-services-overview/aas-overview-data-sources.png)

觀看[此影片](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4)以了解 Azure Analysis Services 如何配合 Microsoft 的整體 BI 功能，以及您如何才能受惠於將資料模型置入雲端。

## <a name="built-on-sql-server-analysis-services"></a>建置在 SQL Server Analysis Services 上
Azure Analysis Services 與 SQL Server Analysis Services Enterprise Edition 中現有的許多優質功能相容。 Azure Analysis Services 支援 1200 和 1400 [相容性層級](analysis-services-compat-level.md)的表格式模型。 磁碟分割、資料列層級安全性、雙向關聯性和轉譯都有支援。 記憶體內部和 DirectQuery 模式表示可以快速查詢大量且複雜的資料集。

表格式模型可供快速進行開發，並可高度自訂。 對於開發人員而言，表格式模型包含可說明模型物件的表格式物件模型 (TOM)。 TOM 會經由 [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) 命名空間透過[表格式模型指令碼語言 (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)和 AMO 資料定義語言以 JSON 公開。

## <a name="better-with-azure"></a>透過 Azure 獲得較佳效能
Azure Analysis Services 與許多 Azure 服務整合，讓您能建置複雜的分析解決方案。 與 [Azure Active Directory](../active-directory/active-directory-whatis.md) 整合可對重要資料提供安全的角色型存取。 納入可將資料載入模型中的活動，進而與 [Azure Data Factory](../data-factory/introduction.md) 管線整合。 [Azure 自動化](../automation/automation-intro.md)和 [Azure Functions](../azure-functions/functions-overview.md) 可用於使用自訂程式碼之模型的輕量型協調流程。

## <a name="get-up-and-running-quickly"></a>快速啟動並執行
您可以在短短幾分鐘內，於 Azure 入口網站中[建立伺服器](analysis-services-create-server.md)。 此外，若是使用 Azure Resource Manager [範本](../azure-resource-manager/resource-manager-create-first-template.md)和 PowerShell，您可以使用宣告式範本來佈建伺服器。 在單一範本中，您可以部署多項服務以及其他 Azure 元件 (例如儲存體帳戶和 Azure Functions)。 

建立伺服器後，您可以在 Azure 入口網站中建立表格式模型。 您可以使用新的 (預覽) [網頁設計工具功能](analysis-services-create-model-portal.md)，連線到 Azure SQL Database、Azure SQL 資料倉儲資料來源或匯入 Power BI Desktop .pbix 檔案。 系統會自動建立資料表之間的關聯性，而您可以從瀏覽器建立量值或編輯 json 格式的 model.bim 檔案。

## <a name="scale-to-your-needs"></a>調整您的需求
Azure Analysis Services 會以開發人員、基本及標準層提供。 每一層中的計劃成本會依據處理能力、QPU 及記憶體大小而有所不同。 當您建立伺服器時，可以選取一個層級內的計劃。 您可以在同一層內上下調動計劃，或升級為較高層，但是無法從較高層降級為較低層。

相應增加、相應減少或暫停您的伺服器。 使用 Azure 入口網站或使用 PowerShell 即時完全掌控各種狀況。 您只需依據使用量付費。 若要深入了解不同的計劃和層級，並使用價格計算機來決定適合您的計劃，請參閱 [Azure Analysis Services 價格](https://azure.microsoft.com/pricing/details/analysis-services/)。

## <a name="keep-your-data-close"></a>將您的資料放在鄰近區域
您可以在下列 [Azure 區域](https://azure.microsoft.com/regions/)中建立 Azure Analysis Services 伺服器：

| 美洲 | 歐洲 | 亞太地區 |
|----------|--------|--------------|
|  巴西南部<br> 加拿大中部<br> 美國東部 2<br> 美國中北部<br> 美國中南部<br> 美國中西部<br> 美國西部 | 北歐<br> 英國南部<br> 西歐 |   澳大利亞東南部<br> 日本東部<br> 東南亞<br> 印度西部  |

區域一直都在新增中，所以這份清單可能不完整。 在 Azure 入口網站中或使用 Azure Resource Manager 範本建立伺服器時，請選擇一個位置。 若要獲得最佳效能，請選擇最靠近最大使用者群的位置。 在多個區域中的備援伺服器上部署您的模型，以確保[高可用性](analysis-services-bcdr.md)。

## <a name="migrate-your-existing-tabular-models"></a>移轉現有的表格式模型
如果您已經有現有的內部部署 SQL Server Analysis Services 模型方案， 可以在沒有重大變更的情況下移轉至 Azure Analysis Services。 若要移轉，您可以使用 SSDT 將模型部署到您的伺服器。 也可以在 SSMS 中使用備份和還原或 TMSL。

如果您有內部部署資料來源，請必須安裝和設定[內部部署資料閘道](analysis-services-gateway.md)。 如果您已經設定角色和角色成員，您的角色會移轉，但您必須使用 SSMS 或 PowerShell 讀取角色成員。

## <a name="connect-to-popular-data-sources"></a>連線到熱門資料來源
Azure Analysis Services 支援連線到您的組織和雲端中內部部署[資料來源](analysis-services-datasource.md)。 結合內部部署和雲端資料來源的資料，可成就混和式解決方案。 

新的表格式 1400 模型會依據 M 公式查詢語言，在 SSDT 中使用新式 Get Data 功能。 使用 Get Data，您有更多資料轉換和混搭功能，而且能夠建立和編輯自己的進階 M 公式語言查詢。 比方說，對於表格式 1400 模型，您可以在 Azure Blob 儲存體中將資料檔案模型化。

## <a name="use-the-tools-you-already-know"></a>使用您已知的工具

![BI 開發人員工具](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>適用於 Visual Studio 的 SQL Server Data Tools (SSDT)
包含免費 [適用於 Visual Studio 的 SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) 的開發和部署模型。 SSDT 包含可讓您快速啟動並執行的 Analysis Services 專案範本。 SSDT 現在包含適用於表格式 1400 模型的新式 Get Data 資料來源查詢和混搭功能。 如果您已熟悉 Power BI Desktop 和 Excel 2016 中的 Get Data，您就已經知道建立高度自訂的資料來源查詢有多輕鬆。

#### <a name="sql-server-management-studio"></a>Sql Server Management Studio
使用 [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 來管理伺服器及建立資料庫模型。 連線到您在雲端中的伺服器。 從 XMLA 查詢視窗執行 TMSL 指令碼，並使用 TMSL 指令碼自動執行工作。 快速提供新特性與功能 - 每個月更新 SSMS。

#### <a name="powershell"></a>PowerShell
建立伺服器、暫停或繼續伺服器作業或變更服務層級 (層) 的伺服器資源管理工作會使用 Azure Resource Manager (AzureRM) Cmdlet。 其他用於管理資料庫的工作 (例如新增或移除角色成員、處理或執行 TMSL 指令碼) 會使用 SqlServer 模組中的 Cmdlet。 在 [PowerShell 資源庫](https://www.powershellgallery.com/)中可取得 AzureRM 和 SQLServer 模組。


## <a name="your-data-is-secure"></a>您的資料很安全
![資料視覺效果](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>驗證
Azure Analysis 服務的使用者驗證由 [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) 處理。 嘗試登入 Azure Analysis Services 資料庫時，使用者會使用具有他們嘗試存取之資料庫的存取權的組織帳戶身分識別。 這些使用者身分識別必須是 Azure Analysis Services 伺服器所在之預設 Azure Active Directory 目錄的訂用帳戶成員。 若要深入了解，請參閱[驗證和使用者權限](analysis-services-manage-users.md)。

#### <a name="data-security"></a>資料安全性
Azure Analysis Services 利用 Azure Blob 儲存體來保留 Analysis Services 資料庫的儲存體和中繼資料。 Blob 內的資料檔案是使用 Azure Blob 伺服器端加密 (SSE) 來加密。 使用直接查詢模式時，只會儲存中繼資料。 查詢時會從資料來源存取實際資料。

#### <a name="on-premises-data-sources"></a>內部部署資料來源
透過安裝和設定[內部部署資料閘道](analysis-services-gateway.md)，即可安全地存取您組織中的內部部署資料。 閘道提供直接查詢和記憶體內部模式的資料存取。 當 Azure Analysis Services 模型連接至內部部署資料來源時，會建立一個查詢，以及內部部署資料來源的加密認證。 閘道雲端服務會分析該查詢，並將要求推送至 Azure 服務匯流排。 內部部署閘道會輪詢 Azure 服務匯流排是否有待處理的要求。 閘道接著收到查詢、解密認證，並連接至資料來源來執行。 結果會從資料來源傳送回閘道，然後再到 Azure Analysis Services 資料庫。

Azure Analysis Services 受 [Microsoft Online Services 條款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)和 [Microsoft Online Services 隱私權聲明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)。
若要深入了解「Azure 資訊安全」，請參閱 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/Security/AzureSecurity)。

## <a name="supports-the-latest-client-tools"></a>支援最新的用戶端工具
![資料視覺效果](./media/analysis-services-overview/aas-overview-clients.png)

新式資料探索和視覺效果工具 (像是 Power BI、Excel 和第三方工具) 可讓使用者以高度互動和豐富的視覺化方式，深入了解您的模型資料。

用戶端可使用 MSOLAP、AMO 或 ADOMD [用戶端程式庫](analysis-services-data-providers.md)來連線到 Analysis Services 伺服器。 Microsoft 用戶端應用程式 (像是 Power BI Desktop 和 Excel) 會安裝這三個用戶端程式庫。 但請記住，用戶端程式庫可能不是 Azure Analysis Services 所需的最新版本，需依更新的版本或頻率而定。 同樣適用於自訂應用程式或其他介面，例如 AsCmd、TOM、ADOMD.NET。 這些應用程式通常需要以手動方式將程式庫安裝為套件的一部分。


## <a name="get-help"></a>取得說明

#### <a name="documentation"></a>文件
設定和管理 Azure Analysis Services 非常容易。 您可以在此找到建立和管理伺服器服務所需要的全部資訊。 建立要部署到您的伺服器的資料模型時，就像是要建立您部署到內部部署伺服器的資料模型一樣。 [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services) 有一個內容廣泛的程式庫，包含概念、程序、教學課程及參考文件。

#### <a name="videos"></a>影片
觀看 [Channel 9 上有關 Azure Analysis Services](https://channel9.msdn.com/series/Azure-Analysis-Services) 的實用影片。

#### <a name="blogs"></a>部落格
事情變化得很快。 您隨時可以在 [Azure Analysis Services 團隊部落格](https://blogs.msdn.microsoft.com/analysisservices/)和 [Azure 部落格](https://azure.microsoft.com/blog/)上找到最新資訊。

#### <a name="community"></a>社群
Analysis Services 有活躍的使用者社群。 在 [Azure Analysis Services 論壇](https://aka.ms/azureanalysisservicesforum)的對話。

## <a name="feedback"></a>意見反應
您有任何建議或功能要求嗎？ 請務必在 [Azure Analysis Services 意見反應](https://aka.ms/azureanalysisservicesfeedback)留下您的意見。

您對文件有何建議嗎？ 您可以使用每篇文章下方的 Livefyre 新增留言。

## <a name="next-steps"></a>後續步驟
在深入了解後，您現在可以開始使用 Azure Analysis Services。 了解如何在 Azure 中[建立伺服器](analysis-services-create-server.md)。 當您的伺服器準備就緒時，逐步執行 [Adventure Works 教學課程](tutorials/aas-adventure-works-tutorial.md)以了解如何建立功能完整的表格式模型，並將它部署到您的伺服器。
