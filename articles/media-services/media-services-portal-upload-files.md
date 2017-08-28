---
title: " 使用 Azure 入口網站將檔案上傳至媒體服務帳戶 | Microsoft Docs"
description: "本教學課程逐步引導您完成使用 Azure 入口網站將檔案上傳至媒體服務帳戶的步驟。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 8b9a7a266259077d49a38d51d7f801f420cc6315
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>使用 Azure 入口網站將檔案上傳至媒體服務帳戶
> [!div class="op_single_selector"]
> * [入口網站](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。 
> 


在媒體服務中，您會將數位檔案上傳到到資產。 「資產」可以包含視訊、音訊、影像、縮圖集合、文字播放軌及隱藏式輔助字幕檔案 (以及這些檔案的相關中繼資料)。上傳檔案之後，您的內容會安全地儲存在雲端，以進一步進行處理和串流處理。


## <a name="upload-files"></a>上傳檔案

>[!NOTE]
>在某些情況下，對於媒體服務中支援處理的檔案大小上限有所限制。 請參閱[此](media-services-quotas-and-limitations.md)主題，以取得有關檔案大小限制的詳細資料。
>

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2. 在 [設定] 刀鋒視窗上，按一下 [資產]。
   
    ![上傳檔案](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. 按一下 [上傳]  按鈕。
   
    [上傳視訊資產]  視窗隨即出現。
   
   > [!NOTE]
   > 檔案大小不受限。
   > 
   > 
4. 瀏覽至您的電腦上想要的視訊，加以選取，然後點擊 [確定]。  
   
    開始上傳，您可以在檔名底下看到進度。  

上傳完成後，您將會看到新資產列在 [資產]  清單中。 

## <a name="next-steps"></a>後續步驟
您現在可以將上傳的資產編碼。 如需詳細資訊，請參閱 [為資產編碼](media-services-portal-encode.md)。

您也可以使用 Azure Functions，以根據在所設定容器到達的檔案來觸發編碼作業。 如需詳細資訊，請參閱[此範例](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


