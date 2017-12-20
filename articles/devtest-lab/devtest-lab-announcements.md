---
title: "將公告張貼到 Azure DevTest Labs 中的實驗室 | Microsoft Docs"
description: "了解如何將公告張貼到 Azure DevTest Labs 中的實驗室"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: tarcher
ms.openlocfilehash: db92caa497b3db6b3e05e59e169111cedc3c71c6
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>將公告張貼到 Azure DevTest Labs 中的實驗室

身為實驗室系統管理員，您可以將自訂公告張貼在現有的實驗室中，通知使用者有關實驗室的近期變動或新增項目。 例如，您可以通知使用者有關：

- 可用的新 VM 大小
- 目前無法使用的映像
- 實驗室原則更新

張貼以後，公告會顯示在實驗室的 [概觀] 頁面上，而使用者可加以選取來取得詳細資訊。

公告功能主要用於臨時通知。  不再需要公告以後，您可以輕鬆地將它停用。

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>在現有實驗室中張貼公告的步驟

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 視需要選取 [所有服務]，然後從清單中選取 [DevTest Labs]。 (您的實驗室可能已顯示在 [所有資源] 下方的儀表板上)。
1. 從實驗室清單中，選取您想要在其中張貼公告的實驗室。  
1. 在實驗室的 [概觀] 區域中選取 [組態和原則]。  

    ![組態和原則按鈕](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. 在左側的 [設定] 之下，選取 [實驗室公告]。

    ![實驗室公告按鈕](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. 若要在這個實驗室中建立使用者訊息，請將 [啟用] 設定為 [是]。

1. 輸入 [公告標題] 和 [公告文字]。

   標題可多達 100 個字元，而且會在實驗室的 [概觀] 頁面上對使用者顯示。 如果使用者選取標題，就會顯示公告文字。

   公告文字接受 Markdown。 當您輸入公告文字時，您可以在畫面底部的 [預覽] 區域中檢視訊息。

    ![用於建立訊息的實驗室公告畫面。](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. 一旦準備好張貼公告，請選取 [儲存]。

當您不想要再對實驗室使用者顯示此公告時，請返回 [實驗室公告] 頁面，然後將 [啟用] 設定為 [否]。

## <a name="steps-for-users-to-view-an-announcement"></a>使用者檢視公告的步驟

1. 從 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)選取一個實驗室。

1. 如果實驗室有為其張貼的公告，則資訊通知會顯示在實驗室的 [概觀] 頁面頂端。 此資訊通知是在建立公告時所指定的公告標題。

    ![概觀頁面上的實驗室公告](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. 使用者可以選取訊息以檢視整個公告。

    ![實驗室公告的詳細資訊](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>後續步驟
* 如果您變更或設定實驗室原則，您可以張貼公告來通知使用者。 [設定原則和排程](devtest-lab-set-lab-policy.md)提供的資訊是關於如何使用自訂的原則，在訂用帳戶內套用限制和慣例。
* 瀏覽 [DevTest Labs Azure Resource Manager 快速入門範本資源庫 (英文)](https://github.com/Azure/azure-devtestlab/tree/master/Samples)。
