---
title: "如何使用 .NET 設定用於媒體服務開發的電腦"
description: "了解將 Media Services SDK for .NET 用於媒體服務的必要條件。 同時了解如何建立 Visual Studio 應用程式。"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/23/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 15828bc74937a036871b26493498232ec7cf6f06
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="media-services-development-with-net"></a>使用 .NET 進行媒體服務開發
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

本主題討論如何使用 .NET 開始開發媒體服務應用程式。

**Azure Media Services .NET SDK 程式庫** 可讓您使用 .NET 對媒體服務進行程式設計。 為了讓使用 .NET 進行開發更為簡單，會提供 **Azure Media Services .NET SDK 延伸模組** 程式庫。 此程式庫包含一組延伸方法和協助程式函數，以簡化 .NET 程式碼。 這兩個程式庫都是透過 **NuGet** 和 **GitHub** 取得。

## <a name="prerequisites"></a>必要條件
* 新的或現有 Azure 訂用帳戶中的媒體服務帳戶。 請參閱主題 [如何建立媒體服務帳戶](media-services-portal-create-account.md)。
* 作業系統：Windows 10、Windows 7、Windows 2008 R2 或 Windows 8。
* .NET Framework 4.5。
* Visual Studio。

## <a name="create-and-configure-a-visual-studio-project"></a>建立和設定 Visual Studio 專案
本節說明如何在 Visual Studio 中建立專案並設定來進行媒體服務開發。  在此案例中，專案是 C# Windows 主控台應用程式，但這裡顯示的設定步驟也適用於可以為媒體服務應用程式建立的其他專案類型 (例如，Windows Forms 應用程式或 ASP.NET Web 應用程式)。

本節顯示如何使用 **NuGet** 新增 Media Services .NET SDK 延伸模組和其他相依程式庫。

或者，您可以從 GitHub 取得最新 Media Services .NET SDK 位元 ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) 或 [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions))、建置方案，並新增至用戶端專案的參考。 所有必要相依性皆會自動下載並解壓縮。

1. 在 Visual Studio 中，建立新的 C# 主控台應用程式。 輸入 [名稱]、[位置] 和 [方案名稱]，然後按一下 [確定]。
2. 建置方案。
3. 使用 **NuGet** 來安裝和新增 **Azure 媒體服務 .NET SDK 延伸模組** (**windowsazure.mediaservices.extensions**)。 安裝這個封裝，也會安裝 **Media Services .NET SDK** ，並新增所有其他必要相依性。
   
    確定您已安裝 NuGet 的最新版本。 如需詳細資訊和安裝指示，請參閱 [NuGet](http://nuget.codeplex.com/)。
4. 在 [方案總管] 中，於專案名稱上按一下滑鼠右鍵，然後選擇 [管理 NuGet 封裝]。
   
    [管理 NuGet 封裝] 對話方塊隨即出現。
5. 在線上資源庫中，搜尋 [Azure MediaServices 延伸模組]，並選擇 [Azure Media Services .NET SDK 延伸模組]，然後按一下 [安裝] 按鈕。
   
    會修改專案，並新增 Media Services .NET SDK 延伸模組、Media Services .NET SDK 和其他相依組件的參考。
6. 若要提升更乾淨的開發環境，請考慮啟用 [NuGet 封裝還原]。 如需詳細資訊，請參閱 [NuGet 封裝還原](http://docs.nuget.org/consume/package-restore)。
7. 加入 **System.Configuration** 組件的參考。 此組件包含用來存取組態檔 (例如 App.config) 的 System.Configuration.**ConfigurationManager** 類別。
   
    若要使用 [管理參考] 對話方塊新增參考，請以滑鼠右鍵按一下 [方案總管] 中的專案名稱。 然後，依序選取 [加入] 和 [參考]。
   
    [管理參考] 對話方塊隨即出現。
8. 在 .NET Framework 組件下，尋找並選取 System.Configuration 組件，然後按 [確定]。
9. 開啟 App.config 檔案並將 *appSettings* 區段新增至檔案。     
   
    設定連接媒體服務 API 時所需的值。 如需詳細資訊，請參閱[使用 Azure AD 驗證存取 Azure 媒體服務 API](media-services-use-aad-auth-to-access-ams-api.md)。 

    如果您使用[使用者驗證](media-services-use-aad-auth-to-access-ams-api.md#types-of-authentication)，您的設定檔可能會有 Azure AD 租用戶網域和 AMS REST API 端點的值。
    
    >[!Important]
    >Azure 媒體服務文件集中大多數的程式碼範例，以使用者 (互動式) 的驗證類型連線到 AMS API。 這種驗證方法適用於管理或監控原生應用程式：行動應用程式、Windows 應用程式和主控台應用程式。 這種驗證方法不適用於伺服器、Web 服務、API 類型的應用程式。  如需詳細資訊，請參閱[使用 Azure AD 驗證存取 AMS API](media-services-use-aad-auth-to-access-ams-api.md)。

        <configuration>
        ...
            <appSettings>
              <add key="AADTenantDomain" value="YourAADTenantDomain" />
              <add key="MediaServiceRESTAPIEndpoint" value="YourRESTAPIEndpoint" />
            </appSettings>

        </configuration>

10. 在 Program.cs 檔案的開頭，使用下列程式碼來覆寫現有的 **using** 陳述式。
           
        using System;
        using System.Configuration;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Collections.Generic;
        using System.Linq;

現在，您可以開始開發媒體服務應用程式。    

## <a name="example"></a>範例

以下是一個小範例，它會連接到 AMS API，並列出所有可用的媒體處理器。
    
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];
    
        private static CloudMediaContext _context = null;
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    
            // List all available Media Processors
            foreach (var mp in _context.MediaProcessors)
            {
                Console.WriteLine(mp.Name);
            }
    
        }

##<a name="next-steps"></a>後續步驟

現在[您可以連接到 AMS API](media-services-use-aad-auth-to-access-ams-api.md)並開始[開發](media-services-dotnet-get-started.md)。


## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


