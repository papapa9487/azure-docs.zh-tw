---
title: "Widevine 授權範本概觀 | Microsoft Docs"
description: "本主題提供了用來設定 Widevine 授權之 Widevine 授權範本的概觀。"
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 667ff16dc7608dab2a5b8b1fd7df715da4620ca1
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="widevine-license-template-overview"></a>Widevine 授權範本概觀
## <a name="overview"></a>Overview
Azure 媒體服務現在可讓您設定和要求 Widevine 授權。 使用者播放程式嘗試播放 Widevine 保護內容時，會將要求傳送到授權傳遞服務來取得授權。 如果授權服務核准要求，就會發出傳送給用戶端並可用來解密和播放所指定內容的授權。

Widevine 授權要求會格式化為 JSON 訊息。  

>[!NOTE]
> 您可以選擇建立不帶值而只有 "{}" 的空訊息，如此會建立具有所有預設值的授權範本。 大部分案例的預設工作。 例如，一律必須使用預設值的 MS 型授權遞送案例。 若需要設定 "provider" 與 "content_id" 值，提供者必須符合 Google 的 Widevine 認證。

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>JSON 訊息
| 名稱 | 值 | 說明 |
| --- | --- | --- |
| payload |Base64 編碼的字串 |用戶端傳送的授權要求。 |
| content_id |Base64 編碼的字串 |用來針對每個 content_key_specs.track_type 衍生 KeyId(s) 與內容金鑰的識別碼。 |
| provider |string |用來查閱內容金鑰和原則。 若將 MS 金鑰遞送用於 Widevine 授權遞送，系統會忽略此參數。 |
| policy_name |string |先前已登錄原則的名稱。 選用 |
| allowed_track_types |列舉 |SD_ONLY 或 SD_HD。 控制授權中應該包含的內容金鑰 |
| content_key_specs |JSON 結構的陣列，請參閱下方的 **內容金鑰規格** |更細部控制要傳回的內容金鑰。 如需詳細資料，請參閱以下的內容金鑰規格。  只可以指定 allowed_track_types 和 content_key_specs 中的一個。 |
| use_policy_overrides_exclusively |布林值。 True 或 False |使用 policy_overrides 所指定的原則屬性，並略過先前儲存的所有原則。 |
| policy_overrides |JSON 結構，請參閱以下的 **原則覆寫** |此授權的原則設定。  如果此資產具有預先定義的原則，就會使用這些指定的值。 |
| session_init |JSON 結構，請參閱下方的 **工作階段初始化** |傳遞選擇性的資料給授權。 |
| parse_only |布林值。 true 或 false |剖析授權要求，但不會發出任何授權。 不過，形成授權要求的值會在回應中傳回。 |

## <a name="content-key-specs"></a>內容金鑰規格
如果有預先存在的原則，則不需要在內容金鑰規格中指定任何值。與此內容相關聯且預先存在的原則將用來判斷輸出保護，例如 HDCP 和 CGMS。  如果預先存在的原則未向 Widevine 授權伺服器登錄，內容提供者可以在授權要求中插入值。   

必須對所有追蹤指定每個 content_key_specs，不論選項 use_policy_overrides_exclusively 為何。 

| 名稱 | 值 | 說明 |
| --- | --- | --- |
| content_key_specs track_type |字串 |追蹤類型名稱。 如果授權要求中指定 content_key_specs，則請務必明確指定所有追蹤類型。 未這樣做會導致無法播放過去 10 秒。 |
| content_key_specs  <br/> security_level |uint32 |定義用戶端對於播放的穩健性需求。 <br/> 1 - 以軟體為基礎白箱加密是必要的。 <br/> 2 - 軟體加密和模糊化的解碼器是必要的。 <br/> 3 - 金鑰資料和加密作業必須在支援硬體的受信任執行環境中執行。 <br/> 4 - 內容的加密和解密必須在支援硬體的受信任執行環境中執行。  <br/> 5 - 加密、解密和媒體 (壓縮和未壓縮) 的所有處理必須在支援硬體的受信任執行環境中處理。 |
| content_key_specs <br/> required_output_protection.hdc |字串 - 以下項目的其中一個：HDCP_NONE、HDCP_V1、HDCP_V2 |指出是否需要 HDCP |
| content_key_specs <br/>索引鍵 |Base64  <br/>編碼的字串 |要用於此追蹤的內容金鑰。如果指定，則需要 track_type 或 key_id。  此選項可讓內容提供者插入此追蹤的內容金鑰，而不是讓 Widevine 授權伺服器產生或查閱金鑰。 |
| content_key_specs.key_id |Base64 編碼的二進位字串，16 位元組 |金鑰的唯一識別碼。 |

