---
title: "移轉：資料倉儲移轉公用程式 | Microsoft Docs"
description: "移轉至 SQL 資料倉儲。"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 4d7ad981-ef31-4513-b337-50bdc4709c09
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: e8a8a84153a950f2d1bc002b34c83dc5ed8a5eb8
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2017
---
# <a name="data-warehouse-migration-utility-preview"></a>資料倉儲移轉公用程式 (預覽)
> [!div class="op_single_selector"]
> * [下載移轉公用程式][Download Migration Utility]
> 
> 

資料倉儲移轉公用程式是一種工具，專門用來將結構描述和資料從 SQL Server 和 Azure SQL Database 移轉至 Azure SQL 資料倉儲。 在結構描述移轉的過程中，此工具會在來源與目的地之間自動對應相應的結構描述。 結構描述完成移轉後，工具提供以自動產生的指令碼移動資料的選項。

除了結構描述和資料移轉之外，此工具還能讓您選擇產生相容性報告，以摘要的形式列出目標和來源執行個體之間可能會妨礙移轉作業的不相容問題。

## <a name="get-started"></a>開始使用
安裝的先決條件是，您必須使用 BCP 命令列公用程式來執行移轉指令碼和 Office，才能檢視相容性報告。 啟動所下載的可執行檔之後，系統會提示您接受標準 EULA，才能繼續安裝此工具。

此外，若要執行移轉公用程式，您在想要移轉的資料庫上需要有下列其中一個權限：CREATE DATABASE、ALTER ANY DATABASE 或 VIEW ANY DEFINITION。

### <a name="launching-the-tool-and-connecting"></a>啟動工具並進行連接
透過按一下於安裝後出現的桌面圖示來啟動工具。 開啟此工具時，畫面會顯示初始連接頁面，提示您選擇移轉工具的來源和目的地。 目前，此工具支援 SQL Server 和 Azure SQL Database 做為來源，SQL 資料倉儲做為目的地。 選取這些項目之後，工具會要求您填寫伺服器名稱並執行驗證程序，然後按一下 [連接]，以連接至您的來源伺服器。

完成驗證後，工具會顯示已連接伺服器中現有的資料庫清單。 您可以選取想要移轉的資料庫，然後按一下 [移轉選取項目]，開始移轉程序。

## <a name="migration-report"></a>移轉報告
在工具中選取 [檢查資料庫相容性] 會產生一份報告，針對您已要求移轉的資料庫，摘要說明所有的物件不相容問題。 未列在 SQL 資料倉儲中的部分 SQL Server 功能清單，可以在我們的[移轉文件][migration documentation]中找到。 報告產生後，您可以在 Excel 中儲存並開啟該報告。

請注意，產生移轉結構描述時，大部分識別為「物件」的問題將會經過調整，以便立即移轉該資料。 請檢閱變更的項目，確保您對所有調整項皆感到滿意後，再套用結構描述。

## <a name="migrate-schema"></a>移轉結構描述
連接後，選取 [移轉結構描述] 會產生所選資料表的結構描述移轉指令碼。 此指令碼可連接資料表結構、將不相容的資料類型對應至較為相容的表單，且若使用者在移轉設定中指定的話，也將一併建立安全性認證和結構描述。 您可對鎖定的 SQL 資料倉儲執行個體執行此程式碼，或將它儲存至檔案、複製到剪貼簿，甚或以內嵌的方式編輯，再採取進一步的動作。  

如前所述，移轉結構描述時，請仔細檢閱工具所做的移轉變更，以確定您已完全了解這些變更內容。  

## <a name="migrate-data"></a>移轉資料
按一下 [移轉資料] 選項，您就可以產生 BCP 指令碼，它會先將資料移至伺服器上的一般檔案，接著再直接移進您的 SQL 資料倉儲。 因為並沒有內建重試功能，且網路連線中斷可能會導致失敗，我們建議您在移動少量資料的情況下使用此程序。 若要執行此程序，您必須先安裝 BCP 命令列公用程式，且必須事先建立資料的結構描述。

填妥上述參數之後，您只需要按一下 [執行移轉]，系統就會在您指定的位置產生兩個封裝。 執行匯出檔案，以將移轉來源的資料匯出至一般檔案，接著執行匯入檔案，將資料匯入 SQL 資料倉儲。

## <a name="next-steps"></a>後續步驟
現在您已成功移轉部分資料，請繼續了解如何[開發][develop]。

<!--Image references-->

<!--Article references-->
[migration documentation]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Download Migration Utility]: https://www.microsoft.com/en-us/download/details.aspx?id=49100
