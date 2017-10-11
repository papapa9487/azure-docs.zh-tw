---
title: "使用 Azure AD 應用程式 Proxy 登入內部部署應用程式時遇到問題 | Microsoft Docs"
description: "針對無法使用 Azure AD 應用程式 Proxy 登入與 Azure AD 整合的內部部署應用程式時的常見問題進行疑難排解"
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
ms.openlocfilehash: 5687f789355cc9769d26b53e98486bb213c66419
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 登入內部部署應用程式時遇到問題

如果您無法登入內部部署應用程式，您可以嘗試下列步驟來解決您的問題。

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>我可以載入應用程式，但頁面上的某些內容看起來支離破碎

下列文件可協助您解決此類別的一些最常見問題。

  * [我可以取得我的應用程式，但應用程式頁面未正確顯示](https://docs.microsoft.com/azure/active-directory/application-proxy-page-appearance-broken-problem/)
  * [我可以取得我的應用程式，但應用程式的載入時間過久](https://docs.microsoft.com/azure/active-directory/application-proxy-page-load-speed-problem/)
  * [我可以取得我的應用程式，但應用程式頁面上的連結無法作用](https://docs.microsoft.com/azure/active-directory/application-proxy-page-links-broken-problem/)

## <a name="im-having-a-connectivity-problem-my-application"></a>我的應用程式在連線時遇到問題
  下列文件可協助您解決此類別的一些最常見問題。
  * [我不知道要為我的應用程式開啟哪些連接埠](https://docs.microsoft.com/azure/active-directory/application-proxy-connectivity-ports-how-to/)
  * [我遇到問題，連接器群組中沒有作用中的連接器可用於我的應用程式](https://docs.microsoft.com/azure/active-directory/application-proxy-connectivity-no-working-connector/)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>我在管理入口網站中設定 Azure AD 應用程式 Proxy 時遇到問題
  下列文件可協助您解決此類別的一些最常見問題。
  * [我無法設定使用應用程式 Proxy 的應用程式](https://docs.microsoft.com/azure/active-directory/application-proxy-config-how-to/)
  * [我不知道如何為使用應用程式 Proxy 的應用程式設定單一登入](https://docs.microsoft.com/azure/active-directory/application-proxy-config-sso-how-to/)
  * [我在管理入口網站中建立我的應用程式時遇到問題](https://docs.microsoft.com/azure/active-directory/application-proxy-config-problem/)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>我在為應用程式設定後端驗證時遇到問題
  下列文件可協助您解決此類別的一些最常見問題。
  * [我不知道如何設定 Kerberos 限制委派](https://docs.microsoft.com/azure/active-directory/application-proxy-back-end-kerberos-constrained-delegation-how-to/)
  * [我不知道如何使用 PingAccess 設定我的應用程式](https://docs.microsoft.com/azure/active-directory/application-proxy-back-end-ping-access-how-to/)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>我在登入應用程式時遇到問題
  下列文件可協助您解決此類別的一些最常見問題。
  * [我收到「無法存取此公司應用程式」的錯誤](https://docs.microsoft.com/azure/active-directory/application-proxy-sign-in-bad-gateway-timeout-error/)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>我在使用應用程式 Proxy 代理程式連接器時遇到問題
  下列文件可協助您解決此類別的一些最常見問題。
  * [我在安裝應用程式 Proxy 代理程式連接器時遇到問題](https://docs.microsoft.com/azure/active-directory/application-proxy-connector-installation-problem/)

## <a name="next-steps"></a>後續步驟
[如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)
