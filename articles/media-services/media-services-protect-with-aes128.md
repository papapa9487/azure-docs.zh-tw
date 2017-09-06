---
title: "使用 AES-128 動態加密和金鑰傳遞服務 | Microsoft Docs"
description: "Microsoft Azure 媒體服務可讓您傳遞您使用 128 位元加密金鑰加密的內容。 媒體服務也提供加密金鑰傳遞服務，將加密金鑰傳遞至授權的使用者。 本主題展示如何利用 AES-128 動態加密，以及使用金鑰傳遞服務。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: ae1b36c26e688e74eb8fcc1a4cdbd3be0c014c08
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>使用 AES-128 動態加密和金鑰傳遞服務
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>概觀
> [!NOTE]
> 如需如何使用 AES 加密保護媒體內容的概觀，請參閱[此視訊](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)。
> 
> 

Microsoft Azure 媒體服務可讓您傳遞您使用進階加密標準 (AES) (使用 128 位元加密金鑰) 加密的 Http-Live-Streaming (HLS) 和 Smooth Streaming 。 媒體服務也提供加密金鑰傳遞服務，將加密金鑰傳遞至授權的使用者。 如果您想要媒體服務加密資產，則需要建立加密金鑰 與資產的關聯，同時設定金鑰的授權原則。 播放程式要求串流時，媒體服務便會使用 AES 加密，使用指定的金鑰動態加密您的內容。 為了將串流解密，播放程式將從金鑰傳遞服務要求金鑰。 為了決定使用者是否有權取得金鑰，服務會評估為金鑰指定的授權原則。

