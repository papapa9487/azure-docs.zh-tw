---
title: "發佈 Azure Stack (雲端操作員) 中的自訂 Marketplace 項目 | Microsoft Docs"
description: "身為 Azure Stack 操作員，應了解如何發佈 Azure Stack 中的自訂 Marketplace 項目。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 7b5f976eb2d51eb86761a2bd0be6adb45ca87681
ms.contentlocale: zh-tw
ms.lasthandoff: 09/27/2017

---
# <a name="the-azure-stack-marketplace-overview"></a>Azure Stack Marketplace 概觀

「適用於：Azure Stack 整合系統和 Azure Stack 開發套件」

Marketplace 是一組針對 Azure Stack 自訂的服務、應用程式與資源集合，像是網路、虛擬機器、儲存體等等。 這是使用者用以建立新資源及部署新應用程式的位置。 不妨將其視為購物目錄，使用者可以在此瀏覽並選擇他們想要使用的項目。 若要使用 Marketplace 項目，使用者必須訂閱會授與他們項目存取權的產品。

身為 Azure Stack 操作員，可以決定要新增 (發佈) 到 Marketplace 中的項目。 您可以發佈像資料庫、應用程式服務等等的項目。 這會讓所有的使用者都可以看到它們。 您可以發佈您所建立的自訂項目。 您也可以從成長中的 [Azure Marketplace 項目清單](azure-stack-marketplace-azure-items.md)內發佈項目。 當您將項目發佈到 Marketplace 中，使用者在五分鐘內就可看到它。

若要開啟 Marketplace，請按一下 [新增] 。

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace 項目
Azure Stack Marketplace 項目是您的使用者可下載並使用的服務、應用程式與資源。 您所有的使用者都可以看到所有 Azure Stack Marketplace 項目。

每個 Marketplace 項目都有：

* 供資源佈建使用的 Azure 資源管理員範本
* 中繼資料，像是字串、圖示與其他行銷關聯資料
* 顯示入口網站中項目的格式化資訊

發佈至 Marketplace 的每個項目，都會使用稱為 Azure 資源庫封裝 (azpkg) 的格式。 請分別將部署或執行階段資源 (如代碼、含軟體的 Zip 檔或虛擬機器映像) 新增到 Azure Stack 中，而不是當作 Marketplace 項目的一部分。 

## <a name="next-steps"></a>後續步驟
[建立及發佈 Marketplace 項目](azure-stack-create-and-publish-marketplace-item.md)


