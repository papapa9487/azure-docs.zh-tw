---
title: "為呼叫 Azure Time Series Insights API 的自訂應用程式設定驗證和授權 | Microsoft Docs"
description: "本教學課程說明如何為呼叫 Azure Time Series Insights API 的自訂應用程式設定驗證和授權"
keywords: 
services: time-series-insights
documentationcenter: 
author: dmdenmsft
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/24/2017
ms.author: dmden
ms.openlocfilehash: abc3b5400e6961a798e1e6bf9e4986304046e845
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API 的驗證和授權

本文說明如何設定可呼叫 Azure Time Series Insights API 的自訂應用程式。

## <a name="service-principal"></a>服務主體

本節說明如何設定應用程式，以代表應用程式存取時 Time Series Insights API。 此應用程式可接著在 Time Series Insights 環境中，使用應用程式認證 (而非使用者認證) 來查詢資料或發佈參考資料。

如果您擁有需要存取 Time Series Insights 的應用程式，您必須在 Time Series Insights 環境中設定一個 Active Directory 應用程式並指派資料存取原則。 以您自己的認證執行 App 是比較好的作法，因為︰

* 您可以對 App 身分識別指派不同於自有權限的權限。 一般而言，這些權限只會限制為 App 必須執行的確切權限。 例如，您可允許應用程式僅讀取特定 Time Series Insights 環境中的資料。
* 如果您的職責變更，就不需要變更應用程式的認證。
* 您可以在執行自動指令碼時，使用憑證或應用程式金鑰自動進行驗證。

本文說明如何透過 Azure 入口網站執行這些步驟。 其中著重在說明單一租用戶應用程式，此應用程式的目的是只在一個組織內執行。 您通常會將單一租用戶應用程式用在組織內執行的企業營運系統應用程式。

設定流程是由三個高階步驟所組成：

1. 在 Azure Active Directory 中建立應用程式。
2. 授權此應用程式存取 Time Series Insights 環境。
3. 使用應用程式識別碼和金鑰來取得 `"https://api.timeseries.azure.com/"` 對象或資源的權杖。 此權杖可用來呼叫 Time Series Insights API。

詳細的步驟如下：

1. 在 Azure 入口網站中，選取 [Azure Active Directory] > [應用程式註冊] > [新應用程式註冊]。

   ![Azure Active Directory 中的新應用程式註冊](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. 指定應用程式的名稱，選取 [Web 應用程式 / API] 作為類型，選取任何有效的 URI 作為 [登入 URL]，然後按一下 [建立]。

   ![在 Azure Active Directory 中建立應用程式](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. 選取新建立的應用程式，並將其應用程式識別碼複製到您慣用的文字編輯器。

   ![複製應用程式識別碼](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. 選取 [金鑰]，輸入金鑰名稱，選取到期日，然後按一下 [儲存]。

   ![選取應用程式金鑰](media/authentication-and-authorization/active-directory-application-keys.png)

   ![輸入金鑰名稱和到期日，然後按一下 [儲存]](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. 將金鑰複製到您慣用的文字編輯器。

   ![複製應用程式金鑰](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. 針對 Time Series Insights 環境，選取 [資料存取原則]，然後按一下 [新增]。

   ![將新的資料存取原則新增至 Time Series Insights 環境](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. 在 [選取使用者] 對話方塊方塊中，貼上應用程式名稱 (來自步驟 2) 或應用程式識別碼 (來自步驟 3)。

   ![在 [選取使用者] 對話方塊中尋找應用程式](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. 選取角色 ([讀者] 可供查詢資料，[參與者] 可供查詢資料和變更參考資料)，然後按一下 [確定]。

   ![在 [選取角色] 對話方塊中挑選讀者或參與者](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. 按一下 [確定] 以儲存原則。

10. 使用應用程式識別碼 (來自步驟 3) 或應用程式金鑰 (來自步驟 5)，代表應用程式取得權杖。 當應用程式呼叫 Time Series Insights API 時，此權杖即可傳入 `Authorization` 標頭。

    如果您使用 C#，您可以使用下列程式碼來代表應用程式取得權杖。 如需完整範例，請參閱[使用 C# 查詢資料](time-series-insights-query-data-csharp.md)。

    ```csharp
    var authenticationContext = new AuthenticationContext(
        "https://login.microsoftonline.com/common",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

## <a name="next-steps"></a>後續步驟

在您的應用程式中使用應用程式識別碼和金鑰。 如需呼叫 Time Series Insights API 的範例程式碼，請參閱[使用 C# 查詢資料](time-series-insights-query-data-csharp.md)。

## <a name="see-also"></a>另請參閱

* [查詢 API](/rest/api/time-series-insights/time-series-insights-reference-queryapi) 可取得完整的查詢 API 參考
* [在 Azure 入口網站中建立服務主體](../azure-resource-manager/resource-group-create-service-principal-portal.md)
