---
title: "Azure Active Directory B2C︰使用語言自訂 | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: sama
ms.openlocfilehash: 3c7c49ee5fbd98762da0eef6f02e7c2f036453cb
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C︰使用語言自訂

>[!NOTE] 
>這項功能處於公開預覽狀態。  建議您在使用這項功能時，使用測試租用戶。  預覽版在進入到公開上市版時應該不會有重大變更，但我們保留進行這類變更以便改進功能的權利。  在您有機會試用功能後，請將關於使用體驗及如何讓它更加完善的意見提供給我們。  您可以透過 Azure 入口網站右上方的笑臉圖示工具來提供意見。   如果您在預覽期間因為有商務需求而需要正式上線使用這項功能，請讓我們知道您的情況，以便我們能為您提供適當的指引與協助。  您可以透過 [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com) 來與我們連絡。

語言自訂可讓您將使用者旅程變更為不同語言，以符合您客戶的需求。  我們提供 36 種語言的翻譯 (請參閱[其他資訊](#additional-information))。  即使您的體驗僅提供單一語言，您也可以自訂頁面上的任何文字以符合您的需求。  

## <a name="how-does-language-customization-work"></a>語言自訂的運作方式為何？
語言自訂可讓您選取使用者旅程所適用的語言。  此功能啟用後，您就可以從應用程式提供查詢字串參數 ui_locales。  當您呼叫 Azure AD B2C 時，我們會將您的頁面轉譯為您指定的地區設定。  使用組態類型可讓您完整控制使用者旅程的語言，並忽略客戶瀏覽器的語言設定。 或者，您也可能不需要對客戶會看見哪種語言擁有這種程度的控制力。  如果您沒有提供 ui_locales 參數，則客戶的使用體驗會由其瀏覽器的設定來決定。  您仍可將各種語言新增為支援的語言，以控制要將使用者旅程轉譯為何種語言。  如果客戶瀏覽器所設定要顯示的語言不是您想要支援的語言，則系統會改為顯示您選取作為支援之文化特性預設值的語言。

1. **ui_locales 所指定的語言** - 在啟用語言自訂後，您的使用者旅程會轉譯為此處指定的語言
2. **瀏覽器所要求的語言** - 如果未指定 ui_locales，則語言會轉譯為瀏覽器所要求的語言 (**如果支援的語言中有包括此語言**)
3. **原則的預設語言** - 如果瀏覽器未指定語言，或指定不支援的語言，語言就會轉譯為原則的預設語言

>[!NOTE]
>如果您使用自訂使用者屬性，則必須提供自己的翻譯。 如需詳細資訊，請參閱[自訂您的字串](#customize-your-strings)。
>

## <a name="support-uilocales-requested-languages"></a>支援 ui_locales 所要求的語言 
藉由對原則啟用「語言自訂」，您現在可以藉由新增 ui_locales 參數來控制使用者旅程的語言。
1. [遵循下列步驟以瀏覽至 Azure 入口網站上的 B2C 功能刀鋒視窗。](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. 瀏覽至您想要啟用轉譯的原則。
3. 按一下 [語言自訂]。
4. 仔細閱讀警告文字。  啟用之後，您就無法關閉「語言自訂」。
5. 開啟該功能，然後按一下 [確定]。
6. 在 [編輯原則] 刀鋒視窗的左上角儲存您的原則。

## <a name="select-which-languages-your-user-journey-supports"></a>選取您的使用者旅程所支援的語言 
建立允許語言清單，以供使用者旅程在您未提供 ui_locales 參數時可進行轉譯。
1. 透過前面的指示來確定您的原則已啟用「語言自訂」。
2. 從 [編輯原則] 刀鋒視窗中，選取 [語言自訂]。
3. 系統會將您帶往 [支援的語言] 刀鋒視窗。  您可以從這裡選取 [新增語言]。
4. 選取您想要支援的所有語言。  

>[!NOTE]
>如果您未提供 ui_locales 參數，則頁面會轉譯為客戶的瀏覽器語言 (但前提是該語言有在此清單上)
>

5. 按一下底部的 [確定]
6. 關閉 [語言自訂] 刀鋒視窗，然後**儲存**原則。

## <a name="customize-your-strings"></a>自訂您的字串
「語言自訂」可讓您自訂使用者旅程中的任何字串。
1. 透過前面的指示來確定您的原則已啟用「語言自訂」。
2. 從 [編輯原則] 刀鋒視窗中，選取 [語言自訂]。
3. 從左側導覽功能表選取 [下載內容]。
4. 選取您想要編輯的頁面。
5. 在下拉式清單中，選取您想要編輯的語言。
6. 按一下 [下載] 。 

上述步驟會向您提供 JSON 檔案，以供您用來開始編輯字串。

### <a name="changing-any-string-on-the-page"></a>變更頁面上的任何字串
1. 在 JSON 編輯器中開啟透過前面的指示所下載的 JSON 檔案。
2. 尋找您想要變更的元素。  您可以尋找您要尋找之字串的 `StringId`，或尋找您要變更的 `Value`。
3. 以您想要顯示的內容來更新 `Value` 屬性。
4. 儲存檔案，並上傳您的變更。

### <a name="changing-extension-attributes"></a>變更擴充屬性
如果您想要變更自訂使用者屬性的字串，或想要將字串新增到 JSON 中，它的格式如下︰
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": false,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```
>[!IMPORTANT]
>如果您需要覆寫字串，請務必將 `Override` 值設為 `true`。  如果該值未變更，則系統會忽略您的輸入。 
>

以自訂使用者屬性的名稱來取代 `<ExtensionAttribute>`。  
以要顯示的新字串來取代 `<ExtensionAttributeValue>`。

### <a name="using-localizedcollections"></a>使用 LocalizedCollections
如果您想要提供設定好的回應值清單，您需要建立 `LocalizedCollections`。  `LocalizedCollections` 是 `Name` 和 `Value` 的配對陣列。  `Name` 是所顯示的內容，`Value` 則是宣告中所傳回的內容。  若要新增 `LocalizedCollections`，其格式如下︰

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": false,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```
>[!IMPORTANT]
>如果您需要覆寫字串，請務必將 `Override` 值設為 `true`。  如果該值未變更，則系統會忽略您的輸入。 
>

* `ElementId` 是使用者屬性，而其回應則是這個 `LocalizedCollections`
* `Name` 是向使用者顯示的值
* `Value` 是選取此選項時在宣告中所傳回的內容

### <a name="upload-your-changes"></a>上傳您的變更
1. 對 JSON 檔案變更完成後，請瀏覽回到 B2C 租用戶。
2. 從 [編輯原則] 刀鋒視窗中，選取 [語言自訂]。
3. 從左側導覽功能表選取 [上傳內容]。
4. 選取您要上傳其變更的頁面。
5. 如果您要上傳的語言先前已提供過 JSON，您必須刪除先前的項目。  您可以按一下該語言右側的 `...`，然後選取 [刪除] 來加以刪除。
6. 按一下左上方的 [新增]。
7. 選取 JSON 檔案的語言。
8. 按一下右邊的資料夾按鈕並瀏覽您的 JSON 檔案。
9. 按一下刀鋒視窗底部的 [上傳] 按鈕。
10. 返回 [編輯原則] 刀鋒視窗，然後按一下 [儲存]。

## <a name="additional-information"></a>其他資訊
### <a name="recommendations-for-language-customization"></a>「語言自訂」的建議
我們建議您只將輸入項目放入您明確要取代之字串的語言資源內。  我們對於出自您所有 JSON 翻譯的編譯檔案大小套用了強制的限制。  過大的檔案會影響使用者旅程的效能。
### <a name="page-ui-customization-labels-are-removed-once-language-customization-is-enabled"></a>「語言自訂」啟用後，系統就會移除頁面 UI 自訂標籤
當您啟用「語言自訂」時，系統會移除您先前使用頁面 UI 自訂對標籤所進行的編輯，但自訂使用者屬性除外。  進行這項變更是為了避免您可以在其中編輯字串的位置發生衝突。  您可以上傳「語言自訂」中的語言資源，來繼續變更您的標籤和其他字串。
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft 致力於提供最新的翻譯來供您使用
我們會持續改善翻譯，並讓它們符合您的需求。  我們會找出全域術語中的錯誤和變更，並製作可在使用者旅程中順暢運作的更新。
### <a name="support-for-right-to-left-languages"></a>支援從右至左的語言
不支援由右至左的語言，如果您需要此功能，請在 [Azure 意見反應](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)投票給這項功能。
### <a name="social-identity-provider-translations"></a>社交識別提供者的翻譯
我們為社交登入提供了 ui_locales OIDC 參數，但某些社交識別提供者 (包括 Facebook 和 Google) 並未加以採用。 
### <a name="browser-behavior"></a>瀏覽器行為
Chrome 和 Firefox 皆會要求自設的語言，如果該語言受支援，則會顯示該語言而不會顯示預設語言。  Edge 目前未要求語言，而是會直接使用預設語言。
### <a name="known-issues"></a>已知問題
* 您目前無法在設定檔編輯原則中上傳 MFA 頁面的語言資源。
* 若要求者為回應類型，系統就不會為值產生 `LocalizedCollections`
### <a name="what-if-i-want-a-language-that-isnt-supported"></a>如果我想使用的語言不受支援會如何？
我們打算提供這項功能的擴充功能，以讓您向「自訂語言」上傳 JSON 資源。  此功能可讓您指定任何語言的名稱和語言代碼，並提供該語言的「所有」翻譯。  如果您需要這項功能，請將您的情況傳送給我們：[aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com)。  

### <a name="what-languages-are-supported"></a>支援哪些語言？

| 語言              | 語言代碼 |
|-----------------------|---------------|
| 孟加拉文                | bn            |
| 捷克文                 | cs            |
| 丹麥文                | da            |
| 德文                | de            |
| 希臘文                 | el            |
| English               | en            |
| 西班牙文               | es            |
| 芬蘭文               | fi            |
| 法文                | fr            |
| 古吉拉特文              | gu            |
| 北印度文                 | hi            |
| 克羅埃西亞文              | hr            |
| 匈牙利文             | hu            |
| 義大利文               | it            |
| 日文              | ja            |
| 坎那達文               | kn            |
| 韓文                | ko            |
| 馬來亞拉姆文             | ml            |
| 馬拉提文               | mr            |
| 馬來文                 | ms            |
| 挪威文 (巴克摩)      | nb            |
| 荷蘭文                 | nl            |
| 旁遮普文               | pa            |
| 波蘭文                | pl            |
| 葡萄牙文 - 巴西   | pt-br         |
| 葡萄牙文 - 葡萄牙 | pt-pt         |
| 羅馬尼亞文              | ro            |
| 俄文               | ru            |
| 斯洛伐克文                | sk            |
| 瑞典文               | sv            |
| 坦米爾文                 | ta            |
| 特拉古文                | te            |
| 泰文                  | th            |
| 土耳其文               | tr            |
| 中文 - 簡體  | zh-hans       |
| 中文 - 繁體 | zh-hant       |
