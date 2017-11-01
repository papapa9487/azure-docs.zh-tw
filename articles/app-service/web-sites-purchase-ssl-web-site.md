---
title: "將 SSL 憑證新增至 Azure App Service 應用程式 | Microsoft Docs"
description: "了解如何將 SSL 憑證新增至您的 App Service 應用程式。"
services: app-service
documentationcenter: .net
author: ahmedelnably
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo
ms.openlocfilehash: 214f05f45f59b0403e6902988f9184d6b62618bd
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2017
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>購買並設定您的 Azure App Service 的 SSL 憑證

本教學課程會說明如何購買 **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** 的 SSL 憑證，安全地將它儲存在 [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) 中，並將它與自訂網域產生關聯，藉此保護 Web 應用程式。

## <a name="step-1---log-in-to-azure"></a>步驟 1 - 登入 Azure

登入 Azure 入口網站，網址是 http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>步驟 2 - 訂購 SSL 憑證

您可以在 **Azure 入口網站**中建立新的 [App Service 憑證](https://portal.azure.com/#create/Microsoft.SSL)，以訂購 SSL 憑證。

![建立憑證](./media/app-service-web-purchase-ssl-web-site/createssl.png)

輸入易記的 [名稱] 作為 SSL 憑證，並輸入 [網域名稱]

> [!NOTE]
> 這是購買程序的其中一個最重要的步驟。 請務必輸入您想要使用此憑證保護的正確主機名稱 (自訂網域)。 **請勿** 在主機名稱上附加 WWW。 
>

選取 [訂用帳戶]、[資源群組] 和 [憑證 SKU]。

> [!WARNING]
> App Service 憑證只能由相同訂用帳戶中的其他應用程式服務使用。  
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>步驟 3 - 將憑證儲存至 Azure Key Vault

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) 是一項 Azure 服務，可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和祕密。
>

完成 SSL 憑證購買程序之後，您必須開啟 [App Service 憑證](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)頁面。

