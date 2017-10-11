---
title: "Azure AD v2 iOS 快速入門 - 設定 | Microsoft Docs"
description: "iOS (Swift) 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
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
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 0ebca65585fc87bd4a85ba092cd423fce9540f58
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
## <a name="create-an-application-express"></a>建立應用程式 (快速)
現在您需要在「Microsoft 應用程式註冊入口網站」註冊應用程式：
1. 透過 [Microsoft 應用程式註冊入口網站 (英文)](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure) 註冊您的應用程式
2.  輸入應用程式的名稱和您的電子郵件
3.  確定已選取 [Guided Setup (引導式設定)] 選項
4.  依照指示取得應用程式識別碼並貼到您的程式碼中

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>將您的應用程式註冊資訊新增到方案 (進階)

1.  移至 [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/portal/register-app)
2.  輸入應用程式的名稱和您的電子郵件
3.  確定已取消選取 [Guided Setup (引導式設定)] 選項
4.  按一下 [`Add Platform`]，然後選取 [`Native Application`] 並按一下 [`Save`]
5.  返回至 Xcode。 在 `ViewController.swift` 中，以您剛剛註冊的應用程式識別碼取代開頭為 '`let kClientID`' 的行：

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
按住 Ctrl 鍵並按一下 <code>Info.plist</code> 以顯示內容功能表，然後按一下：<code>Open As</code> > <code>Source Code</code>
</li>
<li>
在 <code>dict</code> 根節點下，新增下列項目：
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
用您剛剛註冊的應用程式識別碼取代 <i><code>[Your_Application_Id_Here]</code></i>
</li>
</ol>
