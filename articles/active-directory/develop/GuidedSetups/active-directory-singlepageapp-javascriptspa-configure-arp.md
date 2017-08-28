---
title: "Azure AD v2 JS SPA 指引設定 - 設定 (ARP) | Microsoft Docs"
description: "JavaScript SPA 應用程式如何呼叫需要來自 Azure Active Directory v2 端點 (ARP) 之存取權杖的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 735559464db4c237ff7498b7350f2385090fa4e4
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---
## <a name="add-the-applications-registration-information-to-your-app"></a>將應用程式的註冊資訊新增到您的應用程式

在此步驟中，您需要設定應用程式註冊資訊的重新導向 URL，然後將應用程式識別碼新增至 JavaScript SPA 應用程式。

### <a name="configure-redirect-url"></a>設定重新導向 URL

使用以您的 Web 伺服器為基礎的 index.html 網頁 URL 設定上述 `Redirect URL` 欄位，然後按一下 [更新]。


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>取得重新導向 URL 的 Visual Studio 指示
> 若要取得重新導向 URL，請遵循下列指示：
> 1.    在「方案總管」中，選取專案並查看 `Properties` 視窗 (如果您沒有看到屬性視窗，請按 `F4`)
> 2.    將此值從 `URL` 複製到剪貼簿：<br/> ![專案屬性](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    貼上此值作為此分頁頂端的 `Redirect URL`，然後按一下 `Update`

<p/>

> #### <a name="setting-redirect-url-for-python"></a>設定 Python 的重新導向 URL
> 對於 Python，您可以透過命令列設定 Web 伺服器連接埠。 此指引設定會使用連接埠 8080 作為參考，但是可以隨意使用任何其他可用的通訊埠。 在任何情況下，請遵循下列指示以在應用程式註冊資訊中設定重新導向 URL：<br/>
> 設定 `http://localhost:8080/` 作為此分頁頂端的 `Redirect URL`，或者如果您使用自訂 TCP 連接埠，則使用 `http://localhost:[port]/` (其中 [port] 是自訂 TCP 通訊埠號碼)，然後按一下 [更新]

### <a name="configure-your-javascript-spa-application"></a>設定您的 JavaScript SPA 應用程式

1.  建立名為 `msalconfig.js` 的檔案，其中包含應用程式註冊資訊。 如果您使用 Visual Studio，請選取專案 (專案根資料夾)，以滑鼠右鍵按一下並選取：`Add` > `New Item` > `JavaScript File`。 將它命名為 `msalconfig.js`
2.  將下列程式碼新增至 `msalconfig.js` 檔案：

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 

