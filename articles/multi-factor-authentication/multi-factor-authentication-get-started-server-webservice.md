---
title: "Azure MFA Server Mobile App Web 服務 | Microsoft Docs"
description: "Microsoft Authenticator 應用程式提供額外的頻外驗證選項。  它可以讓 MFA Server 將通知推播給使用者。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: it-pro
ms.openlocfilehash: bf758d1241f2a56eba4d5c92ace713d6e563df65
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>使用 Azure Multi-Factor Authentication Server 來啟用行動應用程式驗證

Microsoft Authenticator 應用程式提供額外的頻外驗證選項。 Azure Multi-Factor Authentication 不會在使用者登入時，撥打自動電話或傳送 SMS 給使用者，而是會將通知推送到使用者智慧型手機或平板電腦上的 Microsoft Authenticator 應用程式。 使用者只需要在應用程式中點選 驗證 \(或輸入 PIN 再點選 [驗證]) 即可完成登入。

當手機收訊不可靠時，建議使用行動應用程式進行兩步驟驗證。 如果您使用此應用程式作為 OATH 權杖產生器，它並不需要任何網路或網際網路連線。

視您的環境而定，建議您在與 Azure Multi-factor Authentication Server 相同的伺服器，或在另一個網際網路對向的伺服器上部署行動裝置應用程式 Web 服務。

## <a name="requirements"></a>需求

若要使用 Microsoft Authenticator 應用程式，必須符合以下條件，應用程式才能與 Mobile App Web 服務成功通訊：

