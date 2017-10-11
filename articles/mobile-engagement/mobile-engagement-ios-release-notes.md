---
title: "Azure Mobile Engagement iOS SDK 版本資訊 | Microsoft Docs"
description: "Azure Mobile Engagement iOS SDK 的最新更新與程序"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 9bdaa57f9902373ccf796ff109332b64c66bf9e7
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Azure Mobile Engagement iOS SDK 版本資訊

## <a name="410-07172017"></a>4.1.0 (07/17/2017)
* 修正在背景清除徽章。
* 修正在 XCode 9 上關於 API 未在主要佇列呼叫的警告。
* 修正觸達輪詢中的記憶體流失。
* 停止支援 iOS 6.X。 從此版本開始，您的應用程式部署目標必須至少為 iOS 7。

## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* 改善在背景中的記錄傳送。

## <a name="400-09122016"></a>4.0.0 (09/12/2016)
* iOS 10 裝置上有固定的通知不會採取動作。
* 取代 XCode 7。

## <a name="324-06302016"></a>3.2.4 (06/30/2016)
* 修正技術記錄和其他記錄之間的彙總。

## <a name="323-06072016"></a>3.2.3 (06/07/2016)
* 修正當應用程式在背景時不回報傳遞回饋的錯誤。
* 將技術記錄的傳送最佳化。

## <a name="322-04072016"></a>3.2.2 (04/07/2016)
* 修正 HTTP 要求取消有時會導致損毀的錯誤。

## <a name="321-12112015"></a>3.2.1 (12/11/2015)
* 修正當新應用程式執行個體由具有深度連結的通知觸發時造成的延遲。

## <a name="320-10082015"></a>3.2.0 (10/08/2015)
* 啟用 SDK 中的 Bitcode 以便使用 **Xcode 7**。
* 已修正與應用程式內通知相關的錯誤。
* 讓應用程式內通知在發生電池電力不足與其他這類案例時更可靠。
* 移除第三方程式庫所產生的額外主控台記錄檔。

## <a name="310-08262015"></a>3.1.0 (2015/08/26)
* 搭配協力廠商程式庫修正 iOS 9 相容性錯誤。 當傳送投票結果、應用程式資訊或是額外的資料時會造成當機。

## <a name="300-06192015"></a>3.0.0 (2015/06/19)
* Mobile Engagement 使用無聲推播通知。
* 停止支援 iOS 4.X。 從此版本開始，您的應用程式部署目標必須至少為 iOS 6。

## <a name="220-05212015"></a>2.2.0 (05/21/2015)
* 針對 < iOS 6 之裝置的 Mobile Engagement 裝置識別碼現在是根據在安裝時產生的 GUID。

## <a name="210-04242015"></a>2.1.0 (2015/04/24)
* 新增 Swift 相容性。
* 現在按一下通知時，動作 URL 會在應用程式開啟後立即執行。
* 在 SDK 封裝中加入缺少的標頭檔案。
* 已修正 Mobile Engagement 當機報告程式停用時的問題。

## <a name="200-02172015"></a>2.0.0 (2015/02/17)
* Azure Mobile Engagement 的最初發行版本
* 以連接字串組態取代 appId/sdkKey 組態。
* 已移除從任意 XMPP 實體傳送與接收任意 XMPP 訊息的 API。
* 已移除在裝置之間傳送與接收訊息的 API。
* 增強安全性。
* 已移除 SmartAd 追蹤。