![插入準備在 KV 中儲存的影像](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

憑證狀態是**暫止發行**，因為您必須先完成一些其他的步驟，才能開始使用此憑證。

按一下 [憑證屬性] 頁面內的 [憑證設定]，然後按一下 [步驟 1：儲存]，將此憑證儲存至 Azure Key Vault。

從 [Key Vault 狀態] 頁面中按一下 [Key Vault 存放庫]，選擇要儲存此憑證的現有 Key Vault，或者選擇 [建立新的 Key Vault]，在相同訂用帳戶和資源群組內建立新的 Key Vault。

> [!NOTE]
> Azure Key Vault 儲存此憑證會產生少許費用。
> 如需詳細資訊，請參閱 **[Azure Key Vault 定價詳細資料](https://azure.microsoft.com/pricing/details/key-vault/)**。
>

選取要儲存此憑證的 Key Vault 存放庫後，[儲存] 選項應顯示成功。

![插入在 KV 中儲存成功的影像](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>步驟 4︰確認網域擁有權

> [!NOTE]
> App Service 憑證支援 3 種類型的網域驗證：網域、郵件和手動驗證。 [進階](#advanced)一節會詳細說明這些驗證類型。

從您在步驟 3 使用的相同 [憑證設定] 頁面，按一下 [步驟 2：驗證] 步驟。

**網域驗證**：**只有**在您**已經從 Azure App Service 購買自訂網域[時，這才是最方便的程序。](custom-dns-web-site-buydomains-web-app.md)**
按一下 [驗證] 按鈕來完成這個步驟。

![插入網域驗證的影像](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

按一下 [驗證] 之後，使用 [重新整理] 按鈕，直到 [驗證] 選項顯示成功為止。

![插入在 KV 中驗證成功的影像](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>步驟 5 - 將憑證指派給 App Service 應用程式

> [!NOTE]
> 在執行本節中的步驟之前，您必須先建立自訂網域名稱與應用程式的關聯。 如需詳細資訊，請參閱**[設定 Web 應用程式的自訂網域名稱。](app-service-web-tutorial-custom-domain.md)**
>

在 **[Azure 入口網站](https://portal.azure.com/)**中，按一下頁面左側的 [App Service] 選項。

按一下您要指派此憑證的應用程式的名稱。

在 [設定] 中，按一下 [SSL 憑證]。

按一下 [匯入 App Service 憑證] 並選取您剛購買的憑證。

![插入匯入憑證的影像](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

在 [SSL 繫結] 區段中，按一下 [新增繫結]，然後使用下拉式清單選取要以 SSL 保護的網域名稱，以及要使用的憑證。 您也可以選擇使用**[伺服器名稱指示 (SNI) ](http://en.wikipedia.org/wiki/Server_Name_Indication)**還是 IP SSL。

![插入 SSL 繫結的影像](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

按一下 [新增繫結]  ，儲存變更並啟用 SSL。

> [!NOTE]
> 如果您已選取 [以 IP 為基礎的 SSL]，而且您的自訂網域是以 A 記錄設定，則必須執行下列額外步驟。 [進階](#Advanced)一節中會有詳細的說明。

現在，您應該可以使用 `HTTPS://` 而非 `HTTP://` 造訪您的應用程式，確認已正確設定憑證。

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>步驟 6 - 管理工作

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>進階

### <a name="verifying-domain-ownership"></a>驗證網域擁有權

App Service 憑證另外支援 2 種類型的網域驗證：郵件和手動驗證。

#### <a name="mail-verification"></a>郵件驗證

驗證電子郵件已傳送到與此自訂網域相關聯的電子郵件地址。
若要完成電子郵件驗證步驟，請開啟電子郵件並按一下驗證連結。

![插入電子郵件驗證的影像](./media/app-service-web-purchase-ssl-web-site/KVVerifyEmailSuccess.png)

如果您需要重新傳送驗證電子郵件，按一下 [重新傳送電子郵件] 按鈕。

#### <a name="manual-verification"></a>手動驗證

> [!IMPORTANT]
> HTML 網頁驗證 (僅適用於標準憑證 SKU)
>

1. 建立名為 **"starfield.html"** 的 HTML 檔案

1. 此檔案的內容應該與網域驗證權杖的名稱完全相同。 (您可以從 [網域驗證狀態] 頁面中複製權杖)

1. 在主控您網域 `/.well-known/pki-validation/starfield.html` 的 Web 伺服器的根目錄上傳此檔案。

1. 按一下 [重新整理]，在完成驗證之後更新憑證狀態。 驗證可能需要數分鐘才能完成。

> [!TIP]
> 在終端機中使用 `curl -G http://<domain>/.well-known/pki-validation/starfield.html` 驗證回應應包含 `<verification-token>`。

#### <a name="dns-txt-record-verification"></a>DNS TXT 記錄驗證

1. 使用 DNS 管理員，在具有與網域驗證權杖相同值的 `@` 子網域上建立 TXT 記錄。
1. 按一下 [重新整理]，在完成驗證之後更新憑證狀態。

> [!TIP]
> 您必須在值為 `<verification-token>` 的 `@.<domain>` 上建立 TXT 記錄。

### <a name="assign-certificate-to-app-service-app"></a>將憑證指派給 App Service 應用程式

如果您已選取 [ **IP SSL** ]，而且您的自訂網域是以 A 記錄設定，則必須執行下列額外步驟：

設定 IP SSL 繫結之後，您的應用程式將會獲派專用的 IP 位址。 您可以在應用程式設定下的 [自訂網域] 頁面上找到此 IP 位址，正好位於 [主機名稱] 區段上面。 它會列為 [外部 IP 位址]

![插入 IP SSL 的影像](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

此 IP 位址與先前用來設定網域 A 記錄的虛擬 IP 位址不同。 如果您設定成使用以 SNI 為基礎的 SSL，或未設定成使用 SSL，則不會列出此項目的位址。

使用網域名稱註冊機構所提供的工具，修改自訂網域名稱的 A 記錄，使其指向上一個步驟的 IP 位址。

## <a name="rekey-and-sync-the-certificate"></a>重設金鑰和同步處理憑證

如果您需要重設憑證金鑰，請選取 [憑證屬性] 頁面中的 [重設金鑰和同步處理] 選項。

按一下 [重設金鑰] 按鈕來啟動處理程序。 此程序需要 1 - 10 分鐘才能完成。

![插入重設 SSL 金鑰的影像](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

重設憑證的金鑰，會以憑證授權單位發行的新憑證變更憑證。

<a name="notrenewed"></a>
## <a name="why-is-my-ssl-certificate-not-auto-renewed"></a>我的 SSL 憑證為何未自動更新？

如果您的 SSL 憑證已設定為要自動更新，但並未自動更新，您可能尚未完成網域驗證。 請注意： 

- GoDaddy (會產生 App Service 憑證) 每三年需要驗證網域一次。 網域系統管理員每三年就會收到一次用來驗證網域的電子郵件。 若未檢查電子郵件或驗證網域，App Service 憑證就不會自動更新。 
- 2017 年 3 月 31 日之前發出的所有 App Service 憑證，都需要在下一次更新時重新驗證網域 (即使憑證已啟用自動更新)。 這是由於 GoDaddy 原則有所變更。 請檢查您的電子郵件，並完成這項一次性的網域驗證，以繼續自動更新 App Service 憑證。 

## <a name="next-steps"></a>後續步驟

* [新增內容傳遞網路](app-service-web-tutorial-content-delivery-network.md)