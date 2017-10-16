---
title: "Application Proxy 應用程式的應用程式頁面未正確顯示 | Microsoft Docs"
description: "當頁面在已與 Azure AD 整合的 Application Proxy 應用程式中未正確顯示時的指引"
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
ms.openlocfilehash: cac4c333e59ef9a0f28a2f93a7afee22eeafd54e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Application Proxy 應用程式的應用程式頁面未正確顯示

這篇文章可在您瀏覽至應用程式頁面，但該頁面有些內容未正確顯示時，協助您為 Azure Active Directory Application Proxy 應用程式的問題疑難排解。

## <a name="overview"></a>概觀
當您發佈 Application Proxy 應用程式時，在存取該應用程式時只有您根目錄下的頁面才可供存取。 如果頁面未正確顯示，則用於該應用程式的根目錄內部 URL 可能遺漏某些頁面資源。 若要解決此問題，請確定您已發佈該頁面的*所有*資源做為您應用程式的一部分。

您可以開啟網路追蹤程式 (例如 Fiddler，或 Internet Explorer/Edge 中的 F12 工具)，載入頁面，然後尋找 404 錯誤，來驗證這就是問題。 此錯誤表示該頁面目前找不到，且可能仍然需要發佈。

以此案例為例，假設您已使用 <http://myapps/expenses> 的內部 URL 發佈一個費用應用程式，但該應用程式使用樣式表 <http://myapps/style.css>。 在此狀況下，該樣式表未在您的應用程式中發佈，因此若載入費用應用程式，則會在嘗試載入 style.css 時擲回 404 錯誤。 在此範例中，改用 <http://myapp/> 的內部 URL 發佈應用程式，就能解決問題。

## <a name="problems-with-publishing-as-one-application"></a>發佈為一個應用程式時的問題

如果無法在同一個應用程式內發佈所有的資源，您必須發佈多個應用程式，並啟用之間的連結。

若要這樣做，我們建議使用[自訂網域](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains)解決方案。 不過，此解決方案需要您擁有您網域的憑證，且您的應用程式使用完整的網域名稱 (FQDN)。 如需其他選項，請參閱[為連結中斷的文件疑難排解](https://microsoft-my.sharepoint.com/personal/harshja_microsoft_com/_layouts/15/guestaccess.aspx?guestaccesstoken=IxuG3mFVbnPWI3Yn4Qi7wCNi8VIfHS5mwPt5quh8DMw%3d&docid=2_14558cd6ddea34c1c9887dc640feb5831&rev=1)。

## <a name="next-steps"></a>後續步驟
[使用 Azure AD 應用程式 Proxy 發佈應用程式](application-proxy-publish-azure-portal.md)
