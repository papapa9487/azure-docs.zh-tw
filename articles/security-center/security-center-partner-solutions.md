---
title: "在 Azure 資訊安全中心管理合作夥伴解決方案 | Microsoft Docs"
description: "本文件逐步引導您使用 Azure 資訊安全中心，讓您監視與您的 Azure 訂用帳戶整合之合作夥伴解決方案的健康狀態，一目了然。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: 3ff38892f198ab5dfb9e08c1c01b942810a40260
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-partner-solutions-with-azure-security-center"></a>使用 Azure 資訊安全中心監視合作夥伴解決方案
本文件逐步引導您在 Azure 資訊安全中心監視合作夥伴解決方案的健康狀態。

> [!NOTE]
> 本文件將使用範例部署來介紹服務。 本文件不是一份逐步解說指南。
>
>

## <a name="monitoring-partner-solutions"></a>監視合作夥伴解決方案
[概觀] 下方的 [安全性解決方案] 圖格可讓您監視與您的 Azure 訂用帳戶整合之合作夥伴解決方案的健康狀態，一目了然。

![合作夥伴解決方案圖格][1]

若要檢視合作夥伴解決方案的健康狀態：

1. 選取 [安全性解決方案] 圖格。 [安全性解決方案] 即會開啟，其中顯示已連線到資訊安全中心的合作夥伴解決方案清單。

   ![合作夥伴解決方案][3]

   合作夥伴解決方案的狀態可以是︰

   * 受保護 (綠色) - 沒有任何健康問題。
   * 狀況不良 (紅色) - 有需要立即注意的健康狀態問題。
   * 停止報告 (橘色) - 解決方案已停止報告其健康狀態。
   * 未知保護狀態 (橘色) - 此時解決方案的健康狀態因為將新資源加入至現有解決方案的程序失敗，而呈現未知狀態
   * 未報告 (灰色) - 解決方案尚未報告任何狀態，如果解決方案最近連接且仍在部署中，則可能未報告解決方案的狀態。

2. 選取合作夥伴解決方案。 隨即開啟一個刀鋒視窗，其中顯示合作夥伴解決方案的狀態和解決方案相關聯的資源。 選取 [解決方案主控台]  以開啟此解決方案的合作夥伴管理體驗。

   ![合作夥伴解決方案詳細資料][4]
3. 返回 [Qualys] 刀鋒視窗，然後選取 [連結 VM]。 [連結應用程式]  刀鋒視窗隨即開啟。 您可以在這裡將資源連接到合作夥伴解決方案。

   ![將資源連結至第三方解決方案][5]

## <a name="next-steps"></a>後續步驟
在本文件中，已向您介紹資訊安全中心的 [安全性伴解決方案] 圖格。 若要深入了解與合作夥伴整合以加強整體安全性的資訊，請參閱[合作夥伴和解決方案整合](security-center-partner-integration.md)。

如要深入了解資訊安全中心，請參閱下列文章：

* [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) — 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) — 了解建議如何協助保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) — 了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) — 了解如何管理與回應安全性警示。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) — 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) — 取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png
