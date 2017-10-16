---
title: "轉譯連結和 URL Azure AD Application Proxy | Microsoft Docs"
description: "涵蓋 Azure AD 應用程式 Proxy 連接器的基本概念。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 57218346d236b376d2227e0ffaea6c6dd5ebe855
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>重新導向使用 Azure AD Application Proxy 發佈之應用程式的硬式編碼連結

Azure AD Application Proxy 讓您的內部部署應用程式可供遠端使用者使用，或讓使用者在其自己的裝置上使用。 不過，有些應用程式是以 HTML 中內嵌的本機連結進行開發。 從遠端使用應用程式時，這些連結無法正常運作。 當您有數個指向彼此的內部部署應用程式時，使用者預期當他們不在辦公室時，連結可持續運作。 

若要確定連結在公司網路內部和外部的運作方式相同，最佳方法就是將您應用程式的外部 URL 設定為與其內部 URL 相同。 使用[自訂網域](active-directory-application-proxy-custom-domains.md)，將您的外部 URL 設定為包含公司網域名稱，而不是預設的應用程式 Proxy 網域。

如果您無法在租用戶中使用自訂網域，應用程式 Proxy 的連結轉譯功能會讓您的連結保持運作 (不論您的使用者身在何處)。 當您有直接指向內部端點或連接埠的應用程式時，您可以將這些內部 URL 對應至已發佈的外部 Application Proxy URL。 若已啟用連結轉譯，應用程式 Proxy 會透過 HTML、CSS 搜尋，並選取已發佈內部連結的 JavaScript 標籤。 然後應用程式 Proxy 會將它們轉譯，以便使用者獲得不受干擾的體驗。

>[!NOTE]
>連結轉譯功能適用於無法使用自訂網域 (不論何種原因) 讓其應用程式具有相同內部和外部 URL 的租用戶。 在您啟用這項功能之前，請查看您是否適用 [Azure AD Application Proxy 中的自訂網域](active-directory-application-proxy-custom-domains.md)。
>
>或者，如果您需要透過連結轉譯設定的應用程式為 SharePoint，請參閱[設定 SharePoint 2013 的備用存取對應](https://technet.microsoft.com/library/cc263208.aspx)以取得對應連結的另一種方法。

## <a name="how-link-translation-works"></a>連結轉譯的運作方式

驗證之後，當 Proxy 伺服器將應用程式資料傳遞給使用者時，Application Proxy 會掃描應用程式中的硬式編碼連結，然後以其各自發佈的外部 URL 加以取代。

應用程式 Proxy 假設應用程式是以 UTF-8 編碼。 如果事實並非如此，請在 http 回應標頭中指定編碼類型，例如 `Content-Type:text/html;charset=utf-8`。

### <a name="which-links-are-affected"></a>哪些連結會受影響？

連結轉譯功能只會尋找位於應用程式主體的程式碼標籤中的連結。 Application Proxy 有個別功能可轉譯標頭中的 cookie 或 URL。 

內部部署應用程式中有兩種常見的內部連結類型：

- **相對內部連結**，其指向本機檔案結構中的共用資源，例如 `/claims/claims.html`。 這些連結會自動在透過應用程式 Proxy 發佈的應用程式中運作，並且持續運作 (不論是否啟用連結轉譯)。 
- 其他內部部署應用程式 (例如 `http://expenses`) 或已發佈檔案 (例如 `http://expenses/logo.jpg`) 的**硬式編碼內部連結**。 連結轉譯功能適用於硬式編碼內部連結，並可將這些連結變更為指向遠端使用者必須通過的外部 URL。

### <a name="how-do-apps-link-to-each-other"></a>應用程式如何彼此連結？

每個應用程式都已啟用連結轉譯，以便您控制每個應用程式層級的使用者經驗。 當您想要轉譯「來自」該應用程式的連結 (而非「連到」該應用程式的連結) 時，請開啟應用程式的連結轉譯。 

例如，假設您有三個透過 Application Proxy 發佈且彼此連結的應用程式：Benefits、Expenses 和 Travel。 第四個應用程式 (Feedback) 不是透過 Application Proxy 發佈。

當您啟用 Benefits 應用程式的連結轉譯時，Expenses 和 Travel 的連結會重新導向至這些應用程式的外部 URL，但 Feedback 的連結因為沒有外部 URL 而不會重新導向。 從 Expenses 和 Travel 回到 Benefits 的連結沒有作用，因為這兩個應用程式尚未啟用連結轉譯功能。

![已啟用連結轉譯時從 Benefits 到其他應用程式的連結](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>哪些連結並未轉譯？

為了改善效能和安全性，並未轉譯有些連結：

- 不在程式碼標籤內的連結。 
- 不在 HTML、CSS 或 JavaScript 中的連結。 
- 從其他程式開啟的內部連結。 不會轉譯透過電子郵件或立即訊息傳送的連結，或包含在其他文件中的連結。 使用者必須知道要移至外部 URL。

如果您必須支援下列其中一種情況，請使用相同的內部和外部 URL，而不需進行連結轉譯。  

## <a name="enable-link-translation"></a>啟用連結轉譯

開始使用連結轉譯很簡單，按一下按鈕即可：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 移至 [Azure Active Directory] > [企業應用程式] > [所有應用程式] > 選取您要管理的應用程式 > [Application Proxy]。
3. 將 [轉譯應用程式主體中的 URL] 切換為 [是]。

   ![選取 [是] 可轉譯應用程式主體中的 URL](./media/application-proxy-link-translation/select_yes.png)。
4. 選取 [儲存] 以套用變更。

現在，當您的使用者存取此應用程式時，Proxy 會自動掃描已透過您租用戶上的 Application Proxy 發佈的內部 URL。

## <a name="send-feedback"></a>傳送意見反應

我們需要您的協助，讓這項功能可用於您所有的應用程式。 我們搜尋 HTML 和 CSS 中超過 30 個標籤，並考慮要支援哪些 JavaScript 案例。 如果您有不在轉譯中的已產生連結範例，請將程式碼片段傳送至 [Application Proxy 意見反應](mailto:aadapfeedback@microsoft.com)。 

## <a name="next-steps"></a>後續步驟
[使用自訂網域搭配 Azure AD 應用程式 Proxy](active-directory-application-proxy-custom-domains.md) 以具有相同的內部和外部 URL

[設定 SharePoint 2013 的備用存取對應](https://technet.microsoft.com/library/cc263208.aspx)
