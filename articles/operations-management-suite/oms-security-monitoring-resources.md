---
title: "在 Operations Management Suite 安全性和稽核解決方案內監視資源 | Microsoft Docs"
description: "本文件可協助您使用 OMS 安全性和稽核的功能來監視您的資源及識別安全性問題。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: d6752120-821f-4aa7-a049-25bf5a653b95
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 2f73266b65a4eda6c8751a2d56bc3f11bf4e6a57
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>在 Operations Management Suite 安全性和稽核內監視資源
本文件可協助您使用 OMS 安全性和稽核的功能來監視您的資源及識別安全性問題。

## <a name="what-is-oms"></a>何謂 OMS？
Microsoft Operations Management Suite (OMS) 是 Microsoft 的雲端型 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。 如需 OMS 的詳細資訊，請閱讀 [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx)一文。

## <a name="monitoring-resources"></a>監視資源
在盡可能的情況下，您會想要從一開始就防止發生安全性事件。 然而，要防止所有安全性事件是不可能的。 當安全性事件發生時，您將需要確保其所帶來的影響為最低。  有三項極為重要的建議，可用來將安全性事件的發生次數及影響降至最低︰

* 定期評估您環境內的弱點。
* 定期檢查所有電腦系統和網路裝置，確保它們皆已安裝最新的修補程式。
* 定期檢查所有記錄檔和記錄機制，包括作業系統事件記錄檔、應用程式特定記錄檔,以及入侵偵測系統記錄檔。

OMS 安全性和稽核方案可讓 IT 人員主動監視所有資源，此舉有助於將安全性事件的影響降至最低。 OMS 安全性和稽核俱備可用來監視資源的安全性網域。 安全性網域提供了特定選項的快速存取方式，接下來的部份將會涵蓋關於安全性監視下列網域的詳細說明︰

* 惡意程式碼評估
* 更新評估
* 身分識別與存取

> [!NOTE]
> 如需所有這些選項的概觀，請參閱 [開始使用 Operations Management Suite 安全性和稽核解決方案](oms-security-getting-started.md)。
> 
> 

### <a name="monitoring-system-protection"></a>監視系統保護
在深度防禦方法中，對於您資產的整體安全性狀態而言，每個保護層都是極為重要。 偵測到威脅及保護效力不足的電腦，會在「安全網域」下的「惡意程式碼評估圖格」內出現。 透過使用「惡意程式碼評估」上的資訊，您可以識別計劃以將保護措施套用在需要的伺服器上。 若要存取此選項，請遵循下列步驟：

