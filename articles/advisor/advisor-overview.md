---
title: "Azure 建議程式簡介 | Microsoft Docs"
description: "使用 Azure 建議程式將 Azure 部署最佳化。"
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 906450f75557820bb27762707c3328b08b23cccb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="introduction-to-azure-advisor"></a>Azure 建議程式簡介

了解 Azure Advisor 的主要功能，並取得常見問題的解答。

## <a name="what-is-advisor"></a>何謂 Advisor？
Advisor 是個人化的雲端顧問，可協助您依最佳做法來最佳化您的 Azure 部署。 它可分析您的資源組態和使用量遙測，然後建議可協助您改善 Azure 資源的成本效益、效能、高可用性和安全性的解決方案。

使用 Advisor，您可以：
* 取得主動式、可採取動作且個人化的最佳做法建議。 
* 改善資源的效能、安全性及高可用性，同時尋找降低整體 Azure 費用的機會。
* 取得內嵌了提議動作的建議。

您可以透過 [Azure 入口網站](https://aka.ms/azureadvisordashboard)存取建議程式。 登入[入口網站](https://portal.azure.com)，在導覽功能表中找出 [Advisor]，或在 [更多服務] 功能表中搜尋它。

Advisor 儀表板會顯示您所有訂用帳戶的個人化建議。  您可以套用篩選來顯示適用於特定訂用帳戶和資源類型的建議。  建議分為四個類別： 

* **高可用性**：確保和改善業務關鍵應用程式的持續性。 如需詳細資訊，請參閱[建議程式高可用性的建議](advisor-high-availability-recommendations.md)。
* **安全性**偵測可能導致安全性漏洞的威脅和弱點。 如需詳細資訊，請參閱[建議程式安全性建議](advisor-security-recommendations.md)。
* **效能**提升應用程式的速度。 如需詳細資訊，請參閱[建議程式效能建議](advisor-performance-recommendations.md)。
* **成本**：最佳化並降低整體 Azure 費用。 如需詳細資訊，請參閱[建議程式成本建議](advisor-cost-recommendations.md)。

  ![建議程式建議類型](./media/advisor-overview/advisor-dashboard.png)

> [!NOTE]
> 若要搭配某個訂用帳戶使用 Azure Advisor，訂用帳戶「擁有者」必須啟動 Advisor 儀表板。  此動作會向 Advisor 註冊訂用帳戶。  之後，任何訂用帳戶「擁有者」、「參與者」或「讀取者」便都可以存取該訂用帳戶的 Advisor 建議。 

您可以按一下某個類別來顯示該類別內的建議清單，然後選取建議來進行深入了解。  您也可以了解您可以執行的動作，以便利用機會或解決問題。

![Advisor 建議類別](./media/advisor-overview/advisor-ha-category-example.png) 

請選取某個建議的建議動作以實作該建議。  這會開啟一個簡單的介面，可讓您實作該建議，或讓您參考可協助您進行實作的文件。  實作建議之後，可能需要一天的時間，Azure Advisor 才能完成認可。

如果您不想對建議立即採取行動，您可以將它延遲一段時間或加以關閉。  如果您不想要收到針對特定訂用帳戶或資源群組的建議，您可以將 Advisor 設定成只針對指定的訂用帳戶和資源群組產生建議。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-do-i-access-advisor"></a>如何存取建議程式？
您可以透過 [Azure 入口網站](https://aka.ms/azureadvisordashboard)存取建議程式。 登入[入口網站](https://portal.azure.com)，在導覽功能表中找出 [Advisor]，或在 [更多服務] 功能表中搜尋它。

您也可以透過虛擬機器資源介面檢視 Advisor 建議。 選擇虛擬機器，然後捲動至功能表中的建議程式建議。 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>我需要哪些權限才能存取建議程式？

若要取得適用於某個訂用帳戶的 Advisor 建議，您必須先向 Advisor 註冊您的訂用帳戶。 當訂用帳戶「擁有者」啟動 Advisor 儀表板時，便會註冊訂用帳戶。 此作業只需要執行一次。 註冊訂用帳戶之後，您便能以訂用帳戶的「擁有者」、「參與者」或「讀取者」身分存取 Advisor 建議。

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>建議程式可提供哪些資源的建議？

Advisor 可提供虛擬機器、可用性設定組、應用程式閘道、應用程式服務、SQL Server、SQL Database 和 Redis 快取的建議。

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>是否可以延遲或解除建議？

若要延遲或關閉建議，請按一下 [延遲] 連結。 您可以指定延遲時間週期，或選取 [永不] 來解除建議。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：

* [開始使用建議程式](advisor-get-started.md)
* [建議程式高可用性建議](advisor-high-availability-recommendations.md)
* [建議程式安全性建議](advisor-security-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [建議程式成本建議](advisor-cost-recommendations.md)