## <a name="policy-overrides"></a>原則覆寫
| 名稱 | 值 | 說明 |
| --- | --- | --- |
| policy_overrides can_play |布林值。 true 或 false |表示允許播放內容。 預設值為 false。 |
| policy_overrides can_persist |布林值。 true 或 false |表示可以將授權保存到非揮發性儲存體，供離線使用。 預設值為 false。 |
| policy_overrides can_renew |布林值 true 或 false |表示允許更新此授權。 如果為 true，則在授權期間可以透過活動訊號延長。 預設值為 false。 |
| policy_overrides license_duration_seconds |int64 |指出此特定授權的期間。 值為 0 表示期間沒有限制。 預設值為 0。 |
| policy_overrides rental_duration_seconds |int64 |指出允許播放的期間。 值為 0 表示期間沒有限制。 預設值為 0。 |
| policy_overrides playback_duration_seconds |int64 |一旦在授權期間內開始播放的檢視時段。 值為 0 表示期間沒有限制。 預設值為 0。 |
| policy_overrides renewal_server_url |字串 |應該將此授權的所有活動訊號 (更新) 要求導向到指定 URL。 只有在 can_renew 為 true 時才會使用這個欄位。 |
| policy_overrides renewal_delay_seconds |int64 |license_start_time 之後經過幾秒才會第一次嘗試更新。 只有在 can_renew 為 true 時才會使用這個欄位。 預設值為 0 |
| policy_overrides renewal_retry_interval_seconds |int64 |指定若發生失敗，後續授權更新要求之間的延遲秒數。 只有在 can_renew 為 true 時才會使用這個欄位。 |
| policy_overrides renewal_recovery_duration_seconds |int64 |時段，在此時段當嘗試進行更新時允許繼續播放，不過因為授權伺服器發生後端問題而未成功。 值為 0 表示期間沒有限制。 只有在 can_renew 為 true 時才會使用這個欄位。 |
| policy_overrides renew_with_usage |布林值 true 或 false |表示開始使用時應該傳送授權以進行更新。 只有在 can_renew 為 true 時才會使用這個欄位。 |

## <a name="session-initialization"></a>工作階段初始化
| 名稱 | 值 | 說明 |
| --- | --- | --- |
| provider_session_token |Base64 編碼的字串 |此工作階段權杖會傳回到授權，並且會在後續的更新作業中存在。  工作階段權杖不會在工作階段之外保存。 |
| provider_client_token |Base64 編碼的字串 |要在授權回應中傳回的用戶端權杖。  如果授權要求包含用戶端權杖，則會忽略此值。 用戶端權杖會在授權工作階段之外保存。 |
| override_provider_client_token |布林值。 true 或 false |如果為 false 並且授權要求包含用戶端權杖，請使用來自要求的權杖，即使此結構中已指定用戶端權杖亦然。  如果為 true，則一律使用這個結構中指定的權杖。 |

## <a name="configure-your-widevine-licenses-using-net-types"></a>使用 .NET 型別設定您的 Widevine 授權
媒體服務提供可讓您設定 Widevine 授權的 .NET API。 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>媒體服務 .NET SDK 中所定義的類別
以下是這些類型的定義。

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>範例
下列範例示範如何使用 .NET API 來設定簡單的 Widevine 授權。

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>另請參閱
[使用 PlayReady 和/或 Widevine 動態 Common Encryption](media-services-protect-with-drm.md)


