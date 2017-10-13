---
title: "Microsoft Azure StorSimple Data Manager 概觀 | Microsoft Docs"
description: "提供 StorSimple Data Manager 服務的概觀 (私人預覽)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: aedb44610fe57055851538b9dbdb810e66e58d73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-data-manager-overview-private-preview"></a>StorSimple Data Manager 概觀 (私人預覽)

## <a name="overview"></a>概觀

Microsoft Azure StorSimple 是一個混合式雲端儲存體解決方案，可解決通常與檔案共用關聯之非結構化資料的複雜性問題。 StorSimple 使用雲端儲存體做為內部部署解決方案的擴充功能，並且跨內部部署儲存體和雲端儲存體自動將資料分層。 整合式資料保護 (使用本機和雲端快照集) 不需要擴張的儲存體結構。 保存和災害復原也密切結合，將雲端當做離站位置。

本文介紹的資料轉換服務可讓您順暢地存取雲端中的 StorSimple 資料。 此服務提供 API，可從 StorSimple 擷取資料，並以可輕易取用的資料格式提供給其他 Azure 服務。 此預覽版本支援的格式是 Azure blob 和 Azure 媒體服務資產。 此轉換可讓您輕鬆地接到服務，例如 Azure 媒體服務、Azure HDInsight、Azure Machine Learning 和 Azure 搜尋服務，以處理 StorSimple 8000 系列內部部署裝置上的資料。

相關的高階區塊圖如下所示。

![高階圖表](./media//storsimple-data-manager-overview/high-level-diagram.png)

本文說明如何註冊此服務的私人預覽。 也說明如何使用這項服務來撰寫應用程式，以使用 StorSimple 資料和雲端中的其他 Azure 服務。

## <a name="sign-up-for-data-manager-preview"></a>註冊資料管理員預覽
註冊資料管理員服務之前，請檢閱下列必要條件。

### <a name="prerequisites"></a>必要條件

本練習假設您有
* 有效的 Azure 訂用帳戶。
* 可存取已註冊的 StorSimple 8000 系列裝置
* 所有與 StorSimple 8000 系列裝置相關聯的金鑰。

### <a name="sign-up"></a>註冊

StorSimple Data Manager 處於私人預覽階段。 執行下列步驟來註冊此服務的私人預覽︰

1.  透過 StorSimple Data Manager 擴充登入 Azure 入口網站：[https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)。 輸入您的 Azure 認證來登入。

2.  按一下 **+** 圖示建立服務。 按一下 [儲存體]，然後在開啟的刀鋒視窗中，按一下 [查看全部]。

    ![搜尋 StorSimple Data Manager 圖示](./media/storsimple-data-manager-overview/search-data-manager-icon.png)

3. 您會看到 StorSimple Data Manager 圖示。

    ![選取 StorSimple Data Manager 圖示](./media/storsimple-data-manager-overview/select-data-manager-icon.png)

4. 按一下 StorSimple Data Manager 圖示，然後按一下建立。 挑選您要對此私人預覽啟用的訂用帳戶，然後按一下我要註冊!

    ![我要註冊](./media/storsimple-data-manager-overview/sign-me-up.png)

5. 這會傳送要求請您加入。 我們會儘速讓您加入。 在您的訂用帳戶啟用後，您就可以建立 StorSimple Data Manager 服務。

6. 為了輕鬆存取 StorSimple Data Manager 服務，請按一下星號圖示，將它釘選至我的最愛。

    ![存取 StorSimple Data Manager](./media/storsimple-data-manager-overview/access-data-managers.png)


## <a name="next-steps"></a>後續步驟

[使用 StorSimple Data Manager UI 來轉換資料](storsimple-data-manager-ui.md)。