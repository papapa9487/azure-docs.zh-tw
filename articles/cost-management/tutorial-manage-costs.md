---
title: "使用 Azure 成本管理來管理成本 | Microsoft Docs"
description: "使用成本配置，以及回報和退款報表來管理成本。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/06/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 83ddc0cb4227235069b0027a24a52f4d8e818126
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="manage-costs-by-using-azure-cost-management"></a>使用 Azure 成本管理來管理成本

在 Cloudyn 的 Azure 成本管理中，您會透過以標記為基礎來配置成本的方式，來管理成本並產生回報報表。 成本配置的程序會將成本指派給已使用的雲端資源。 當所有的資源都搭配標記完成分類之後，成本便會被完全配置。 在成本完成配置之後，您可以透過儀表板和報表為使用者提供回報或退款。 不過，當您開始使用成本管理時，有許多資源可能會被取消標記或是無法標記。

例如，您可能會想要取得工程成本上的補償。 您必須能根據資源成本，向工程團隊顯示您所需的特定金額。 您可以向他們顯示包含所有標記為*工程*之已使用資源的報表。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用自訂標記來配置成本。
> * 建立回報和退款報表。

## <a name="use-custom-tags-to-allocate-costs"></a>使用自訂標記來配置成本

當您開始進行成本配置時，必須先使用成本模型來定義範圍。 成本模型並不會變更成本，而是會分配它們。 當您建立成本模型時，會依成本實體、帳戶或訂用帳戶，並透過多個標記將資料分割。 常見的範例標記可能會包含帳單代碼、成本中心或群組名稱。 標記也能協助針對組織的其他部分執行回報或退款。

若要建立自訂的成本配置模型，請選取報表功能表上的 [Cost] \(成本\) &gt; [Cost Management] \(成本管理\) &gt; [Cost Allocation 360°] \(成本配置 360°\)。

![選取 [Cost Allocation 360] \(成本配置 360\)](./media/tutorial-manage-costs/cost-allocation-360.png)

在 [Cost Allocation 360] \(成本配置 360\) 頁面上，選取 [Add] \(新增\)，然後輸入成本模型的名稱和描述。 您可以選取所有的帳戶，或是個別的帳戶。 如果您想要使用個別的帳戶，便可以選取來自多個雲端服務提供者的多個帳戶。 接下來，按一下 [Categorization] \(分類\) 來選擇能分類您成本資料的已探索標記。 選擇您想要包含在模型中的標記 (類別)。 下例中選取了 [Unit] \(單位\) 標記。

![範例成本模型分類](./media/tutorial-manage-costs/cost-model01.png)



範例顯示有 $14,444 美元是沒有分類的 (沒有標記)。

接下來，選取 [Uncategorized Resources] \(未分類的資源\)，然後選取具有未配置成本的服務。 接著，定義規則以配置成本。

例如，您可能會想要將 Azure 儲存體的成本平均分配給 Azure 虛擬機器 (VM)。 若要這麼做，請選取 [Azure/Storage] \(Azure/儲存體\) 服務，選取 [Proportional to Categorized] \(與分類成比例\)，然後選取 [Azure/VM]。 然後，選取 [Create] \(建立\)。

![平均分配的範例成本模型配置規則](./media/tutorial-manage-costs/cost-model02.png)



在不同的範例中，您可能會想要將所有的 Azure 網路成本配置給組織中特定的營業單位。 若要這麼做，請選取 [Azure/Network] \(Azure/網路\) 服務，然後選取 [Explicit Distribution] \(明確分配\)。 接著，將分配百分比設為 100，並選取下列影像中的營業單位 [**G&amp;A**]：

![特定營業單位的範例成本模型配置規則](./media/tutorial-manage-costs/cost-model03.png)



針對所有剩餘的未分類資源，請建立額外的配置規則。

如果您有任何未配置的 Amazon Web Services (AWS) 保留的執行個體，便可以將它們指派給具有**保留的執行個體**的已標記類別。

若要檢視您在配置成本上所做之選擇的相關資訊，請選取 [Summary] \(摘要\)。 若要儲存資訊並於稍後繼續處理其他規則，請選取 [Save As Draft] \(儲存為草稿\)。 或者，若要儲存資訊並讓 Cloudyn 開始處理您的成本配置模型，請選取 [Save and Activate] \(儲存並啟動\)。

成本模型清單會將您的新成本模型顯示為處於 [Processing] \(正在處理\) 的狀態。 Cloudyn 資料庫可能需要一些時間才會更新為具有您的成本模型。 處理完成之後，狀態將會更新為 [Completed] \(已完成\)。 您可以在 [Extended Filters] \(擴充篩選\) &gt; [Cost Model] \(成本模型\) 底下的 [Cost Analysis] \(成本分析\) 報表中，檢視來自您成本模型的資料。

### <a name="category-manager"></a>類別管理員

類別管理員是可以協助您合併多個類別 (標記) 的值以建立新類別的資料清理工具。 它是一個簡易的規則型工具，可讓您選取類別並建立規則以合併現有的值。 例如，您可能會有針對 **R&amp;D** 和 **dev** 的現有類別，而這兩個類別都代表開發群組。

在 Cloudyn 入口網站中，按一下右上角的齒輪符號並選取 [Category Manager] \(類別管理員\)。 若要建立新類別，請選取加號 (**+**)。 輸入類別的名稱，然後在 [Keys] \(索引鍵\) 底下，輸入要包含在新類別中的類別索引鍵。

定義規則時，您可以使用 OR 條件來新增多個值。 您也可以執行某些基本的字串作業。 針對上述任一案例，請按一下位於 [Rule] \(規則\) 右側的省略符號 (**…**)。

若要定義新的規則，請在 [Rules] \(規則\) 區域建立新的規則。 例如，在 [Rules] \(規則\) 底下輸入 **dev**，然後在 [Actions] \(動作\) 底下輸入 **R&amp;D**。 完成後，請儲存新的類別。

下列影像顯示針對名為 **Work-Load** 的新類別所建立之規則的範例：

![範例類別](./media/tutorial-manage-costs/category01.png)



## <a name="create-showback-and-chargeback-reports"></a>建立回報和退款報表

不同的組織用來執行回報和退款的方法皆有著極大的差異。 不過，您可以使用 Cloudyn 入口網站中的任何儀表板和報表，作為上述任一用途的基礎。 您可以將使用者存取權提供給組織中的任何人員，使他們可以視需要檢視儀表板和報表。 所有的 [Cost Analysis] \(成本分析\) 報表皆支援回報，因為它們能向使用者顯示他們所使用的資源。 接著，它們能允許使用者向下鑽研至其群組於您組織中的特定成本或使用量資料。

若要檢視成本配置的結果，請開啟 [Cost Analysis] \(成本分析\) 報表，並選取您所建立的成本模型。 接著，依據一或多個於成本模型中選取的標記來新增群組。

![[Cost Analysis] \(成本分析\) 報表](./media/tutorial-manage-costs/cost-analysis.png)

您可以輕鬆建立並儲存著重於由特定群組所使用之特定服務的報表。 例如，您可能有一個會廣泛使用 Azure VM 的部門。 您可以建立針對 Azure VM 進行篩選的報表，以顯示使用情況和成本。

如果您需要為其他團隊提供快照集資料，則可以將任何報表匯出為 PDF 或 CSV 格式。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用自訂標記來配置成本。
> * 建立回報和退款報表。



若要深入了解 Cloudyn 入門及其功能的使用方式，請參閱 Cloudyn 文件。

> [!div class="nextstepaction"]
> [Cloudyn 文件](https://support.cloudyn.com/hc/)
