---
title: "如何針對自訂開發的應用程式填寫特定欄位 | Microsoft Docs"
description: "關於當您向 Azure AD 註冊自訂開發的應用程式時如何填寫特定欄位的指引"
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
ms.openlocfilehash: 04fd35f238e4dd05486f85b0b16c2ab0c5ae9f30
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>如何針對自訂開發的應用程式填寫特定欄位

本文提供 [Azure 入口網站](https://portal.azure.com)上應用程式註冊表單中所有可用欄位的簡短說明。

## <a name="register-a-new-application"></a>註冊新的應用程式

-   若要註冊新的應用程式，請瀏覽至 [Azure 入口網站](https://portal.azure.com)。

-   從左側瀏覽窗格中，按一下 [Azure Active Directory]。

-   選擇 [應用程式註冊]，然後按一下 [新增]。

-   這樣會開啟應用程式註冊表單。

## <a name="fields-in-the-application-registration-form"></a>應用程式註冊表單中的欄位


| 欄位            | 說明                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| 名稱             | 應用程式的名稱。 至少應該有四個字元。                |
| 應用程式類型 | **Web 應用程式/Web API**：代表 Web 應用程式、Web API 或兩者的應用程式 
| |**原生**：可安裝在使用者裝置或電腦上的應用程式           |
| 登入 URL      | 使用者可登入以使用您應用程式的 URL                                  |

填寫上述欄位之後，就會在 Azure 入口網站中註冊該應用程式，並將您重新導向至應用程式頁面。 應用程式窗格上的 [設定] 按鈕會開啟 [設定] 頁面，其中提供更多欄位供您自訂應用程式。 下表描述 [設定] 頁面中的所有欄位。 請注意，根據您建立的是 Web 應用程式或原生應用程式而定，您只會看見這些欄位的一部分。

| 欄位           | 說明                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 應用程式識別碼  | 當您註冊應用程式時，Azure AD 會將應用程式識別碼指派給您的應用程式。 應用程式識別碼可在對 Azure AD 的驗證要求中用來唯一識別您的應用程式，還可用來存取資源 (例如圖形 API)。                                                          |
| 應用程式識別碼 URI      | 這應該是唯一的 URI，通常形式為 **https://&lt;tenant\_name&gt;/&lt;application\_name&gt;。** 這會在授權授與流程中使用，並做為唯一識別碼來指定應為其簽發權杖的資源。 它在核發的存取權杖中也會變成 'aud' 宣告。 |
| 上傳新標誌 | 您可以使用此欄位上傳應用程式的標誌。 標誌必須是 .bmp、.jpg 或.png 格式，而檔案大小應小於 100 KB。 影像尺寸應該是 215 x 215 像素，而中央影像尺寸為 94 x 94 像素。                                                       |
| 首頁 URL   | 這是在應用程式註冊期間指定的登入 URL。                                                                                                                                                                                                                                              |
| 登出 URL      | 這是單一登出的登出 URL。 當使用者透過 Azure AD 使用任何其他已註冊的應用程式來清除他們的工作階段時，Azure AD 會傳送登出要求給這個 URL。                                                                                                                                       |
| 多重租用戶  | 這個參數指定應用程式是否可供多重租用戶使用。 一般而言，這表示外部組織可以在其租用戶中註冊應用程式，並授權存取其組織的資料，以使用您的應用程式。                                                                   |
| 回覆 URL      | 回覆 URL 是 Azure AD 傳回應用程式所要求之任何權杖時的端點。                                                                                                                                                                                                          |
| 重新導向 URI   | 針對原生應用程式，使用者在成功授權之後會前往這裡。 Azure AD 會檢查您的應用程式在 OAuth 2.0 要求中所提供的重新導向 URI，是否符合入口網站上其中一個已註冊的值。                                                            |
| 之間的信任            | 您可以建立金鑰，以程式設計方式存取 Azure AD 所保護的 Web API，而不需與使用者互動。 從 [金鑰]\*\*\*\* 頁面，輸入金鑰描述和到期日，並儲存以產生金鑰。 務必將它儲存在安全的位置，因為您稍後將無法存取它。             |

## <a name="next-steps"></a>後續步驟
[使用 Azure Active Directory 管理應用程式](active-directory-enable-sso-scenario.md)
