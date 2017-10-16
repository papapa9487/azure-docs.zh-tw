---
title: "Azure CDN 規則引擎比對條件 | Microsoft Docs"
description: "Azure CDN 規則引擎比對條件和功能的參考文件。"
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: f4886b1d78dfa87cf25737fb46c12b5963034f27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Azure CDN 規則引擎比對條件
本主題會針對 Azure 內容傳遞網路 (CDN) [規則引擎](cdn-rules-engine.md)列出可用比對條件的詳細說明。

規則的第二個部分是比對條件。 比對條件會識別特定類型的要求，系統將針對這類要求執行一組功能。

例如，可能會使用它來篩選對於特定位置之內容的要求、產生自特殊 IP 位址或國家/地區的要求，或是依標頭資訊篩選要求。

## <a name="always"></a>一律

「一律」比對條件可設計來將一組預設功能套用至所有要求。

## <a name="device"></a>裝置

「裝置」比對條件可識別從行動裝置根據其屬性所提出的要求。  行動裝置偵測可以透過 [WURFL](http://wurfl.sourceforge.net/) 達成。  以下列出 WURFL 功能和其 CDN 規則引擎變數。

> [!NOTE] 
> 下列變數在**修改用戶端要求標頭**和**修改用戶端回應標頭**功能中受到支援。

功能 | 變數 | 說明 | 範例值
-----------|----------|-------------|----------------
品牌名稱 | %{wurfl_cap_brand_name} | 字串，表示裝置的品牌名稱。 | Samsung
裝置作業系統 | %{wurfl_cap_device_os} | 字串，表示裝置上安裝的作業系統。 | iOS
裝置作業系統版本 | %{wurfl_cap_device_os_version} | 字串，表示裝置上安裝的作業系統版本號碼。 | 1.0.1
雙向 | %{wurfl_cap_dual_orientation} | 布林值，表示裝置是否支援雙向。 | true
HTML 慣用 DTD | %{wurfl_cap_html_preferred_dtd} | 字串，表示 HTML 內容的行動裝置慣用文件類型定義 (DTD)。 | 無<br/>xhtml_basic<br/>html5
內嵌影像 | %{wurfl_cap_image_inlining} | 布林值，表示裝置是否支援 Base64 編碼映像。 | false
是 Android | %{wurfl_vcap_is_android} | 布林值，表示裝置是否使用 Android 作業系統。 | true
是 iOS | %{wurfl_vcap_is_ios} | 布林值，表示裝置是否使用 iOS。 | false
是智慧型電視 | %{wurfl_cap_is_smarttv} | 布林值，表示裝置是否為智慧型電視。 | false
是智慧型手機 | %{wurfl_vcap_is_smartphone} | 布林值，表示裝置是否為智慧型手機。 | true
是平板電腦 | %{wurfl_cap_is_tablet} | 布林值，表示裝置是否為平板電腦。 這是獨立於作業系統的描述。 | true
是無線裝置 | %{wurfl_cap_is_wireless_device} | 布林值，表示裝置是否視為無線裝置。 | true
行銷名稱 | %{wurfl_cap_marketing_name} | 字串，表示裝置的行銷名稱。 | BlackBerry 8100 Pearl
行動瀏覽器 | %{wurfl_cap_mobile_browser} | 字串，表示用來向裝置要求內容的瀏覽器。 | Chrome
行動瀏覽器版本 | %{wurfl_cap_mobile_browser_version} | 字串，表示用來向裝置要求內容的瀏覽器版本。 | 31
模型名稱 | %{wurfl_cap_model_name} | 字串，表示裝置的模型名稱。 | s3
漸進式下載 | %{wurfl_cap_progressive_download} | 布林值，表示裝置是否支援音訊/視訊仍在下載時播放。 | true
發行日期 | %{wurfl_cap_release_date} | 字串，表示裝置新增至 WURFL 資料庫的年和月。<br/><br/>格式： `yyyy_mm` | 2013_december
解析高度 | %{wurfl_cap_resolution_height} | 整數，表示裝置的高度，以像素為單位。 | 768
解析寬度 | %{wurfl_cap_resolution_width} | 整數，表示裝置的寬度，以像素為單位。 | 1024


## <a name="location"></a>位置

這些比對條件是設計來根據要求者的位置識別要求。

名稱 | 目的
-----|--------
AS 號碼 | 識別源自特定網路的要求。
國家 (地區) | 識別源自特定國家/地區的要求。


## <a name="origin"></a>來源

這些比對條件是設計來識別指向 CDN 儲存體或客戶原始伺服器的要求。

名稱 | 目的
-----|--------
CDN 原點 | 識別對儲存於 CDN 儲存體之內容的要求。
客戶原點 | 識別對儲存於特定客戶原始伺服器上之內容的要求。


## <a name="request"></a>要求

這些比對條件是設計來根據要求的位置識別它們。

名稱 | 目的
-----|--------
用戶端 IP 位址 | 識別源自特定 IP 位址的要求。
Cookie 參數 | 檢查與每個適用於指定值之要求相關聯的 Cookie。
Cookie 參數 Regex | 檢查與每個適用於規則運算式之要求相關聯的 Cookie。
邊緣 Cname | 識別指向特定邊緣 CNAME 的要求。
轉介網域 | 識別從指定主機名稱轉介的要求。
要求標頭常值 | 識別包含設為指定值之指定標頭的要求。
要求標頭 Regex | 識別包含指定標頭的要求，該標頭已設定為符合特定規則運算式的值。
要求標頭萬用字元 | 識別包含指定標頭的要求，該標頭已設定為符合特定模式的值。
要求方法 | 依其 HTTP 方法來識別要求。
要求配置 | 依其 HTTP 通訊協定來識別要求。

## <a name="url"></a>URL

這些比對條件是設計來根據要求的 URL 來識別它們。

名稱 | 目的
-----|--------
URL 路徑目錄 | 依其相對路徑來識別要求。
URL 路徑的副檔名 | 依其副檔名來識別要求。
URL 路徑的檔案名稱 | 依其檔案名稱來識別要求。
URL 路徑常值 | 比較要求的相對路徑與指定的值。
URL 路徑 Regex | 比較要求的相對路徑與指定的規則運算式。
URL 路徑萬用字元 | 比較要求的相對路徑與指定的模式。
URL 查詢常值 | 比較要求的查詢字串與指定的值。
URL 查詢參數 | 識別包含指定查詢字串參數的要求，該參數已設定為符合特定模式的值。
URL 查詢 Regex | 識別包含指定查詢字串參數的要求，該參數已設定為符合特定規則運算式的值。
URL 查詢萬用字元 | 根據要求的查詢字串來比較指定的值。


## <a name="next-steps"></a>後續步驟
* [Azure CDN 概觀](cdn-overview.md)
* [規則引擎參考](cdn-rules-engine-reference.md)
* [規則引擎條件運算式](cdn-rules-engine-reference-conditional-expressions.md)
* [規則引擎功能](cdn-rules-engine-reference-features.md)
* [使用規則引擎覆寫預設的 HTTP 行為](cdn-rules-engine.md)

