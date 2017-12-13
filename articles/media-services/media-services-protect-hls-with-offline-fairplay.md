---
title: "使用離線 Apple FairPlay 保護 HLS 內容 - Azure | Microsoft Docs"
description: "本主題提供概觀，並示範如何使用 Azure 媒體服務，利用離線模式的 Apple FairPlay 動態加密您的 HTTP 即時串流 (HLS) 內容。"
services: media-services
keywords: "HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10, FairPlay 串流 (FPS), 離線"
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: bf5828ecd6b6bd2e862c4d7709014ecac47c6be0
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2017
---
# <a name="offline-fairplay-streaming"></a>離線 FairPlay 串流
Microsoft Azure 媒體服務提供一組設計良好的[內容保護服務](https://azure.microsoft.com/services/media-services/content-protection/)，其中涵蓋：
- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128 加密

內容的 DRM/AES 加密會根據各種不同串流處理通訊協定的要求，以動態方式執行。 Azure 媒體服務也會提供 DRM 授權/AES 解密金鑰傳遞服務。

除了透過各種不同的串流處理通訊協定來保護內容進行線上串流處理，受保護內容的離線模式也是一個經常被要求的功能。 下列案例需要離線模式支援：
1. 在無法使用網際網路連線 (例如旅行期間) 時播放；
2. 某些內容提供者可能不允許 DRM 授權傳遞超出國家/地區的邊界。 如果使用者想要在出國旅行時觀賞內容，則需要離線下載。
3. 在某些國家/地區，網際網路可用性和/或頻寬仍受到限制。 使用者可以選擇先下載，以便能以夠高的解析度觀賞內容，來獲得令人滿意的檢視體驗。 在此情況下，更常見的問題不是網路可用性，而是有限的網路頻寬。 OTT/OVP 提供者正在要求提供離線模式支援。

本文涵蓋 FairPlay 串流 (FPS) 離線模式支援，其目標為執行 iOS 10 或更新版本的裝置。 watchOS、tvOS 或 macOS 上的 Safari 等其他 Apple 平台均不支援此功能。

## <a name="preliminary-steps"></a>預備步驟
在 iOS 10+ 裝置上實作適用於 FairPlay 的離線 DRM 之前，您應該先：
1. 熟悉適用於 FairPlay 的線上內容保護。 下列文章或範例中會詳細說明這一點：
- [適用於 Azure 媒體服務的 Apple FairPlay 串流已正式推出](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/) \(英文\)
- [使用 Apple FairPlay 或 Microsoft PlayReady 保護 HLS 內容](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
- [線上 FPS 串流的範例](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/) \(英文\)
2. 從 Apple 開發人員網路取得 FPS SDK。 FPS SDK 包含兩個元件：
- FPS Server SDK，其中包含 KSM (金鑰安全性模組)、用戶端範例、規格，以及一組測試向量；
- FPS Deployment Pack，其中包含 D Function、規格，以及有關如何產生 FPS 憑證、客戶專屬的私密金鑰及應用程式祕密金鑰 (ASK) 的指示。 Apple 只會將 FPS Deployment Pack 發給授權的內容提供者。

## <a name="configuration-in-azure-media-services"></a>Azure 媒體服務中的設定
如需透過 [Azure 媒體服務 .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices) \(英文\) 設定 FPS 離線模式，您必須使用 Azure 媒體服務 .NET SDK 4.0.0.4 版或更新版本，其中提供了設定 FPS 離線模式所需的 API。
如以上假設所示，我們假設您目前的程式碼可用來設定線上模式 FPS 內容保護。 當您的程式碼可用來設定適用於 FPS 的線上模式內容保護之後，您就只需進行下列兩個變更。

## <a name="code-change-in-fairplay-configuration"></a>FairPlay 設定中的程式碼變更
讓我們定義一個稱為 objDRMSettings.EnableOfflineMode 的「啟用離線模式」布林值，在啟用離線 DRM 案例時，此值為 true。 根據這個指標，我們會對 FairPlay 設定進行下列變更：

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-asset-delivery-policy-configuration"></a>資產傳遞原則設定中的程式碼變更
第二個變更是將第三個金鑰新增至字典 Dictionary<AssetDeliveryPolicyConfigurationKey, 字串> 中。
第三個需要新增的 AssetDeliveryPolicyConfigurationKey 如下： 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

執行這個步驟之後，FPS 資產傳遞原則中的 Dictionary<AssetDeliveryPolicyConfigurationKey, 字串> 將包含下列三個項目：
1. AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl 或 AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl，取決於像是所使用的 FPS KSM/金鑰伺服器，以及我們是否想要跨多個資產重複使用相同資產傳遞原則等因素
2. AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
3. AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

現在，已將您的媒體服務帳戶設定為已傳遞的離線 FairPlay 授權。

## <a name="sample-ios-player"></a>範例 iOS 播放程式
首先，我們應該注意 FPS 離線模式支援僅適用於 iOS 10 和更新版本。 我們應該取得 FPS Server SDK (3.0 版或更新版本)，其中包含適用於 FPS 離線模式的文件和範例。 具體來說，FPS Server SDK (3.0 版或更新版本) 包含下列兩個與離線模式相關的項目：
1. 文件：使用 FairPlay 串流和 HTTP 即時串流進行離線播放。 Apple，2016 年 9 月 14 日。 在 FPS Server SDK 4.0 版中，我們已將此文件合併至主要的 FPS 串流文件。
2. 範例程式碼：適用於 FPS 離線模式的 HLSCatalog 範例，位於 \FairPlay Streaming Server SDK v3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\。 在 HLSCatalog 範例應用程式中，特別使用下列程式碼檔案來實作離線模式功能：
- AssetPersistenceManager.swift 程式碼檔案：AssetPersistenceManager 是此範例中的主類別，示範
    - 如何管理 HLS 串流的下載，例如，用來開始和取消下載、刪除使用者裝置的現有資產；
    - 如何監視下載進度。
- AssetListTableViewController.swift 和 AssetListTableViewCell.swift 程式碼檔案：AssetListTableViewController 是此範例的主要介面。 它提供範例可播放、下載、刪除或取消下載的資產清單。 

以下是設定執行中 iOS 播放程式的詳細步驟。 假設您從 FPS Server SDK 4.0.1 版的 HLSCatalog 範例開始。  我們需要進行下列程式碼變更：

在 HLSCatalog\Shared\Managers\ContentKeyDelegate.swift 中，使用下列程式碼實作方法 `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)`：讓 drmUr 成為指派給 HLS 串流 URL 的變數。

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

在 HLSCatalog\Shared\Managers\ContentKeyDelegate.swift 中，實作方法 `requestApplicationCertificate()`。 此實作取決於您會將憑證內嵌於裝置上 (僅限公開金鑰)，還是將憑證裝載於 Web 上。 以下實作會使用我們測試範例中所用的裝載的應用程式憑證。 讓 certUrl 成為包含應用程式憑證 URL 的變數。

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

針對最終整合測試，將在「整合測試」一節中提供影片 URL 和應用程式憑證 URL。

在 HLSCatalog\Shared\Resources\Streams.plist 中，新增您的測試影片 URL，而針對內容金鑰識別碼，我們只需使用 FairPlay 授權取得 URL 搭配 skd 通訊協定作為唯一值。

![離線 FairPlay iOS 應用程式串流](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

針對測試影片 URL、FairPlay 授權取得 URL 及應用程式憑證 URL，如果您已經加以設定，就能使用自己的 URL，或者您可以繼續進行下一節，其中包含了測試範例。

## <a name="integrated-test"></a>整合測試
Azure 媒體服務中已設定三個測試範例，其中涵蓋下列三種案例：
1.  受到 FPS 保護，具備影片、音訊及替代曲目；
2.  受到 FPS 保護，具備影片、音訊，但不具替代曲目；
3.  受到 FPS 保護，僅具備影片，但不具音訊。

您可以在這個[示範網站](http://aka.ms/poc#22) \(英文\) 中找到這些範例，以及裝載於 Azure Web 應用程式中對應的應用程式憑證。
我們已注意到，透過 FPS Server SDK 的 v3 或 v4 範例，如果主要播放清單包含替代音訊，則在離線模式期間，它就只會播放音訊。 因此，我們需要移除替代音訊。 換句話說，在上述三個範例中，(2) 和 (3) 都可在線上及離線模式中運作。 但 (1) 只有在線上串流處理運作正常時，才能在離線模式期間播放音訊。

## <a name="faq"></a>常見問題集
一些有關疑難排解的常見問題集：
- **為什麼在離線模式期間只有音訊播放而沒有影片呢？** 這個行為似乎是範例應用程式的設計使然。 顯示替代曲目 (也就是適用於 HLS 的情況) 時，在離線模式期間，iOS 10 和 iOS 11 都將預設為替代曲目。為了彌補 FPS 離線模式的此一行為，我們需要從串流中移除替代曲目。 若要在 Azure 媒體服務端執行此動作，我們只需新增動態資訊清單篩選 “audio-only=false” 即可。 換句話說，HLS URL 的結尾會是 .ism/manifest(format=m3u8-aapl,audio-only=false)。 
- **當我新增 audio-only=false 之後，為什麼它在離線模式期間仍舊只會播放音訊，而無法播放影片呢？** 根據 CDN 快取金鑰設計，系統可能會快取內容。 您需要清除快取。
- **除了 iOS 10，iOS 11 也支援 FPS 離線模式嗎？** 是，iOS 10 和 iOS 11 都支援 FPS 離線模式。
- **為什麼我在 FPS Server SDK 中找不到＜使用 FairPlay 串流處理和 HTTP 即時串流進行離線播放＞文件呢？** 從 FPS Server SDK 第 4 版開始，我們已將這份文件合併至《FairPlay 串流處理程式設計指南》文件。
- **在以下適用於 FPS 離線模式的 API 中，最後一個參數代表什麼？**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

您可以在[這裡](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet) \(英文\) 找到此 API 的文件。 該參數代表以小時為單位的離線租用持續時間。
- **在 iOS 裝置上的下載/離線檔案結構為何？** 在 iOS 裝置上下載的檔案結構看起來如下 (螢幕擷取畫面)。 `_keys` 資料夾會儲存下載的 FPS 授權，每個授權服務主機一個存放區檔案。 `.movpkg` 資料夾會儲存音訊和影片內容。 第一個資料夾名稱結尾是一個破折號後面接著一個數字，此資料夾包含影片內容。 數值為影片轉譯的 "PeakBandwidth"。 第二個資料夾名稱結尾是一個破折號後面接著 0，此資料夾包含音訊內容。 第三個名為 "Data" 的資料夾包含 FPS 內容的主要播放清單。 Boot.xml 提供 `.movpkg` 資料夾內容的完整描述 (請參閱下方範例 boot.xml 檔案)。

![離線 FairPlay iOS 範例應用程式檔案結構](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

範例 boot.xml 檔案：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="summary"></a>摘要
在本文件中，我們已提供實作 FPS 離線模式的詳細步驟和資訊，包括：
1. 透過 AMS .NET API 設定 Azure 媒體服務內容保護。 這會在 AMS 中設定動態 FairPlay 加密和 FairPlay 授權傳遞。
2. 以 Apple FPS Server SDK 範例為基礎的 iOS 播放程式。 這會設定 iOS 播放程式，此播放程式可在線上串流處理模式或離線模式中播放 FPS 內容。
3. 用來測試離線模式和線上串流處理的範例 FPS 影片。
4. 關於 FPS 離線模式的常見問題集。
