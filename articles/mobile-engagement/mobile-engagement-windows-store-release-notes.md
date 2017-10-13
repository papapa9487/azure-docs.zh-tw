---
title: "Azure Mobile Engagement Windows 通用 app SDK 版本資訊 | Microsoft Docs"
description: "Azure Mobile Engagement - Windows 通用 app SDK 版本資訊"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: de938ce5-93d5-4218-9e33-10eef102bd61
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: dc5529a9e8f4eba867732f719ca8fff718c00d5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-sdk-release-notes"></a>Windows 通用 app SDK 版本資訊
## <a name="341-11032016"></a>3.4.1 (11/03/2016)

* 穩定性改進。

## <a name="340-04192016"></a>3.4.0 (04/19/2016)
* Reach 重疊增強功能。
* 已加入 "TestLogLevel" API 來啟用/停用/篩選 SDK 所發出的主控台記錄檔。
* 已修正活動中通知目標設定為應用程式啟動時未顯示的第一個活動。

## <a name="331-02182016"></a>3.3.1 (02/18/2016)
* 已修正 Web 通知的 HTML 內容與 SDK HTML 頁面之間的衝突。
* 穩定性改進。

## <a name="330-01222016"></a>3.3.0 (01/22/2016)
* 已修正在格式化以發行模式執行的 UWP 應用程式時當機的問題。
* 已修正 Universal 8.1 應用程式的通知中 1 像素邊界的問題。
* 動作 URL 現在可使用 ms-appx 及 ms-appdata 配置。
* 穩定性改進。

## <a name="320-11202015"></a>3.2.0 (11/20/2015)
* 新增對 Windows 10 通用 Windows 平台應用程式的支援。
* 新增推播通到共用功能，以修正通道衝突 (現已與 Azure 通知中樞相容)。
* 修正初始化之後立即要求裝置 ID 時發生的當機。
* 主控台記錄檔改善。
* 修正解析未處理的例外狀況時發生的當機。

## <a name="310-05212015"></a>3.1.0 (05/21/2015)
* Mobile Engagement 裝置識別碼現在是根據在安裝時產生的 GUID。

## <a name="301-04292015"></a>3.0.1 (2015/04//29)
* 已修復影響 SDK 在部分 Windows Phone WinRT 應用程式上的初始化錯誤。

## <a name="300-04032015"></a>3.0.0 (2015/04/03)
* 導入了跨平台 app (Windows 和 Windows Phone WinRT) 的 Mobile Engagement SDK。
* 預設通知圖示已更新。
* 按一下通知時，會傳回系統通知動作回饋。
* 已修復系統通知有時在點選後於應用程式內重播的狀況。

## <a name="200-02172015"></a>2.0.0 (2015/02/17)
* Azure Mobile Engagement 的最初發行版本
* 以連接字串組態取代 appId/sdkKey 組態。
* 增強安全性。

