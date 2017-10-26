---
title: "使用用戶端憑證驗證保護後端服務 - Azure API 管理 | Microsoft Docs"
description: "了解如何在 Azure API 管理中使用用戶端憑證驗證來保護後端服務。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 43453331-39b2-4672-80b8-0a87e4fde3c6
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 196a91c21afb8c1596c9766f6a2a5d373b828f60
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>如何在 Azure API 管理中使用用戶端憑證驗證來保護後端服務
API 管理提供以用戶端憑證保護 API 後端服務之存取的功能。 本指南將示範如何在 API 發行者入口網站內管理憑證，以及如何設定 API 以使用憑證來存取其後端服務。

如需使用 API 管理 REST API 來管理憑證的詳細資訊，請參閱 [Azure API 管理 REST API 憑證實體][Azure API Management REST API Certificate entity]。

## <a name="prerequisites"> </a>必要條件
本指南將示範如何設定 API 管理服務執行個體，以使用用戶端憑證驗證來存取 API 的後端服務。 在遵循本主題中的步驟之前，請先設定後端服務以進行用戶端憑證驗證 ([若要在 Azure WebSites 中設定憑證驗證，請參閱此文章][to configure certificate authentication in Azure WebSites refer to this article])，以及取得憑證的存取權限和憑證密碼，以在 API 管理發佈者入口網站內上傳。

## <a name="step1"> </a>上傳用戶端憑證
若要開始，請在 API 管理服務的 Azure 入口網站中按一下 [發佈者入口網站]。 這會帶您前往 API 管理發行者入口網站。

![API 發行者入口網站][api-management-management-console]

> 如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][Get started with Azure API Management]教學課程中的[建立 API 管理服務執行個體][Create an API Management service instance]。
> 
> 

從左側的 [API 管理] 功能表按一下 [安全性]，然後按一下 [用戶端憑證]。

![Client certificates][api-management-security-client-certificates]

若要上傳新憑證，請按一下 [Upload certificate] 。

![Upload certificate][api-management-upload-certificate]

瀏覽至憑證的所在位置，然後輸入憑證密碼。

> 憑證必須是 **.pfx** 格式。 可接受自我簽署憑證。
> 
> 

![Upload certificate][api-management-upload-certificate-form]

按一下 [上傳]  以上傳憑證。

> 此時，系統會驗證憑證密碼。 如果密碼不正確，系統會顯示錯誤訊息。
> 
> 

![Certificate uploaded][api-management-certificate-uploaded]

待憑證上傳完畢後，它會顯示在 [用戶端憑證]  索引標籤中。如果您擁有多個憑證，請記下主體或指紋的最後四個字元，因為在設定 API 以使用憑證時，您可以利用它們來選取憑證，如下文中的＜[設定 API 以使用用戶端憑證來驗證閘道][Configure an API to use a client certificate for gateway authentication]＞一節所述。

> 若要在使用自我簽署的憑證時關閉憑證鏈結驗證，請遵循此常見問題集[項目](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)中所述的步驟。
> 
> 

## <a name="step1a"> </a>刪除用戶端憑證
若要刪除憑證，請按一下目標憑證旁的 [刪除]  。

![Delete certificate][api-management-certificate-delete]

按一下 [Yes, delete it]  加以確認。

![Confirm delete][api-management-confirm-delete]

如果有 API 佔用憑證，系統會顯示警告畫面。 若要刪除憑證，您必須先從已設定為使用該憑證的 API 中將其移除。

![Confirm delete][api-management-confirm-delete-policy]

## <a name="step2"> </a>設定 API 以使用用戶端憑證來驗證閘道
從左側的 [API 管理] 功能表按一下 [API]，再依序按一下所需之 API 的名稱和 [安全性] 索引標籤。

![API security][api-management-api-security]

從 [使用認證] 下拉式清單選取 [用戶端憑證]。

![Client certificates][api-management-mutual-certificates]

從 [用戶端憑證]  下拉式清單選取需要的憑證。 如果有多個憑證，可以藉由主體或指紋的最後四個字元來判斷正確的憑證 (如前文所述)。

![Select certificate][api-management-select-certificate]

按一下 [儲存]  以將組態變更儲存至 API。

> 此變更將立即生效，且該 API 之作業的呼叫將使用憑證以在後端伺服器上進行驗證。
> 
> 

![Save API changes][api-management-save-api]

> 當您將憑證指定用於 API 後端服務的閘道驗證時，憑證遂成為該 API 之原則的一部分，因此可以在原則編輯器中檢視。
> 
> 

![Certificate policy][api-management-certificate-policy]

## <a name="self-signed-certificates"></a>自我簽署憑證

如果您使用自我簽署憑證，則需要停用信任鏈結驗證，API 管理才能與後端系統通訊，否則會傳回 500 錯誤碼。 若要這樣設定，請使用 [`New-AzureRmApiManagementBackend`](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (適用於新的後端) 或 [`Set-AzureRmApiManagementBackend`](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (適用於現有的後端) PowerShell Cmdlet，並將 `-SkipCertificateChainValidation` 參數設定為 `True`。

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="next-steps"></a>後續步驟
如需其他用來保護您後端服務方式的詳細資訊，例如 HTTP Basic 或共用密碼驗證，請參閱下列影片。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Last-mile-Security/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



