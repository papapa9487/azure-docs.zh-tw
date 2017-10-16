---
title: "在 Azure API 管理中修改開發人員入口網站的頁面內容 | Microsoft Docs"
description: "了解如何在 Azure API 管理中編輯開發人員入口網站的頁面內容。"
services: api-management
documentationcenter: 
author: antonba
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>在 Azure API 管理中修改開發人員入口網站的頁面內容和配置
在 Azure API 管理中自訂開發人員入口網站的基本方式有三種：

* [編輯靜態頁面和頁面配置元素的內容][modify-content-layout] (如本指南所述)
* [更新用於開發人員入口網站上頁面元素的樣式][customize-styles]
* [修改入口網站所產生的網頁使用的範本][portal-templates] (例如 API 文件、產品、使用者驗證等)

## <a name="page-structure"> </a>開發人員入口網站頁面的結構

開發人員入口網站是以內容管理系統為基礎。 每個頁面的配置都是根據一組小型網頁元素 (也稱為 Widget) 來建置：

![開發人員入口網站頁面結構][api-management-customization-widget-structure]

所有 Widget 都是可編輯的。 
* 每個個別頁面特定的核心內容是位於 [內容] Widget 中。 編輯網頁，表示編輯此 Widget 的內容。
* 所有頁面配置元素均隨附於其餘的 Widget 中。 對這些 Widget 所做的變更將套用至所有頁面。 它們將稱為「配置 Widget」。

在日常頁面中，編輯某一個頁面通常只會修改內容 Widget，它將針對每個個別頁面擁有不同的內容。

## <a name="modify-layout-widget"> </a>修改配置 Widget 的內容

開發人員入口網站內的內容是透過經由 Azure 入口網站存取的發佈者入口網站進行修改。 若要觸達它，請從 API 管理執行個體的服務工具列按一下 [發佈者入口網站]。

![發行者入口網站][api-management-management-console]

若要編輯該 Widget 的內容，請從左側的 [開發人員入口網站] 功能表中按一下 [Widget]。 讓我們針對此範例來修改頁首 Widget 的內容。 從清單中選取 [頁首] Widget。

![Widgets header][api-management-widgets-header]

頁首的內容可透過 [本文]  欄位加以編輯。 視需要變更文字，然後按一下頁面底部的 [儲存]。

現在，您應該會在開發人員入口網站內的每個頁面上看到新的頁首。

> 進入發行者入口網站後，若要開啟開發人員入口網站，請按一下頂端列的 [開發人員入口網站]  。
> 
> 

## <a name="edit-page-contents"> </a>編輯頁面的內容

若要查看所有現有內容頁面的清單，請在發行者入口網站的 [開發人員入口網站] 功能表中按一下 [內容]。

![Manage content][api-management-customization-manage-content]

按一下 [歡迎使用]  頁面，編輯要在開發人員入口網站首頁上顯示的內容。 進行所需的變更、必要時請進行預覽，然後按一下立即發佈  ，讓每個人都可看見這些變更。

> 首頁會使用特殊的版面配置，使其得以在上方顯示橫幅。 您無法透過 [內容]  區段編輯此橫幅。 若要編輯此橫幅，請從 [開發人員入口網站] 功能表按一下 [Widget]，然後從 [目前層級] 下拉式清單中選取 [首頁]，然後開啟 [精選] 區段下的 [橫幅] 項目。 如同其他任何頁面一般，此 Widget 的內容可供編輯。
> 
> 

## <a name="next-steps"> </a>後續步驟
* [更新用於開發人員入口網站上頁面元素的樣式][customize-styles]
* [修改入口網站所產生的網頁使用的範本][portal-templates] (例如 API 文件、產品、使用者驗證等)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
