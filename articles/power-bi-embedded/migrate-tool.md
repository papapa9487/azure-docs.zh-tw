---
title: "使用 Power BI Embedded 移轉工具 | Microsoft Docs"
description: "Power BI Embedded 移轉工具可以用來將報表從 Power BI 工作區集合複製到 Power BI Embedded。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 0b7b5089045daf6dd88fcd84e316b2bd44f8c927
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>使用 Power BI Embedded 移轉工具

Power BI Embedded 移轉工具可以用來將報表從 Power BI 工作區集合複製到 Power BI Embedded。

將內容從工作區集合移轉至 Power BI 服務，可在您目前的方案中以平行方式完成，而且不需任何停機時間。

## <a name="limitations"></a>限制

* 無法下載已推送的資料集，而且將需使用適用於 Power BI 服務的 Power BI REST API 重新建立。
* 您將無法下載在 2016 年 11 月 26 日之前匯入的 PBIX 檔案。

## <a name="download"></a>下載

您可以從 [GitHub](https://github.com/Microsoft/powerbi-migration-sample) \(英文\) 下載移轉工具範例。 您可以下載存放庫的 zip 檔，或者可在本機加以複製。 下載之後，您可以在 Visual Studio 內開啟 powerbi-migration-sample.sln 來建置及執行移轉工具。

## <a name="migration-plans"></a>移轉計劃

您的移轉計劃只是將 Power BI 工作區集合內的內容編目，以及您想要如何將它們發佈至 Power BI Embedded 的中繼資料。

### <a name="start-with-a-new-migration-plan"></a>開始使用新的移轉計劃

移轉計劃是可在您接著想要移至 Power BI Embedded 之 Power BI 工作區集合中使用的項目中繼資料。 移轉計劃會儲存為 XML 檔案。

您會想要從建立新的移轉計劃開始。 若要建立新的移轉計劃，請執行下列動作。

1. 選取 [檔案] > [New Migration Plan] \(新增移轉計劃\)。

    ![新增移轉計劃](media/migrate-tool/migrate-tool-plan.png)

2. 在 [Select Power BI Workspace Collections Resource Group] \(選取 Power BI 工作區集合資源群組\) 對話方塊中，您會想要選取 [Environment] \(環境\) 下拉式清單，然後選取 [prod] (生產環境)。

3. 系統將提示您登入。 您將使用您的 Azure 訂用帳戶登入。

    > [!IMPORTANT]
    > 這**不是**您用以登入 Power BI 的 Office 365 組織帳戶。

4. 選取儲存您 Power BI 工作區集合資源的 Azure 訂用帳戶。

    ![選取您的 Azure 訂用帳戶](media/migrate-tool/migrate-tool-select-resource-group.png)

5. 在訂用帳戶清單下方，選取包含您工作區集合的**資源群組**，然後選取 [選取]。

    ![選取資源群組](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. 選取 [分析]。 這將會取得您 Azure 訂用帳戶中的項目清單，以便開始您的計劃。

    ![選取 [分析] 按鈕](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > 根據工作區集合和工作區集合中有多少內容而定，分析程序可能需要幾分鐘的時間。

7. 當**分析**完成時，它將提示您儲存移轉計劃。

此時，已將移轉計劃連接到您的 Azure 訂用帳戶。 閱讀下列內容，以了解如何使用移轉計劃的流程。 這包括分析與規劃移轉、下載、建立群組及上傳。

### <a name="save-your-migration-plan"></a>儲存您的移轉計劃

您可以儲存移轉計劃，以供後續使用。 這將建立一個 XML 檔案來包含移轉計劃中的所有資訊。

若要儲存您的移轉計劃，請執行下列動作。

1. 選取 [檔案] > [Save Migration Plan] \(儲存移轉計劃\)。

    ![[儲存移轉計劃] 功能表選項](media/migrate-tool/migrate-tool-save-plan.png)

2. 為您的檔案命名或使用產生的檔案名稱，然後選取 [儲存]。

### <a name="open-an-existing-migration-plan"></a>開啟現有的移轉計劃

您可以開啟儲存的移轉計劃，以繼續進行移轉。

若要開啟現有的移轉計劃，請執行下列動作。

1. 選取 [檔案] > [Open Existing Migration Plan] \(開啟現有的移轉計劃\)。

    ![[開啟現有的移轉計劃] 功能表選項](media/migrate-tool/migrate-tool-open-plan.png)

2. 選取您的移轉檔案，然後選取 [開啟]。

## <a name="step-1-analyze-and-plan-migration"></a>步驟 1：分析與規劃移轉

[Analyze & Plan Migration] \(分析與規劃移轉\) 索引標籤可讓您檢視目前在您 Azure 訂用帳戶資源群組中的項目。

![[分析與規劃移轉] 索引標籤](media/migrate-tool/migrate-tool-step1.png)

我們將探討 *SampleResourceGroup* 作為範例。

### <a name="paas-topology"></a>PaaS 拓撲

這是您資源群組 > 工作區集合 > 工作區的清單。 資源群組和工作區集合將會顯示易記名稱。 工作區會顯示 GUID。

清單中的項目也會以 (#/#) 格式顯示色彩和數字。 這表示您可以下載的報表數目。

黑色表示所有可下載的報表。 紅色表示某些無法下載的報表。 左邊的數字將指出可下載的報表總數。 右邊的數字會指出群組中的報表總數。

您可以選取 PaaS 拓撲中的項目，以便在報表區段中顯示報表。

### <a name="reports"></a>報告

報表區段將會列出可用的報表，並指出其是否可供下載。

![Power BI 工作區集合中的報表清單](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>目標結構

您可以在 [Target Structure] \(目標結構\) 中告訴工具，要將項目下載至何處以及如何將它們上傳。

#### <a name="download-plan"></a>下載計劃

系統將會為您自動建立路徑。 您可以視需要變更此路徑。 如果您變更路徑，就必須選取 [Update paths] \(更新路徑\)。

**這並不會實際執行下載。** 這只會指定要將報表下載到其中的結構。

#### <a name="upload-plan"></a>上傳計劃

您可以在這裡指定要用於應用程式工作區的前置詞，這些工作區將建立 Power BI 服務中。 之後，前置詞將成為存在於 Azure 之工作區的 GUID。

![指定群組名稱前置詞](media/migrate-tool/migrate-tool-upload-plan.png)

**這實際上不會在 Power BI 服務中建立群組。** 這只會定義群組的命名結構。

如果您變更前置詞，就必須選取 [Generate Upload Plan] \(產生上傳計劃\)。

您可以視需要在群組上按一下滑鼠右鍵，然後選擇在上傳計劃中直接將群組重新命名。

![[將群組重新命名] 快顯功能表選項](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> 「群組」名稱不能包含空格或無效字元。

## <a name="step-2-download"></a>步驟 2：下載

在 [下載] 索引標籤上，您將會看到報表清單及相關聯的中繼資料。 您可以看到匯出狀態以及先前匯出狀態。

![[下載] 索引標籤](media/migrate-tool/migrate-tool-download-tab.png)

您有兩個選擇。

* 選取特定的報表，然後選取 [下載已選項目]
* 選取 [全部下載]。

![[下載已選項目] 按鈕](media/migrate-tool/migrate-tool-download-options.png)

如果成功下載，您將會看到狀態為「完成」，且它將反映出 PBIX 檔案已存在。

下載完成之後，選取 [建立群組] 索引標籤。

## <a name="step-3-create-groups"></a>步驟 3：建立群組

下載可用的報表之後，您可以前往 [建立群組] 索引標籤。此索引標籤將根據您建立的移轉計劃，在 Power BI 服務中建立應用程式工作區。 它將使用您在 [Analyze & Plan Migration] \(分析與規劃移轉\) 的 [上傳] 索引標籤上提供的名稱來建立應用程式工作區。

![[建立群組] 索引標籤](media/migrate-tool/migrate-tool-create-groups.png)

若要建立應用程式工作區，您可以選取 [Create Selected Groups] \(建立選取的群組\) 或 [Create All Missing Groups] \(建立所有遺漏的群組\)。

當您選取這其中一個選項時，系統將提示您登入。 *您將想要針對您希望在其中建立應用程式工作區的 Power BI 服務使用您的認證。*

![Power BI 登入畫面](media/migrate-tool/migrate-tool-create-group-sign-in.png)

這將在 Power BI 服務中建立應用程式工作區。 這不會將報表上傳到應用程式工作區。

您可以藉由登入 Power BI 並驗證工作區已存在，來確認已建立應用程式工作區。 您將會注意到工作區中沒有任何內容。

![Power BI 服務中的應用程式工作區](media/migrate-tool/migrate-tool-app-workspace.png)

建立工作區之後，您可以將它移到 [上傳] 索引標籤。

## <a name="step-4-upload"></a>步驟 4：上傳

在 [上傳] 索引標籤上，這會將報表上傳至 Power BI 服務。 根據您的移轉計劃，您將會看到我們已在 [下載] 索引標籤上下載的報表清單以及目標群組名稱。

![[上傳] 索引標籤](media/migrate-tool/migrate-tool-upload-tab.png)

您可以上傳選取的報表，或者上傳所有報表。 您也可以重設上傳狀態，以便重新上傳項目。

此外，您還可以選擇萬一有相同名稱的報表存在時該怎麼辦。 您可以在 [中止]、[忽略] 和 [覆寫] 之間進行選擇。

![萬一報表存在時該怎麼辦的選項下拉式清單](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![上傳選取的結果](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>重複的報表名稱

如果您有相同名稱的報表，但您知道它是不同的報表，將需變更報表的 **TargetName**。 您可以手動編輯移轉計劃 XML 來變更名稱。

您將需關閉移轉工具以進行變更，然後重新開啟工具和移轉計劃。

在上述範例中，其中一個複製的報表失敗，並指出有相同名稱的報表。 如果我們查看移轉計劃 XML，將會看到下列內容。

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

針對失敗的項目，我們可以變更 SaaSTargetReportName 的名稱。

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

然後在移轉工具中重新開啟計劃，並上傳失敗的報表。

回到 Power BI，我們可以在應用程式工作區中看到已上傳報表和資料集。

![Power BI 服務中的報表清單](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>上傳本機 PBIX 檔案

您可以上傳 Power BI Desktop 檔案的本機版本。 您將需關閉工具、編輯 XML，然後在 **PbixPath** 屬性中放置您本機 PBIX 的完整路徑。

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

編輯 xml 之後，在移轉工具中重新開啟計劃，然後上傳報表。

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>DirectQuery 報表

您將需要更新，以更新 DirectQuery 報表的連接字串。 這可以在 *powerbi.com* 中完成，或者可以程式設計方式從 Power BI Embedded (Paas) 查詢連接字串。 如需範例，請參閱[從 PaaS 報表擷取 DirectQuery 連接字串](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections)。

然後，您可以在 Power BI 服務中更新資料集的連接字串，並設定資料來源的認證。 您可以查看下列範例，以了解如何執行此動作。

* [在 Power BI Embedded 中更新 DirectQuery 連接字串](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [在 Power BI Embedded 中設定 DirectQuery 認證](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>後續步驟

您的報表目前已從 Power BI 工作區集合移轉至 Power BI Embedded，您現在可以更新應用程式，並開始在此應用程式工作區中內嵌報表。

如需詳細資訊，請參閱[如何將 Power BI 工作區集合內容移轉至 Power BI Embedded](migrate-from-power-bi-workspace-collections.md)。

有其他疑問？ [嘗試在 Power BI 社群中提問](http://community.powerbi.com/) \(英文\)