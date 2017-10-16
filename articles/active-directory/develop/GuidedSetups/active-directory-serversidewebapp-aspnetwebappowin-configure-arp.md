---
title: "Azure AD v2 ASP.NET Web 伺服器快速入門 - 設定 | Microsoft Docs"
description: "使用 OpenID Connect 標準，搭配傳統網頁瀏覽器型應用程式，在 ASP.NET 方案上實作 Microsoft 登入"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 8a1650a65e7980f4a13fa4edc7918b0099bb5464
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>使用應用程式的註冊資訊設定您的 ASP.NET Web 應用程式

在這個步驟中，您將會設定專案使用 SSL，然後使用 SSL URL 設定應用程式的註冊資訊。 設定完成後，請透過 *web.config* 將應用程式的註冊資訊新增到方案。

1.  在「方案總管」中，選取專案並查看 `Properties` 視窗 (如果您沒有看到 [屬性] 視窗，請按 F4)
2.  將 `SSL Enabled` 變更為 `True`
3.  從上面的 `SSL URL` 複製值並貼到此頁面頂端的 `Redirect URL` 欄位中，然後按一下 [更新]：<br/><br/>![專案屬性](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
4.  在位於根資料夾之 `web.config` 檔案中的 `configuration\appSettings` 區段底下新增以下內容：

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
