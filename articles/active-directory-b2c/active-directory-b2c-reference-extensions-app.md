---
title: "擴充功能應用程式 - Azure AD B2C | Microsoft Docs"
description: "還原 b2c-extensions-app"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: f0392e32-0771-473c-a799-81438ca2bcff
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/17/2017
ms.author: parja
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 17500b572a0e92c1c233c6967840a5b6d96e21cb
ms.contentlocale: zh-tw
ms.lasthandoff: 08/17/2017

---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C：擴充功能應用程式

建立 Azure AD B2C 目錄時，會自動在新的目錄內建立名為 `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` 的應用程式。 此應用程式稱為 **b2c-extensions-app**，會顯示在應用程式註冊中。 它是由 Azure AD B2C 服務用來儲存使用者和自訂屬性的相關資訊。 如果將應用程式刪除，Azure AD B2C 就無法正確運作，並會影響您的生產環境。

> [!IMPORTANT]
> 除非您打算立即刪除租用戶，否則請勿刪除 b2c-extensions-app。 若將應用程式刪除超過 30 天，使用者資訊就會永久遺失。

## <a name="verifying-that-the-extensions-app-is-present"></a>正在驗證擴充功能應用程式是否存在

若要驗證 b2c-extensions-app 是否存在：

1. 在您的 Azure AD B2C 租用戶中，按一下左側導覽功能表中的 [更多服務]。
1. 搜尋並開啟 [應用程式註冊]。
1. 尋找以 **b2c-extensions-app** 開頭的應用程式

## <a name="recover-the-extensions-app"></a>復原擴充功能應用程式

如果您不小心刪除 b2c-extensions-app，會有 30 天可進行復原。 您可以使用圖形 API 來還原應用程式：

1. 瀏覽至 [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)。
1. 針對您需要還原已刪除應用程式的 Azure AD B2C 目錄，以全域系統管理員身分登入站台。
1. 針對 URL `https://graph.windows.net/{tenantName}.onmicrosoft.com/deletedApplications` 發出 HTTP GET，其中 api-version=1.6。 請務必將 `{tenantName}` 取代為您的租用戶名稱。 這項作業會列出過去 30 天內所有已刪除的應用程式。
1. 在名稱開頭為 'b2c-extension-app' 的清單中尋找應用程式，並複製其 `objectid` 屬性值。
1. 針對 URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore` 發出 HTTP POST。 從上一個步驟將 URL 的 `{OBJECTID}` 部分取代為 `objectid`。 

您現在應該能夠在 Azure 入口網站中[看到還原的應用程式](#verifying-that-the-extensions-app-is-present)。

> [!NOTE]
> 只能還原過去 30 天內刪除的應用程式。 如果已經超過 30 天，資料將會永久遺失。 如需協助，請提出支援票證。
