---
title: "了解 Azure MFA 中的雙步驟驗證 | Microsoft Docs"
description: "何謂 Azure Multi-factor Authentication、為什麼使用 MFA，還有 Multi-Factor Authentication 用戶端、不同的方法及可用版本的詳細資訊。 "
keywords: "MFA 的簡介, mfa 概觀, 什麼是 mfa"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: c40d7a34-1274-4496-96b0-784850c06e9b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2017
ms.author: kgremban
ms.openlocfilehash: 231028a7389dba50efe7d197837855b39826f9b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-multi-factor-authentication"></a>什麼是 Azure Multi-Factor Authentication？
雙步驟驗證是需要多種驗證方法，並在使用者登入和交易中新增重要的第二層安全性的驗證方法。 其運作方式需要下列其中任何二或多個驗證方法：

* 您知道的某些資訊 (通常是密碼)
* 您擁有的某些東西 (不容易輕易複製的信任裝置，例如電話)
* 您身上的某些特徵 (生物識別技術)

<center>![使用者名稱和密碼](./media/multi-factor-authentication/pword.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![憑證](./media/multi-factor-authentication/phone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![智慧型手機](./media/multi-factor-authentication/hware.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![智慧卡](./media/multi-factor-authentication/smart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![虛擬智慧卡](./media/multi-factor-authentication/vsmart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![使用者名稱和密碼](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) 是 Microsoft 的雙步驟驗證解決方案。 Azure MFA 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 它可以透過一些驗證方法 (包括電話、文字訊息，或行動應用程式驗證) 來提供強大的驗證功能。

## <a name="why-use-azure-multi-factor-authentication"></a>為何使用 Azure Multi-Factor Authentication？
與以前比較起來，現今人們連線網路的時間越來越長。 透過智慧型手機、平板電腦、膝上型電腦以及電腦，人們有幾種不同選擇可隨時用來連線網路並維持連線。 人們可以從任何地方存取他們的帳戶與應用程式，這表示他們可以完成更多工作並為客戶提供更好的服務。

Azure Multi-Factor Authentication 是一個容易使用、可調整且可靠的解決方案，可提供第二種驗證方法讓您的使用者永遠受到保護。

| ![容易使用](./media/multi-factor-authentication/simple.png) | ![可調整](./media/multi-factor-authentication/scalable.png) | ![永遠受到保護](./media/multi-factor-authentication/protected.png) | ![可靠](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **容易使用** |**可調整** |**永遠受到保護** |**可靠** |

* **容易使用** - Azure Multi-Factor Authentication 很容易設定及使用。 隨附於 Azure Multi-Factor Authentication 的額外保護可讓使用者管理他們自己的裝置。 最棒的是，在許多情況下，只需簡單按幾下滑鼠即可進行設定。
* **可調整** - Azure Multi-Factor Authentication 採用雲端技術且與您內部部署的 AD 與自訂應用程式整合。 此保護功能甚至可以擴充以因應您高任務關鍵性的狀況。
* **永遠受到保護** - Azure Multi-Factor Authentication 使用最高工業標準提供強大驗證功能。
* **可靠** - 我們保證 Azure Multi-Factor Authentication 的可用性可達到 99.9%。 當服務無法接收或處理雙步驟驗證的驗證要求時，服務會被視為無法使用。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Multi-Factor Authentication 的作用](multi-factor-authentication-how-it-works.md)

- 請參閱不同的 [Azure Multi-Factor Authentication 版本和耗用方法](multi-factor-authentication-versions-plans.md)