1. 在 [Microsoft Operations Management Suite] 主儀表板內按一下 [安全性和稽核] 圖格。
   
    ![安全性和稽核](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. 在 [安全性和稽核] 儀表板內，按一下 [安全性網域] 下的 [惡意程式碼評估]。 如下所示，[反惡意程式碼評估] 儀表板會出現：

![惡意程式碼評估](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

您可以使用 [惡意程式碼評估]  儀表板來識別下列安全性問題：

* [作用中威脅]︰電腦曾受到入侵，而且系統內有作用中威脅。
* [已矯正的威脅]︰電腦曾受到入侵，但已矯正了這些威脅。
* [簽章已過期]︰電腦已啟用惡意程式碼保護，但是簽章已過期。
* [沒有即時保護]：電腦並無安裝反惡意程式碼產品。

### <a name="monitoring-updates"></a>監控更新
套用最新的安全性更新是確保安全性的最佳作法，而且應將其納入您的更新管理策略之中。 Microsoft Monitoring Agent 服務 (HealthService.exe) 會讀取來自受監視電腦的更新資訊，接著會傳送此更新過的資訊至雲端上的 OMS 服務以進行處理。 Microsoft Monitoring Agent 服務會設定為自動服務，而其應一律在目標電腦中執行。

![監控更新](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

會將邏輯套用至更新資料，且雲端服務會記錄資料。 如果找到遺失的更新，它們會顯示在 [ **更新** ] 儀表板。 您可以使用 [ **更新** ] 儀表板處理遺失的更新及開發計劃，將它們套用至所需要的伺服器。 請遵循下列步驟來存取 [更新]  儀表板：

1. 在 [Microsoft Operations Management Suite] 主儀表板內按一下 [安全性和稽核] 圖格。
2. 在 [安全性和稽核] 儀表板內，按一下 [安全性網域] 下的 [更新評估]。 如下所示，[更新儀表板] 會出現：

![更新評估](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

在此儀表板中，您可以執行更新評估來了解您電腦目前的狀態，接著解決最為嚴重的威脅。 透過使用 [重大或安全性更新]  圖格的方式，IT 系統管理員將能存取遺失的更新詳細資訊，如下所示︰

![搜尋結果](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

此報告包含可用來識別此系統容易遭受何種類型威脅攻擊的重要資訊，其中包括與安全性更新相關的 Microsoft 知識庫文章，以及有更多關於弱點詳細資料的 Microsoft 資訊安全公告。

### <a name="monitoring-identity-and-access"></a>監視身分識別與存取
由於使用者有隨處工作、使用不同裝置，以及存取大量雲端及內部部署應用程式的需求，因此務必確保他們的認證能受到保護。 認證竊取攻擊是指攻擊者一開始先取得一般使用者的認證，進而在網路內存取系統。 許多情況下，此初始攻擊僅是取得存取網路權限的一種方式，而終極目標是探索有使用權限的帳戶。 

攻擊者將會在網路內停留，並隨意地使用合適的工具針對工作階段內的其他已登入帳戶來擷取認證。 根據系統組態而定，這些認證可擷取為雜湊、票證或甚至是純文字密碼的格式。  

> [!NOTE]
> 直接與網際網路連接的機器，將會看到許多的失敗登入嘗試，都是試著使用各種類型的常用使用者名稱 (例如：系統管理員) 來登入。 若是並未使用常用的使用者名稱，而且所使用的密碼強度夠強，在大部分情況下沒有問題。
> 
> 

其實要在這些入侵者危害俱有權限的帳戶之前，就已先行識別是有可能的。 您可以利用 **OMS 安全性和稽核解決方案** 來監視身分識別與存取。 請遵循下列步驟來存取 [身分識別與存取]  儀表板：

1. 在 [Microsoft Operations Management Suite]  主儀表板內按一下 [安全性和稽核] 圖格。
2. 在 [安全性和稽核] 儀表板內，按一下 [安全性網域] 下的 [身分識別與存取]。 如下所示，[身分識別與存取] 儀表板會出現：

![身分識別與存取](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

作為定期監視策略的一部分，您必須包含身分識別監視。 IT 系統管理員應查看是否有特定的有效使用者名稱，出現多次登入嘗試的情況。 這可能代表攻擊者已取得實際的使用者名稱，接著嘗試進行暴力密碼破解，或是使用透過已過期硬式編碼密碼的自動工具進行破解。

此儀表板可讓 IT 人員迅速地判斷和身分識別與存取公司資源相關的潛在威脅。 而在識別潛在趨勢上，其亦特別重要，例如：在 [登入經過時間] 圖格上，您可以看到過往期間曾進行過的失敗登入嘗試次數。 在此案例中， **FileServer** 電腦已接收到 35 次登入嘗試。 只要按一下該項目，您就可以瞭解更多關於此電腦的詳細資訊。 

![其他詳細資訊](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

為此電腦而產生的報告，提供了與此模式相關的有用詳細資訊。 請注意，**ACCOUNT** 資料行提供您用於嘗試存取系統的使用者帳戶，而 **TIMEGENERATED** 資料行提供您已進行嘗試的時間間隔，至於 **LOGONTYPENAME** 資料行則提供您進行此嘗試的所在位置。 如果這些嘗試是在本機內透過程式所進行，則 **PROCESS** 資料行會顯示處理程序的名稱。 在透過程式所進行的登入嘗試案例中，您已取得可用的處理程序名稱，現在您可以在目標系統內進行更進一步的調查。

## <a name="see-also"></a>另請參閱
在本文件中，您已了解如何使用「OMS 安全性和稽核」解決方案來監視您的資源。 若要深入了解 OMS 安全性，請參閱下列文章：

* [Operations Management Suite (OMS) 概觀](operations-management-suite-overview.md)
* [開始使用 Operations Management Suite 安全性和稽核解決方案](oms-security-getting-started.md)
* [在 Operations Management Suite 安全性和稽核內監視及回應安全性警示](oms-security-responding-alerts.md)

