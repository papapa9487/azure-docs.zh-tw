---
title: "Application Proxy 應用程式載入時間過長 | Microsoft Docs"
description: "為 Azure AD Application Proxy 的頁面載入效能問題疑難排解"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: ce462c90746e6af0dc201686557121665b82b93d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Application Proxy 應用程式載入時間過長

這篇文章會協助您了解為何 Azure AD Application Proxy 應用程式的載入時間可能很長，以及您如何解決此問題。

## <a name="overview"></a>概觀
如果您的應用程式正在運作，但您發現延遲時間很長，您可以考慮對網路拓撲進行一些微幅調整，以改善速度。 若要評估不同的拓樸，請參閱[網路考量文件](https://docs.microsoft.com/azure/active-directory/application-proxy-network-topology-considerations)。

如果這些考量沒有幫助，很抱歉我們目前對於效能調整沒有進一步的建議。 隨著 Application Proxy 服務不斷擴展，當有更多的資料中心更靠近您時，您可能會開始直接感受到延遲的問題獲得改善。 若要檢視完整的 Azure 資料中心清單，請參閱[延遲測試頁面](http://www.azurespeed.com/Azure/Latency) (英文)。 

若要找到含 Application Proxy 服務的資料中心，可使用[連接器連接埠測試工具](https://aadap-portcheck.connectorporttest.msappproxy.net/)。 

## <a name="feedback-on-application-proxy-data-center-locations"></a>對 Application Proxy 資料中心位置的意見反應 
可能會有 Azure 資料中心尚未包含 Application Proxy，但能為您顯著改善延遲的問題。 請將資料中心位置意見以電子郵件傳送到 <aadapfeedback@microsoft.com>，讓我們在規劃擴充時能採用您的意見反應。

我們正在開發其他功能，幫助目前延遲時間長的使用者改善延遲問題，當這些功能可供使用時，我們一定會將文件分享給您。

## <a name="next-steps"></a>後續步驟
[使用現有的內部部署 Proxy 伺服器](application-proxy-working-with-proxy-servers.md)
