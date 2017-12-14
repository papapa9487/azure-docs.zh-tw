---
title: "使用 OMS 更新管理中的 SCCM 集合進行目標更新 | Microsoft Docs"
description: "本文旨在協助您設定 System Center Configuration Manager，以使用此解決方案來管理 SCCM 受管理電腦的更新。"
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: ec97432cd14c6289928f0419c242e1ccc2c8d876
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2017
---
# <a name="integrate-system-center-configuration-manager-with-oms-update-management"></a>整合 System Center Configuration Manager 與 OMS 更新管理

投資了 System Center Configuration Manager 以管理電腦、伺服器和行動裝置的客戶也需仰賴其管理軟體更新的強度和成熟度，作為軟體更新管理 (SUM) 週期的一部分。  

以現今 OMS 和 Configuration Manager 之間的整合為基礎建置，您可以透過在 Configuration Manager 中建置並預先暫存軟體更新部署，並使用[更新管理解決方案](../operations-management-suite/oms-solution-update-management.md)取得所完成更新部署的詳細狀態，藉此報告和更新受管理的 Windows 伺服器。 如果您將 Configuration Manager 用於更新相容性報告，而不是用於管理 Windows 伺服器的更新部署，您可以繼續向 Configuration Manager 報告，同時安全性更新則是使用 OMS 更新管理解決方案來管理。

## <a name="prerequisites"></a>必要條件

* 您必須將[更新管理解決方案](../operations-management-suite/oms-solution-update-management.md)新增至您的 Log Analytics 工作區，並與您位於相同的資源群組與區域的自動化帳戶連結。   
* 目前由 System Center Configuration Manager 環境所管理的 Windows 伺服器，也需要向也已啟用更新管理解決方案的 Log Analytics 工作區報告。  
* 此功能支援 System Center Configuration Manager 目前分支，1606 版和更高版本。  若要將 Configuration Manager 中央管理網站或獨立主要站台整合與 Log Analytics 整合並匯入集合，請檢閱[將 Configuration Manager 連線至 Log Analytics](../log-analytics/log-analytics-sccm.md)。  
* Windows 代理程式如果未從 Configuration Manager 收到任何安全性更新，則必須設定為可與 Windows Server Update Services (WSUS) 伺服器通訊，或必須能夠存取 Microsoft Update。   

您使用現有的 Configuration Manager 環境管理裝載在 Azure IaaS 中用戶端的方式，主要取決於在 Azure 資料中心與您的基礎結構之間具有的連線。 此連線會影響您可能需要對 Configuration Manager 基礎結構進行的任何設計變更，與支援這些必要變更的相關成本。  若要了解繼續之前需要評估哪些規劃考，請檢閱 [Azure 上的 Configuration Manager - 常見問題集](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure#networking)。    

## <a name="configuration"></a>組態

### <a name="manage-software-updates-from-configuration-manager"></a>從 Configuration Manager 管理軟體更新 

如果您要繼續從 Configuration Manager 管理更新部署，請執行下列步驟。  OMS 會連線至 Configuration Manager，將更新套用至連線到您 Log Analytics 工作區的用戶端電腦。 更新內容可從用戶端電腦快取取得，就如同部署受到 Configuration Manager 管理一般。  

1. 從使用[部署軟體更新程序](https://docs.microsoft.com/sccm/sum/deploy-use/deploy-software-updates)中描述的程序，從 Configuration Manager 階層中的頂層站台建立軟體更新部署。  必須與標準部署不同的唯一設定為可控制部署封裝下載行為的選項 [不要安裝軟體更新]。 此行為是由 OMS 更新管理解決方案透過在下一個步驟中建立排定的更新部署來管理。  

1. 在 OMS 入口網站中，開啟 [更新管理] 儀表板。  遵循[建立更新部署](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment)中所述的步驟來建立新的部署，並從下拉式清單選取以 OMS 電腦群組表示的適當 Configuration Manager 集合。  請記住下列重點：
    1. 如果已在所選的 Configuration Manager 裝置集合上定義維護時段，該集合的成員會接受它，而不是 OMS 的排程部署中定義的 [持續時間] 設定。
    1. 目標集合的成員必須具有網際網路的連線 (直接、透過 Proxy 伺服器或透過 OMS 閘道)。  

完成使用 OMS 解決方案的更新部署之後，屬於所選電腦群組成員的目標電腦會在排定的時間，從其本機用戶端快取安裝更新。  您可以[檢視更新部署狀態](../operations-management-suite/oms-solution-update-management.md#viewing-update-deployments)，以監視您的部署結果。  


### <a name="manage-software-updates-from-oms"></a>從 OMS 管理軟體更新

若要為身為 Configuration Manager 用戶端的 Windows Server VM 管理更新，您必須設定用戶端原則，為此解決方案所管理的所有用戶端停用軟體更新管理功能。  根據預設，用戶端設定是以階層中的所有裝置為目標。  如需有關此原則設定和設定方式的詳細資訊，請檢閱[如何在 System Center Configuration Manager 中設定用戶端設定](https://docs.microsoft.com/sccm/core/clients/deploy/configure-client-settings)。  

執行這項組態變更之後，請遵循[建立更新部署](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment)中所述的步驟來建立新的部署，並從下拉式清單選取以 OMS 電腦群組表示的適當 Configuration Manager 集合。 

