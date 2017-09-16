---
title: "Log Analytics 記錄檔搜尋中的電腦群組 | Microsoft Docs"
description: "Log Analytics 中的電腦群組可讓您將記錄檔搜尋範圍限於一組特定的電腦。  這篇文章說明可用來建立電腦群組的不同方法，以及如何將它們用在記錄檔搜尋中。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: a2ddc932343d54963a378ee27dc962a790326b2a
ms.contentlocale: zh-tw
ms.lasthandoff: 07/28/2017

---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Log Analytics 記錄檔搜尋中的電腦群組

>[!NOTE]
> 本文說明使用目前的 Log Anayltics 查詢語言，以使用電腦群組。    如果您的工作區已升級為[新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則電腦群組的運作方式不同。  本文中提供的附註具有新查詢語言的不同語法和行為。  


Log Analytics 中的電腦群組中可讓您將[記錄檔搜尋](log-analytics-log-searches.md)範圍限於一組特定的電腦。  使用您所定義的查詢，或從不同來源匯入群組，將電腦填入每個群組中。  當記錄檔搜尋包含群組時，結果就僅限於與群組中的電腦相符的記錄。

## <a name="creating-a-computer-group"></a>建立電腦群組
您可以使用下表的任何方法，在 Log Analytics 中建立電腦群組。  下列各節提供每個方法的詳細資料。 

| 方法 | 說明 |
|:--- |:--- |
| 記錄搜尋 |建立記錄檔搜尋來傳回電腦清單，並將結果儲存為電腦群組。 |
| 記錄檔搜尋 API |使用記錄檔搜尋 API，根據記錄檔搜尋結果，以程式設計方式建立電腦群組。 |
| Active Directory |自動掃描屬於 Active Directory 網域的任何代理程式電腦的群組成員資格，並為每個安全性群組在 Log Analytics 中建立一個群組。 |
| WSUS |自動掃描 WSUS 伺服器或用戶端來找出目標群組，並為每個群組在 Log Analytics 中建立一個群組。 |

### <a name="log-search"></a>記錄搜尋
從記錄搜尋建立的電腦群組會包含您定義的搜尋查詢所傳回的所有電腦。  每次使用電腦群組時都會執行此查詢，因此會反映自建立群組以來的任何變更。

使用下列程序從記錄檔搜尋建立電腦群組。

1. [建立記錄檔搜尋](log-analytics-log-searches.md)來傳回電腦清單。  此搜尋必須在查詢中使用類似 **Distinct Computer** 或 **measure count() by Computer** 的語法，以傳回一組不同的電腦。  
2. 按一下畫面頂端的 [儲存] 按鈕。
3. 在 [將此查詢儲存為電腦群組] 上選取 [是]。
4. 輸入群組的 [名稱] 和 [類別]。  如果已經存在具有相同名稱和類別的搜尋，則會提示您覆寫它。  您可以有多個相同名稱但不同類別的搜尋。 

以下是您可以儲存為電腦群組的搜尋範例。

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

>[!NOTE]
> 如果您的工作區已升級為[新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則會對程序進行以下變更以建立新的電腦群組。
>  
> - 用來建立電腦群組的查詢必須包含 `distinct Computer`。  以下是用來建立電腦群組之查詢的範例。<br>`Heartbeat | where Computer contains "srv" | distinct Computer `
> - 當您建立新的電腦群組時，除了名稱之外，您還必須指定別名。  您在查詢中使用電腦群組時可以使用別名，如下所述。  

### <a name="log-search-api"></a>記錄檔搜尋 API
以記錄檔搜尋 API 建立的電腦群組，與以記錄檔搜尋建立的搜尋相同。

如需使用記錄檔搜尋 API 建立電腦群組的詳細資料，請參閱 [Log Analytics 記錄檔搜尋 REST API 中的電腦群組](log-analytics-log-search-api.md#computer-groups)。

### <a name="active-directory"></a>Active Directory
當您設定匯入 Log Analytics 來匯入 Active Directory 群組成員資格時，它會分析已加入網域且裝有 OMS 代理程式的任何電腦的群組成員資格。  針對 Active Directory 中的每個安全性群組，Log Analytics 中會建立一個電腦群組，而每一部電腦會新增至對應到它們所屬安全性群組的電腦群組。  此成員資格持續地每 4 小時更新一次。  

您可以從 Log Analytics [設定] 的 [電腦群組] 功能表中，設定 Log Analytics 來匯入 Active Directory 安全性群組。  選取 [自動化]，然後選取 [從電腦匯入 Active Directory 群組成員資格]。  不需要進一步的組態。

![來自 Active Directory 的電腦群組](media/log-analytics-computer-groups/configure-activedirectory.png)

匯入群組後，此功能表會列出已偵測到群組成員資格的電腦數目，以及匯入的群組數目。  您可以按一下任一連結，以連同此資訊傳回 **ComputerGroup** 記錄。

### <a name="windows-server-update-service"></a>Windows Server Update Service
當您設定匯入 Log Analytics 來匯入 WSUS 群組成員資格時，它會分析裝有 OMS 代理程式的任何電腦的目標群組成員資格。  如果您使用用戶端目標，則任何連線至 OMS 且屬於任何 WSUS 目標群組的電腦，其群組成員資格都將匯入到 Log Analytics。 如果您使用伺服器端目標，則 WSUS 伺服器上應該安裝 OMS 代理程式，群組成員資格資訊才能匯入到 OMS。  此成員資格持續地每 4 小時更新一次。 

您可以從 Log Analytics [設定] 的 [電腦群組] 功能表中，設定 Log Analytics 來匯入 Active Directory 安全性群組。  選取 [Active Directory]，然後選取 [從電腦匯入 Active Directory 群組成員資格]。  不需要進一步的組態。

![來自 Active Directory 的電腦群組](media/log-analytics-computer-groups/configure-wsus.png)

匯入群組後，此功能表會列出已偵測到群組成員資格的電腦數目，以及匯入的群組數目。  您可以按一下任一連結，以連同此資訊傳回 **ComputerGroup** 記錄。

## <a name="managing-computer-groups"></a>管理電腦群組
您可以從 Log Analytics [設定] 的 [電腦群組] 功能表中，檢視已從記錄檔搜尋或記錄檔搜尋 API 建立的電腦群組。  按一下 [移除] 欄的 [x] 來刪除電腦群組。  按一下群組的 [檢視成員] 圖示，以執行群組的記錄檔搜尋來傳回其成員。 

![已儲存的電腦群組](media/log-analytics-computer-groups/configure-saved.png)

若要修改群組，請建立具有相同 [類別] 和 [名稱] 的新群組來覆寫原始群組。

## <a name="using-a-computer-group-in-a-log-search"></a>在記錄檔搜尋中使用電腦群組
您可以使用下列語法，在記錄檔搜尋中參考電腦群組。  指定 **Category** 是選擇性，只有當您在不同類別中有相同名稱的電腦群組時，才需要指定。 

    $ComputerGroups[Category: Name]

執行搜尋時會先解析搜尋中包含的任何電腦群組的成員。  如果群組是根據記錄搜尋，則會先執行該搜尋來傳回群組的成員，然後才執行最上層的記錄搜尋。

在記錄搜尋中，電腦群組通常搭配 **IN** 子句一起使用，如下列範例所示：

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

>[!NOTE]
> 如果您的工作區已升級為[新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則您藉由將電腦群組的別名視為函式，在查詢中使用電腦群組，如下列範例所示：
> 
>  `UpdateSummary | where Computer in (mycomputergroup)`

## <a name="computer-group-records"></a>電腦群組記錄
針對從 Active Directory 或 WSUS 建立每個電腦群組成員資格，OMS 存放庫中會建立一筆記錄。  這些記錄的類型為 **ComputerGroup**，且具有下表中的屬性。  如果電腦群組是根據記錄檔搜尋，則不會建立記錄。

| 屬性 | 說明 |
|:--- |:--- |
| 類型 |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| 電腦 |成員電腦的名稱。 |
| 群組 |群組的名稱。 |
| GroupFullName |群組的完整路徑，包括來源和來源名稱。 |
| GroupSource |群組的收集來源。 <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |群組的收集來源名稱。  對於 Active Directory，這是網域名稱。 |
| ManagementGroupName |SCOM 代理程式的管理群組名稱。  若為其他代理程式，此為 AOI-\<工作區 ID\> |
| TimeGenerated |建立或更新電腦群組的日期和時間。 |

## <a name="next-steps"></a>後續步驟
* 了解 [記錄搜尋](log-analytics-log-searches.md) ，其可分析從資料來源和方案所收集的資料。  


