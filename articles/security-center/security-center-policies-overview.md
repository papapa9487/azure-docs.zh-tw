---
title: "Azure 資訊安全中心安全性原則簡介 | Microsoft 文件"
description: "了解 Azure 資訊安全中心安全性原則與其主要功能。"
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: f85f7077e5227818d062d114370e7344601a998e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="security-policies-overview"></a>安全性原則概觀
本文件提供資訊安全中心的安全性原則概觀。

## <a name="what-are-security-policies"></a>什麼是安全性原則？
安全性原則會定義工作負載的所需設定，並且協助確保符合公司或法規安全性需求。 您可以在資訊安全中心為 Azure 訂用帳戶定義原則，可以針對工作負載類型或資料機密性訂製。 例如，對於使用個人識別資訊等規範資料的應用程式，其需要的安全性層級可能比工作負載還高。 

資訊安全中心原則包含下列元件：

- 資料收集：代理程式佈建和[資料收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)設定。
- 安全性原則：[Azure 原則](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)，用於決定哪些控制項由資訊安全中心監視及建議 (在資訊安全中心編輯，或使用 Azure 原則以建立新的定義、定義額外的原則，以及在整個管理群組指派原則)。
- 電子郵件通知：安全性連絡人和[電子郵件通知](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)設定。
- 定價層：免費或標準[定價選項](https://docs.microsoft.com/azure/security-center/security-center-pricing)，用來決定哪個資訊安全中心功能可用於範圍中的資源 (可以針對訂用帳戶、資源群組和工作區指定)。 


## <a name="who-can-edit-security-policies"></a>誰可以編輯安全性原則？
資訊安全中心會使用角色型存取控制 (RBAC)，以提供可在 Azure 中指派給使用者、群組與服務的內建角色。 當使用者開啟資訊安全中心時，他們只能看到與自己能夠存取的資源相關的資訊。 這表示會為使用者指派資源所屬訂用帳戶或資源群組的「擁有者」、「參與者」或「讀者」角色。 除了這些角色，有兩個特定的資訊安全中心角色：

- 安全性讀取者：屬於此角色的使用者能夠檢視資訊安全中心的權限 (包括建議、警示、原則和健康情況)，但它無法進行變更。
- 安全性管理員：與安全性讀取者相同，但它也可以更新安全性原則，解除建議和警示。


## <a name="next-steps"></a>後續步驟
在本文件中，您已了解 Azure 資訊安全中心安全性原則的相關資訊。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) — 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) — 了解建議如何協助保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) — 了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) — 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) — 了解如何監視合作夥伴解決方案的健全狀況。
- [Azure 資訊安全中心資料安全性](security-center-data-security.md) - 了解資訊安全中心如何管理及保護其中的資料。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) — 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) — 取得最新的 Azure 安全性新聞和資訊。


