---
title: "在 Azure Active Directory 中使用 Cloud App Discovery 尋找未受管理的雲端應用程式 | Microsoft Docs"
description: "提供有關使用 Cloud App Discovery 尋找和管理應用程式、它的優勢和運作方式的資訊。"
services: active-directory
keywords: "雲端應用程式探索, 管理應用程式"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 35b898aa3c03aeef914a7df574ac65a22a6c7bec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>使用 Cloud App Discovery 尋找未受管理的雲端應用程式
## <a name="summary"></a>摘要

Cloud App Discovery Premium 是 Azure Active Directory 的一個功能，可讓您探索您組織中的人員所使用的未受管理雲端應用程式。 在現代企業中，IT 部門通常不會知道組織的成員執行其工作所使用的所有雲端應用程式。 很容易知道為什麼系統管理員必須對未經授權存取公司資料、可能的資料外洩和其他安全性風險有所顧慮。 缺乏認知可能使得要建立一個可應付這些安全性風險的計劃讓人卻步。

> [!TIP] 
> 請查看 Azure Active Directory (Azure AD) 中 Cloud App Discovery 的增強功能，這些增強功能是藉由[與 Microsoft Cloud App Security 整合](https://portal.cloudappsecurity.com)而獲得增強。

**使用 Cloud App Discovery，您可以：**

* 尋找使用的雲端應用程式，並依使用者數目、流量或應用程式的 Web 要求數目測量使用量。
* 識別正在使用應用程式的使用者。
* 匯出資料以進行離線分析。
* 讓這些應用程式在 IT 的控制下並為使用者管理啟用單一登入。

## <a name="how-it-works"></a>運作方式
1. 應用程式使用代理程式會安裝在使用者的電腦上。
2. 代理程式所擷取的應用程式使用情況資訊會透過安全的加密通道，傳送到 Cloud App Discovery 服務。
3. Cloud App Discovery 服務會評估資料並產生報告。

![Cloud App Discovery 圖表](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>後續步驟
* [Cloud App Discovery 的安全性和隱私權考量](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [具有自訂連接埠的 Proxy 伺服器的 Cloud App Discovery 登錄設定](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery 代理程式變更記錄 ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)