* Azure Multi-Factor Authentication Server 6.0 或更新版本
* 將 Mobile App Web 服務安裝在執行 Microsoft® [Internet Information Services (IIS) IIS 7.x 或更新版本](http://www.iis.net/)的網際網路對向 Web 伺服器上
* 已安裝、註冊 ASP.NET v4.0.30319 並已設定為 [已允許]
* 所需的角色服務包括 ASP.NET 和 IIS 6 Metabase 相容性
* 可透過公用 URL 存取 Mobile App Web 服務
* 已使用 SSL 憑證來保護 Mobile App Web 服務。
* 將 Azure Multi-Factor Authentication Web 服務 SDK 安裝在**與 Azure Multi-Factor Authentication Server 相同伺服器**上的 IIS 7.x 或更新版本中
* 已使用 SSL 憑證來保護 Azure Multi-Factor Authentication Web 服務 SDK。
* Mobile App Web 服務可透過 SSL 連接到 Azure Multi-Factor Authentication Web 服務 SDK
* Mobile App Web 服務可使用隸屬於 "PhoneFactor Admins" 安全性群組之服務帳戶的認證向 Azure MFA Web 服務 SDK 進行驗證。 如果 Azure Multi-Factor Authentication Server 是位於已加入網域的伺服器上，Active Directory 中就會有此服務帳戶和群組。 如果伺服器未加入網域，此服務帳戶和群組會存在於 Azure Multi-Factor Authentication Server 本機。

## <a name="install-the-mobile-app-web-service"></a>安裝 Mobile App Web 服務

在安裝 Mobile App Web 服務之前，請注意下列細節：

* 您需要屬於 "PhoneFactor Admins" 群組的服務帳戶。 此帳戶可與使用者入口網站安裝所用的帳戶相同。
* 在網際網路對向 Web 伺服器上開啟網頁瀏覽器，並瀏覽至輸入 web.config 檔案中的 Web 服務 SDK URL，如此將有所幫助。 如果瀏覽器可以順利連接 Web 服務，它應該會提示您輸入認證。 輸入使用者名稱和密碼 (與輸入 web.config 檔案中的使用者名稱和密碼完全相同)。 確定未出現任何憑證警告或錯誤。
* 如果反向 Proxy 或防火牆是在 Mobile App Web 服務 Web 伺服器之前，並且目前執行 SSL 卸載，則您可以編輯 Mobile App Web 服務的 web.config 檔案，以便讓 Mobile App Web 服務能夠使用 http 而不是 https。 從 Mobile App 到防火牆/反向 Proxy 仍然需要 SSL。 將下列索引鍵新增到 \<appSettings\> 區段：

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>安裝 Web 服務 SDK

在任一案例中，如果 Azure Multi-Factor Authentication (MFA) Server 上尚**未**安裝 Azure Multi-Factor Authentication Web 服務 SDK，請完成後續步驟。

1. 開啟 Multi-Factor Authentication Server 主控台。
2. 移至 [Web 服務 SDK] 並選取 [安裝 Web 服務 SDK]。
3. 除非基於某種原因而需要變更預設值，否則使用預設值來完成安裝。
4. 將 SSL 憑證繫結至 IIS 中的網站。

如果您有關於在 IIS 伺服器上設定 SSL 憑證的問題，請參閱[如何在 IIS 上設定 SSL](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) 一文。

Web 服務 SDK 必須受到 SSL 憑證保護。 自我簽署憑證適用於這項用途。 請將此憑證匯入到「使用者入口網站」Web 伺服器上 [本機電腦] 帳戶的 [受信任的根憑證授權單位] 存放區中，這樣它才會在起始 SSL 連線時信任該憑證。

![MFA Server 組態設定 Web 服務 SDK](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

### <a name="install-the-service"></a>安裝服務

1. **在 MFA Server 上**，瀏覽至安裝路徑。
2. 瀏覽至安裝 Azure MFA Server 的資料夾，預設值是 **C:\Program Files\Azure Multi-Factor Authentication**。
3. 找到 **MultiFactorAuthenticationMobileAppWebServiceSetup64** 安裝檔案。 如果伺服器**不是**網際網路對向伺服器，請將安裝檔案複製到網際網路對向伺服器。
4. 如果 MFA Server **不是**網際網路對向伺服器，請切換到**網際網路對向伺服器**。
5. 以系統管理員身分執行 **MultiFactorAuthenticationMobileAppWebServiceSetup64** 安裝檔案、視需要變更網站，以及將虛擬目錄變更為簡短名稱 (如果您想要)。
6. 完成安裝之後，瀏覽至 **C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** (或以虛擬目錄名稱為基礎的適當目錄) 並編輯 Web.Config 檔案。

   * 找出 **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** 機碼並將 **value=""** 變更為 **value="DOMAIN\User"**，其中的 DOMAIN\User 是屬於 "PhoneFactor Admins" 群組的服務帳戶。
   * 找出 **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** 機碼並將 **value=""** 變更為 **value="Password"**，其中的 Password 是在前一行中輸入之服務帳戶的密碼。
   * 找出 **pfMobile App Web Service_pfwssdk_PfWsSdk** 設定，並將值從 **http://localhost:4898/PfWsSdk.asmx** 變更為 Web 服務 SDK URL (例如：https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx)。
   * 儲存 Web.Config 檔案並關閉 [記事本]。

   > [!NOTE]
   > 由於此連線使用 SSL，因此您必須依**完整網域名稱 (FQDN)** 參考 Web 服務 SDK，而不是依 **IP 位址**。 SSL 憑證會針對 FQDN 發出，因此所使用的 URL 必須與憑證上的名稱相符。

7. 如果安裝 Mobile App Web 服務的網站尚未與公開簽署的憑證繫結，請在伺服器上安裝憑證、開啟「IIS 管理員」，然後將憑證繫結至該網站。
8. 從任何電腦開啟網頁瀏覽器，並瀏覽至安裝行動裝置應用程式 Web 服務的 URL (例如：https://mfa.contoso.com/MultiFactorAuthMobileAppWebService)。 確定未出現任何憑證警告或錯誤。

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>在 Azure Multi-Factor Authentication Server 中配置行動應用程式設定

既然 Mobile App Web 服務已安裝完成，您需要設定 Azure Multi-Factor Authentication Server，使其與入口網站搭配運作。

1. 在 Multi-Factor Authentication Server 主控台中，按一下 [使用者入口網站] 圖示。 如果您允許使用者控制其驗證方法，請在 [設定] 索引標籤之 [允許使用者選取方法] 下方選取 [行動應用程式]。 若未啟用這個功能，使用者就必須連絡技術支援人員來完成行動裝置應用程式啟用。
2. 選取 [允許使用者啟用行動裝置應用程式] 方塊。
3. 選取 [允許使用者註冊] 方塊。
4. 按一下 [行動裝置應用程式] 圖示。
5. 在 [行動裝置應用程式 Web 服務 URL：] 欄位中，輸入要搭配在安裝 MultiFactorAuthenticationMobileAppWebServiceSetup64 時建立之虛擬機器使用的 URL (例如：https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/)。
6. 以要顯示在此帳戶的行動裝置應用程式中的公司或組織名稱填入 [帳戶名稱] 欄位。
   ![MFA Server 組態行動裝置應用程式設定](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure Multi-Factor Authentication 與協力廠商 VPN 的進階案例](multi-factor-authentication-advanced-vpn-configurations.md)。