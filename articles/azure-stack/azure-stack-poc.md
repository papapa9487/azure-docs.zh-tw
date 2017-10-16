---
title: "什麼是 Azure Stack？ | Microsoft Docs"
description: "Azure Stack 可讓您在資料中心內執行 Azure 服務。"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: helaw
ms.custom: mvc
ms.openlocfilehash: 950ba44c0b7eb80c9b0a3c69a9fca03cd244576d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-stack"></a>什麼是 Azure Stack？

Microsoft Azure Stack 是混合式雲端平台產品，可讓您組織的資料中心提供 Azure 服務。  Azure Stack 的設計目的是要為您在關鍵案例 (例如邊緣和中斷連線的環境) 中的現代化應用程式啟用新案例，或是為了符合特定的安全性和合規性需求。  Azure Stack 是透過兩種部署選項來提供的，用以滿足您的需求。

## <a name="azure-stack-integrated-systems"></a>Azure Stack 整合系統
Azure Stack 整合系統是透過 Microsoft 與[硬體合作夥伴](https://azure.microsoft.com/overview/azure-stack/integrated-systems/)的合作來提供的，可建立既具有雲端步調的創新又兼顧管理簡易性的解決方案。  由於是以硬體與軟體的整合系統形式來提供 Azure Stack，因此除了仍然會採用來自雲端的創新之外，還會提供您適度的彈性和控制。  Azure Stack 整合系統的大小範圍為 4 到 12 個節點，並且由硬體合作夥伴與 Microsoft 共同支援。  您可以使用 Azure Stack 整合系統，來為生產環境工作負載啟用新案例。    

## <a name="azure-stack-development-kit"></a>Azure Stack 開發套件
「Microsoft Azure Stack 開發套件」是單一節點的 Azure Stack 部署，您可以用來評估和瞭解 Azure Stack。  您也可以使用「Azure Stack 開發套件」作為開發人員環境，您可以在其中使用與 Azure 一致的 API 和工具進行開發。  「Azure Stack 開發套件」不應該用來作為生產環境。

Azure Stack 開發套件有下列限制：
* Azure Stack 開發套件會與單一 Azure Active Directory 或「Active Directory 同盟服務」識別提供者關聯。 您可以在此目錄中建立多位使用者，並對每位使用者指派訂用帳戶。
* 因為在單一機器上部署了所有元件，所以為租用戶資源所提供的實體資源有限。 此設定不適合進行規模或效能評估。
* 因為單一主機/NIC 的需求，所以網路功能案例會受到限制。  

## <a name="next-steps"></a>後續步驟
[重要功能與概念](azure-stack-key-features.md)

[Azure Stack：Azure 的延伸 (PDF)](https://azure.microsoft.com/en-us/resources/azure-stack-an-extension-of-azure/) \(英文\)