媒體服務支援多種方式來驗證提出金鑰要求的使用者。 內容金鑰授權原則可能會有一個或多個授權限制：open 或 token 限制。 權杖限制原則必須伴隨著安全權杖服務 (STS) 所發出的權杖。 媒體服務支援[簡單 Web 權杖](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) 格式和 [JSON Web 權杖](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) 格式的權杖。 如需詳細資訊，請參閱 [設定內容金鑰的授權原則](media-services-protect-with-aes128.md#configure_key_auth_policy)。

若要利用動態加密，您需有一個資源，其中包含一組多位元速率 MP4 檔案或多位元速率 Smooth Streaming 來源檔案。 您也需要設定資產的傳遞原則 (本主題稍後會加以描述)。 然後，根據串流 URL 中指定的格式，隨選資料流處理伺服器將確保以您所選擇的通訊協定傳遞串流。 因此，您只需要儲存及支付一種儲存格式之檔案的費用，媒體服務會根據用戶端的要求建置及提供適當的回應。

本主題將有助於開發人員開發提供受保護媒體的應用程式。 本主題將展示如何利用授權原則設定金鑰傳遞服務，這樣只有授權的用戶端才會收到加密金鑰。 它也會展示如何使用動態加密。


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>使用 AES-128 動態加密和金鑰傳遞服務工作流程

以下是您利用 AES 加密資產、使用媒體服務金鑰傳遞服務，同時也使用動態加密時將需要執行的一般步驟。

1. [建立資產並將檔案上傳到資產](media-services-protect-with-aes128.md#create_asset)。
2. [將包含檔案的資產編碼為自適性位元速率 MP4 集](media-services-protect-with-aes128.md#encode_asset)。
3. [建立內容金鑰，並將它與編碼的資產產生關聯](media-services-protect-with-aes128.md#create_contentkey)。 在媒體服務中，內容金鑰包含資產的加密金鑰。
4. [設定內容金鑰的授權原則](media-services-protect-with-aes128.md#configure_key_auth_policy)。 內容金鑰授權原則必須由您設定，而且用戶端必須符合條件，才能將內容金鑰傳遞給用戶端。
5. [設定資產的傳遞原則](media-services-protect-with-aes128.md#configure_asset_delivery_policy)。 傳遞原則組態包括：主要取得 URL 和初始化向量 (IV) (AES 128 會在加密和解密時要求提供相同的 IV)、傳送通訊協定 (例如，MPEG DASH、HLS、Smooth Streaming 或全部)、動態加密的類型 (例如，信封或沒有動態加密)。

    您可以將不同的原則套用至相同資產上的每一個通訊協定。 例如，您可以將 PlayReady 加密套用到 Smooth/DASH，以及將 AES 信封加密套用到 HLS。 傳遞原則中未定義的任何通訊協定 (例如，您加入單一原則，它只有指定 HLS 做為通訊協定) 將會遭到封鎖無法串流。 這個狀況的例外情形是您完全沒有定義資產傳遞原則之時。 那麼，將允許所有通訊協定，不受阻礙。

6. [建立隨選定位器](media-services-protect-with-aes128.md#create_locator) 。

本主題也說明 [用戶端應用程式如何從金鑰傳遞服務要求金鑰](media-services-protect-with-aes128.md#client_request)。

您會在本主題結尾處發現完整的.NET [範例](media-services-protect-with-aes128.md#example) 。

下圖示範上述的工作流程。 這裡的權杖用於驗證。

![利用 AES 128 保護](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

本主題的其餘部分會提供詳細的說明、程式碼範例，以及展示如何達成上述工作之主題的連結。

## <a name="current-limitations"></a>目前的限制
如果您加入或更新您的資產傳遞原則，您必須刪除現有的定位程式 (如果有的話)，並建立新的定位器。

## <a id="create_asset"></a>建立資產並將檔案上傳到資產
為了管理、編碼及串流處理您的視訊，您必須先將內容上傳到 Microsoft Azure 媒體服務。 一旦上傳，您的內容就會安全地儲存在雲端，以進一步進行處理和串流處理。 

如需詳細資訊，請參閱 [上傳檔案到媒體服務帳戶](media-services-dotnet-upload-files.md)。

## <a id="encode_asset"></a>將包含檔案的資產編碼為自適性位元速率 MP4 集
使用動態加密時，您只需建立一個資源，其中包含一組多位元速率 MP4 檔案或多位元速率 Smooth Streaming 來源檔案。 然後隨選資料流處理伺服器會根據資訊清單或片段要求中的指定格式，確保您以自己選擇的通訊協定接收串流。 因此，您只需要儲存及支付一種儲存格式之檔案的費用，媒體服務會根據用戶端的要求建置及提供適當的回應。 如需詳細資訊，請參閱 [動態封裝概觀](media-services-dynamic-packaging-overview.md) 主題。

>[!NOTE]
>建立 AMS 帳戶時，**預設**串流端點會新增至 [已停止] 狀態的帳戶。 若要開始串流內容並利用動態封裝和動態加密功能，您想要串流內容的串流端點必須處於 [執行中] 狀態。 
>
>此外，為了能夠使用動態封裝和動態加密功能，您的資產必須包含一組調適性位元速率 MP4 或調適性位元速率 Smooth Streaming 檔案。

如需如何編碼的指示，請參閱 [如何使用 Media Encoder Standard 為資產編碼](media-services-dotnet-encode-with-media-encoder-standard.md)。

## <a id="create_contentkey"></a>建立內容金鑰並將它與編碼的資產產生關聯
在媒體服務中，內容金鑰包含您要加密資產時使用的金鑰。

如需詳細資訊，請參閱 [建立內容金鑰](media-services-dotnet-create-contentkey.md)。

## <a id="configure_key_auth_policy"></a>設定內容金鑰的授權原則
媒體服務支援多種方式來驗證提出金鑰要求的使用者。 內容金鑰授權原則必須由您設定，而且用戶端 (播放器) 必須符合條件，才能將金鑰傳遞給用戶端。 內容金鑰授權原則可能會有一個或多個授權限制：Open、權杖限制或 IP 限制。

如需詳細資訊，請參閱 [設定內容金鑰授權原則](media-services-dotnet-configure-content-key-auth-policy.md)。

## <a id="configure_asset_delivery_policy"></a>設定資產傳遞原則
設定資產的傳遞原則。 資產傳遞原則組態包括：

* 金鑰取得 URL。 
* 用於信封加密的初始化向量 (IV)。 AES 128 會在加密和解密時要求提供相同的 IV。 
* 資產傳遞通訊協定 (例如，MPEG DASH、HLS、Smooth Streaming 或全部)。
* 動態加密的類型 (例如，AES 信封) 或沒有動態加密。 

如需詳細資訊，請參閱 [設定資產傳遞原則 ](media-services-rest-configure-asset-delivery-policy.md)。

## <a id="create_locator"></a>建立隨選串流定位器以取得串流 URL
您必須為您的使用者提供 Smooth、DASH 或 HLS 的串流 URL。

> [!NOTE]
> 如果您加入或更新您的資產傳遞原則，您必須刪除現有的定位程式 (如果有的話)，並建立新的定位器。
> 
> 

如需有關如何發佈資產，並建置串流 URL 的指示，請參閱 [建置串流 URL](media-services-deliver-streaming-content.md)。

## <a name="get-a-test-token"></a>取得測試權杖
根據用於金鑰授權原則的權杖限制取得測試權杖。

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

您可以使用 [AMS 播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html) 來測試您的串流。

## <a id="client_request"></a>您的用戶端如何從金鑰傳遞服務要求金鑰？
在上一個步驟中，您可以建構指向資訊清單檔案的 URL。 您的用戶端必須從串流資訊清單檔案擷取所需的資訊，才能向金鑰傳遞服務提出要求。

### <a name="manifest-files"></a>資訊清單檔案
用戶端必須從資訊清單檔案擷取 URL (其中也包含內容金鑰識別碼 (kid)) 值。 用戶端接著會嘗試從金鑰傳遞服務取得加密金鑰。 用戶端也必須擷取 IV 值，並使用它解密串流。下列程式碼片段展示 Smooth Streaming 資訊清單的 <Protection> 項目。

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

在 HLS 的案例中，根資訊清單會分成區段檔案。 

例如，根資訊清單是︰http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl)，其中包含區段檔案名稱的清單。

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

如果您在文字編輯器中開啟其中一個區段檔案 (例如，http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl)，它應該包含表示檔案已加密的 #EXT-X-KEY。

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>如果您想要在 Safari 中播放 AES 加密的 HLS，請參閱[這篇部落格](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)。

### <a name="request-the-key-from-the-key-delivery-service"></a>從金鑰傳遞服務要求金鑰

下列程式碼展示如何使用金鑰傳遞 Uri (擷取自資訊清單) 和權杖 (本主題不會討論如何從安全性權杖服務取得簡單 Web 權杖)，將要求傳送至媒體服務金鑰傳遞服務。

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }

## <a name="protect-your-content-with-aes-128-using-net"></a>使用 .NET 透過 AES-128 保護內容

### <a name="create-and-configure-a-visual-studio-project"></a>建立和設定 Visual Studio 專案

1. 設定您的開發環境並在 app.config 檔案中填入連線資訊，如[使用 .NET 進行 Media Services 開發](media-services-dotnet-how-to-use.md)中所述。 
2. 將下列項目新增至 app.config 檔案中定義的 **appSettings**：

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>範例

以本章節中所顯示的程式碼覆寫 Program.cs 檔案中的程式碼。
 
>[!NOTE]
>對於不同的 AMS 原則 (例如 Locator 原則或 ContentKeyAuthorizationPolicy) 有 1,000,000 個原則的限制。 如果您一律使用相同的日期 / 存取權限，例如，要長時間維持就地 (非上載原則) 的定位器原則，您應該使用相同的原則識別碼。 如需詳細資訊，請參閱 [這個](media-services-dotnet-manage-entities.md#limit-access-policies) 主題。

請務必更新變數，以指向您的輸入檔案所在的資料夾。

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Security.Cryptography;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

    namespace AESDynamicEncryptionAndKeyDeliverySvc
    {
        class Program
        {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        // A Uri describing the issuer of the token.  
        // Must match the value in the token for the token to be considered valid.
        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        // The Audience or Scope of the token.  
        // Must match the value in the token for the token to be considered valid.
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateEnvelopeTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine();

            if (tokenRestriction)
            tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
            AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
            // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
            // back into a TokenRestrictionTemplate class instance.
            TokenRestrictionTemplate tokenTemplate =
                TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

            // Generate a test token based on the data in the given TokenRestrictionTemplate.
            // Note, you need to pass the key id Guid because we specified 
            // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
            Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

            //The GenerateTestToken method returns the token without the word “Bearer” in front
            //so you have to add it in front of the token string. 
            string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
            Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
            Console.WriteLine();
            }

            // You can use the bit.ly/aesplayer Flash player to test the URL 
            // (with open authorization policy). 
            // Paste the URL and click the Update button to play the video. 
            //
            string URL = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Smooth Streaming Url: {0}/manifest", URL);
            Console.WriteLine();
            Console.WriteLine("HLS Url: {0}/manifest(format=m3u8-aapl)", URL);
            Console.WriteLine();

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
            Console.WriteLine("File does not exist.");
            return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);


            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
            processor,
            "Adaptive Streaming",
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }

        static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
        {
            // Create envelope encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                keyId,
                contentKey,
                "ContentKey",
                ContentKeyType.EnvelopeEncryption);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions 
            // and create authorization policy             
            IContentKeyAuthorizationPolicy policy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Open Authorization Policy").Result;

            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

            ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
            Name = "HLS Open Authorization Policy",
            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
            Requirements = null // no requirements needed for HLS
        };

            restrictions.Add(restriction);

            IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy",
            ContentKeyDeliveryType.BaselineHttp,
            restrictions,
            "");

            policy.Options.Add(policyOption);

            // Add ContentKeyAutorizationPolicy to ContentKey
            contentKey.AuthorizationPolicyId = policy.Id;
            IContentKey updatedKey = contentKey.UpdateAsync().Result;
            Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            IContentKeyAuthorizationPolicy policy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("HLS token restricted authorization policy").Result;

            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

            ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "Token Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString
            };

            restrictions.Add(restriction);

            //You could have multiple options 
            IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "Token option for HLS",
            ContentKeyDeliveryType.BaselineHttp,
            restrictions,
            null  // no key delivery data is needed for HLS
            );

            policy.Options.Add(policyOption);

            // Add ContentKeyAutorizationPolicy to ContentKey
            contentKey.AuthorizationPolicyId = policy.Id;
            IContentKey updatedKey = contentKey.UpdateAsync().Result;
            Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

            return tokenTemplateString;
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);

            string envelopeEncryptionIV = Convert.ToBase64String(GetRandomBuffer(16));

            // When configuring delivery policy, you can choose to associate it
            // with a key acquisition URL that has a KID appended or
            // or a key acquisition URL that does not have a KID appended  
            // in which case a content key can be reused. 

            // EnvelopeKeyAcquisitionUrl:  contains a key ID in the key URL.
            // EnvelopeBaseKeyAcquisitionUrl:  the URL does not contains a key ID

            // The following policy configuration specifies: 
            // key url that will have KID=<Guid> appended to the envelope and
            // the Initialization Vector (IV) to use for the envelope encryption.

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeKeyAcquisitionUrl, keyAcquisitionUri.ToString()}
            };

            IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
        }

        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the  
            // collection of files in the asset. 

            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                EndsWith(".ism")).
                FirstOrDefault();

            // Create a 30-day readonly access policy. 
            // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin. 
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file. 
            return originLocator.Path + assetFile.Name;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int size)
        {
            byte[] randomBytes = new byte[size];
            using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
            {
            rng.GetBytes(randomBytes);
            }

            return randomBytes;
        }
        }
    }


## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


