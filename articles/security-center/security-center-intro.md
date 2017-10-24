---
title: "Azure 資訊安全中心簡介 | Microsoft Docs"
description: "了解 Azure 資訊安全中心、其主要功能及運作方式。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: terrylan
ms.openlocfilehash: 21415af0d449d639d000e07afdb4de3680a64774
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-security-center"></a>Azure 資訊安全中心簡介
了解 Azure 資訊安全中心、其主要功能及運作方式。

## <a name="what-is-azure-security-center"></a>什麼是 Azure 資訊安全中心？
Azure 資訊安全中心提供統一的安全性管理和進階的威脅防護，保護 Azure、內部部署和其他雲端中執行的工作負載。  其提供了針對混合式雲端工作負載的可見性和控制能力、可降低威脅曝露度的主動防禦措施，還有智慧型偵測可幫助您跟上網路攻擊快速發展的腳步。

[資訊安全中心 - 概觀] 可讓您快速檢視您 Azure 與非 Azure 工作負載的安全性狀態、探索並評估工作負載的安全性，並識別和降低風險。

![概觀](./media/security-center-intro/security-center-intro-fig1.png)

## <a name="why-use-security-center"></a>為何使用資訊安全中心？

**統一的可見性和控制**

- **了解混合式工作負載之間的安全性狀態**。 可在一個主控台管理所有混合式雲端工作負載 (內部部署、Azure 和其他雲端平台) 的安全性。 內建儀表板可供立即深入掌握需要注意的安全性問題。
- **雲端工作負載的可見性**。 跟上雲端工作負載快速變化的腳步。 自動探索在您的 Azure 訂用帳戶中建立的新資源並加以上架。
- **集中式管理原則**。 集中管理所有混合式雲端工作負載的安全性原則，確保符合公司或法規的安全性需求。
- **多個來源的安全性資料**。 收集、搜尋和分析各種來源 (包括已連線的合作夥伴解決方案，例如網路防火牆和其他 Microsoft 服務) 的安全性資料。 
- **與現有安全性工作流程整合**。 使用 REST API 連接現有的工具和程序，以存取、整合和分析安全性資訊。
- **更新狀態報告**。 使用安全性資料和深入剖析資訊來展現合規性，並輕鬆產生用於稽核員的辨識項。

**調適性威脅防護**

- **持續的安全性評估**。 使用數百種內建安全性評定或建立您自己的安全性評定，監視您的機器、網路和 Azure 服務安全性。 識別容易遭受攻擊的軟體和組態。
- **可操作的建議**。 運用已排定優先順序、可操作的安全性建議與內建的自動化腳本，在攻擊者入侵之前修正安全性弱點。
- **自動調整應用程式控制項**。 藉由套用依特定 Azure 工作負載調整並由機器學習服務提供的白名單建議，來封鎖惡意程式碼和其他不想要的應用程式。 
- **網路存取安全性**。 利用即時控制存取減少網路攻擊面，以管理 Azure VM 上的連接埠，大幅減少暴力密碼破解攻擊和其他網路攻擊。

**智慧型威脅偵測和回應**

- **業界最廣泛的威脅情報**。 善用 Microsoft Intelligent Security Graph，運用世界各地的 Microsoft 服務和系統發出數兆個訊號識別新興的和不斷演進的威脅。
- **進階威脅偵測**。 使用內建行為分析和機器學習服務，以識別攻擊和零時差惡意探索。 監視網路、機器和雲端服務中是否有傳入攻擊和侵入後活動。
- **已排定優先順序的警示和事件**。 藉由已排定優先順序的安全性警示和事件 (將不同類型的警示對應到單一攻擊活動)，首先專注處理最嚴重的威脅。 建立您自己的自訂安全性提醒。
- **簡化調查**。 透過視覺化互動式操作方式快速評估攻擊的範圍和影響。 使用預先定義或臨機操作查詢，以更深入探索安全性資料。 
- **內容相關威脅情報**。 在互動式世界地圖上以視覺化的方式呈現攻擊的來源。 使用內建威脅情報報告，掌握已知惡意執行者利用的技術和設定的目標相關的重要情報。

## <a name="get-started"></a>開始使用
若要開始使用資訊安全中心，您需要 Microsoft Azure 訂用帳戶。 資訊安全中心已經由 Azure 訂用帳戶啟用。 如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial/)。 

[開始使用 Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-get-started) 會快速引導您認識資訊安全中心的安全性監視和原則管理元件。 


## <a name="next-steps"></a>後續步驟
本文介紹了資訊安全中心、其重要功能和如何開始進行。 若要深入了解，請參閱下列資源：

* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)：了解如何根據您組織的安全性需求和雲端管理模型，將您的資訊安全中心使用最佳化。
* [設定安全性原則](https://docs.microsoft.com/azure/security-center/security-center-policies)：了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理安全性建議](https://docs.microsoft.com/azure/security-center/security-center-recommendations)：了解建議如何協助保護您的 Azure 和非 Azure 資源。
* [安全性健康情況監視](https://docs.microsoft.com/azure/security-center/security-center-monitoring)：了解如何監視您 Azure 和非 Azure 資源的健康情況。
* [管理及回應安全性警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)：了解如何管理與回應安全性警示。
* [監視合作夥伴解決方案](https://docs.microsoft.com/azure/security-center/security-center-partner-solutions)：了解如何監視合作夥伴解決方案的健全狀態。
* [資訊安全中心常見問題集](https://docs.microsoft.com/azure/security-center/security-center-faq)：尋找有關資訊安全中心的常見問題。


