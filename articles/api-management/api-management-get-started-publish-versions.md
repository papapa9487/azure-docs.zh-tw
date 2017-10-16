---
title: "使用 Azure API 管理發行 API 版本 |Microsoft Docs"
description: "依照此教學課程的步驟，了解如何在 API 管理中發行多個版本。"
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 7c355e2feb5ebe5971d8391b326422a1abec1497
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="publish-multiple-versions-of-your-api-in-a-predictable-way"></a>利用可預測的方式發行多個版本的 API
本教學課程說明如何設定您的 API 版本，然後選擇 API 開發人員呼叫的方式。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要建立 API 管理服務，並擁有可修改的現有 API (取代會議 API)。

## <a name="about-versions"></a>關於版本
有時候，讓 API 的所有呼叫者使用完全相同的版本不太實際。 有時您想要發佈新的或不同 API 功能給某些使用者，而其他人想要堅持使用目前適用的 API。 當呼叫者想要升級至更新版本時，他們希望能夠使用簡單易懂的方法來執行此操作。  我們可以在 Azure API 管理中使用**修訂**進行此動作。

## <a name="walkthrough"></a>逐步介紹
在本逐步介紹中，我們會將新版本加入現有的 API，選擇版本配置和版本識別碼。

## <a name="add-a-new-version"></a>加入新版本
![API 內容功能表 - 新增版本](media/api-management-getstarted-publish-versions/AddVersionMenu.png)
1. 瀏覽至 Azure 入口網站之 API 管理服務中的 [API] 頁面。
2. 從 API 清單選取 [會議 API]，然後選取旁邊的操作功能表 (**...**)。
3. 選取 [+ 新增版本]。

    > [!TIP]
    > 當您第一次建立新的 API 時，也會啟用版本 - 請在 [新增 API] 畫面中選取 [要為此 API 設定版本嗎？]。

## <a name="choose-a-versioning-scheme"></a>選擇版本設定配置
Azure API 管理可讓您選擇讓呼叫者指定他們想要之 API 版本的方式。 您可以選擇 [版本設定配置] 以執行此操作。 這個配置可以是**路徑、標頭或查詢字串**。 在我們的範例中，我們使用路徑。
![新增版本畫面](media/api-management-getstarted-publish-versions/AddVersion.PNG)
1. 讓選取的**路徑**做為您的**版本設定配置**。
2. 新增 **v1** 做為您的**版本識別碼**。

    > [!TIP]
    > 如果您選取 [標題] 或 [查詢字串] 做為版本設定配置，您需要提供其他值 - 標題名稱或查詢字串參數。

3. 視需要提供說明。
4. 選取 [建立] 以設定您的新版本。
5. 在 API 清單中的 [大型會議 API] 下，您可以看見兩個不同的 API - **原始**和 **v1**。
![在 Azure 入口網站中 API 下列出的版本](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > 如果您新增版本至未設定版本的 API，我們一律會為您建立**原始** - 在預設 URL 上回應。 這可確保任何現有的呼叫者不會因為新增版本的程序而中斷。 如果您在開始時建立已啟用版本的新 API，則不會建立「原始」。

6. 您現在可以編輯 **v1**，並將其設定為與**原始**完全不同的 API。 對某個版本進行變更不會影響另一個版本。

## <a name="add-the-version-to-a-product"></a>將版本新增至產品
若要讓呼叫者查看您的新版本，必須將新版本加入**產品** (產品不是從父版本繼承)。

1. 從服務管理頁面選取 [產品]。
2. 選取 [無限制]。
3. 選取 [API]。
4. 選取 [新增] 。
5. 選取 [會議 API，版本 v1]。
6. 返回服務管理頁面，然後選取 [API]。

## <a name="browse-the-developer-portal-to-see-the-version"></a>瀏覽開發人員入口網站以查看版本
1. 從頂端功能表選取 [開發人員入口網站]。
2. 選取 [API]，請注意，[會議 API] 會顯示**原始**和 **v1** 版本。
3. 選取 [v1]。
4. 請注意清單中第一項作業的 [要求 URL]。 它會顯示 API URL 路徑，包含 **v1**。
![開發人員入口網站上顯示的版本](media/api-management-getstarted-publish-versions/VersionDevPortal.PNG)
