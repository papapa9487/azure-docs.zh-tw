---
title: "整合 Azure 儲存體帳戶與 Azure CDN | Microsoft Docs"
description: "了解如何使用 Azure 內容傳遞網路 (CDN)，透過快取 Azure 儲存體中的 Blob 來傳遞高頻寬內容。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 511076935d06ed0908341044e37069e74530be49
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>整合 Azure 儲存體帳戶與 Azure CDN
可以啟用 CDN，以從您的 Azure 儲存體快取內容。 它透過將計算執行個體 Blob 與靜態內容快取到位於美國、歐洲、亞洲、澳洲與南美洲的實體節點中，為開發人員提供一套傳遞高頻寬內容的全球解決方案。

## <a name="step-1-create-a-storage-account"></a>步驟 1：建立儲存體帳戶
使用下列程序，為 Azure 訂用帳戶建立新的儲存體帳戶。 有了儲存體帳戶，才能存取 Azure 儲存體服務。 儲存體帳戶代表最高層級的命名空間，用於存取每個 Azure 儲存體服務元件：Blob 服務、佇列服務和資料表服務。 如需詳細資訊，請參閱 [Microsoft Azure 儲存體簡介](../storage/common/storage-introduction.md)。

若要建立儲存體帳戶，您必須是服務管理員或是相關聯訂用帳戶的共同管理員。

> [!NOTE]
> 您可使用數種方法來建立儲存體帳戶，包括 Azure 入口網站和 Powershell。  針對本教學課程，我們將使用 Azure 入口網站。  
> 
> 

**為 Azure 訂用帳戶建立儲存體帳戶**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左下角，選取 [ **新增**]。 在 [新增] 對話方塊中，選取 [資料 + 儲存體]，然後按一下 [儲存體帳戶]。
    
    此時會顯示 [建立儲存體帳戶]  刀鋒視窗。   

    ![建立儲存體帳戶][create-new-storage-account]  

3. 在 [名稱]  欄位中，輸入子網域名稱。 此項目可以包含 3 至 24 個小寫字母與數字。
   
    此值會成為 URI 內用來將訂用帳戶的 Blob、「佇列」或「資料表」資源定址的主機名稱。 若要將 Blob 服務中的容器資源定址，您需要使用下列格式的 URI，其中 &lt;StorageAccountLabel&gt; 是指在 [輸入 URL] 中輸入的值：
   
    http://&lt;StorageAcountLabel&gt;.blob.core.windows.net/&lt;mycontainer&gt;
   
    **重要事項：** URL 標籤會形成儲存體帳戶 URI 的子網域，而且必須在 Azure 中的所有代管服務間是唯一的。
   
    此值也用作這個儲存體帳戶在入口網站中的名稱，或用於透過程式設計方式存取此帳戶時。
4. 保留 [部署模型]、[帳戶種類]、[效能] 和 [複寫] 的預設值。 
5. 選取將與儲存體帳戶搭配使用的 [ **訂用帳戶** ]。
6. 選取或建立 **資源群組**。  如需資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md#resource-groups)。
7. 選取儲存體帳戶的位置。
8. 按一下 [建立] 。 建立儲存體帳戶的程序可能需要幾分鐘才能完成。

## <a name="step-2-enable-cdn-for-the-storage-account"></a>步驟 2︰啟用儲存體帳戶的 CDN

您現在可以利用最新的整合啟用儲存體帳戶的 CDN，而不需要離開儲存體入口網站延伸模組。 

1. 選取儲存體帳戶，搜尋 "CDN" 或從左側導覽功能表向下捲動，然後按一下 [Azure CDN]。
    
    [Azure CDN] 刀鋒視窗隨即顯示。

    ![CDN 啟用導覽][cdn-enable-navigation]
    
2. 輸入所需的資訊建立新端點
    - **CDN 設定檔**：您可以建立新的設定檔，或使用現有設定檔。
    - **定價層**︰如果您建立新的 CDN 設定檔，則只需選取定價層。
    - **CDN 端點名稱**︰依照您的選擇輸入端點名稱。

    > [!TIP]
    > 根據預設，建立的 CDN 端點會使用儲存體帳戶的主機名稱作為來源。

    ![cdn new endpoint creation][cdn-new-endpoint-creation]

3. 建立之後，新的端點會出現在上面的端點清單。

    ![CDN 儲存體新端點][cdn-storage-new-endpoint]

> [!NOTE]
> 您也可以移至 Azure CDN 延伸模組以啟用 CDN，[教學課程](#Tutorial-cdn-create-profile)。
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]  

## <a name="step-3-enable-additional-cdn-features"></a>步驟 3︰ 啟用其他 CDN 功能

從儲存體帳戶 [Azure CDN] 刀鋒視窗中，按一下清單中的 CDN 端點，以開啟 CDN 組態刀鋒視窗。 您可以為傳遞啟用其他的 CDN 功能，例如壓縮、查詢字串、地理篩選。 您也可以將自訂網域對應新增至 CDN 端點，並啟用自訂網域 HTTPS。
    
![CDN 儲存體 CDN 組態][cdn-storage-cdn-configuration]

## <a name="step-4-access-cdn-content"></a>步驟 4：存取 CDN 內容
若要存取 CDN 上快取的內容，請使用入口網站中提供的 CDN URL。 所快取 Blob 的位址將類似如下：

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> 啟用 CDN 存取儲存體帳戶後，所有公開可用的物件皆適用於 CDN 邊緣快取。 如果您修改的物件目前是 CDN 中的快取物件，在快取內容的有效存留期已滿，且 CDN 重新整理內容之前，都無法透過 CDN 取得新的內容。
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>步驟 5：從 CDN 移除內容
如果不想要將內容快取到 Azure 內容傳遞網路 (CDN) 中，您可以採取下列其中一個步驟：

* 您可以將容器設為私人而非公用。 如需詳細資訊，請參閱 [管理對容器和 Blob 的匿名讀取權限](../storage/blobs/storage-manage-access-to-resources.md) 。
* 您可以使用管理入口網站來停用或刪除 CDN 端點。
* 您可以修改託管服務，使其不再回應物件的要求。

已在 CDN 中快取的物件會保持快取狀態，直到物件的有效存留期已過或端點已清除為止。 有效存留期間已滿時，CDN 將查看 CDN 端點是否仍然有效，以及物件是否仍可匿名存取。 如果不是的話，將不再快取物件。

## <a name="additional-resources"></a>其他資源
* [如何將 CDN 內容對應至自訂網域](cdn-map-content-to-custom-domain.md)
* [啟用自訂網域的 HTTPS](cdn-custom-ssl.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png
[cdn-enable-navigation]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png
[cdn-storage-new-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png
[cdn-storage-cdn-configuration]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png 

