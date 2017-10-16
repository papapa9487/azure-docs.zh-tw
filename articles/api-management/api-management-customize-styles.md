---
title: "在 Azure API 管理中自訂開發人員入口網站的樣式 | Microsoft Docs"
description: "了解如何在 Azure API 管理中修改開發人員入口網站中任何頁面所使用的樣式。"
services: api-management
documentationcenter: 
author: vladvino
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
ms.openlocfilehash: 89baf60d0204a1701e93309f09b90bc94c4ca57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>在 Azure API 管理中自訂開發人員入口網站的樣式
在 Azure API 管理中自訂開發人員入口網站的基本方式有三種：

* [編輯靜態頁面和頁面配置元素的內容][modify-content-layout]
* [更新用於開發人員入口網站上頁面元素的樣式][customize-styles] (如本指南所述)
* [修改入口網站所產生的網頁使用的範本][portal-templates] (例如 API 文件、產品、使用者驗證等)

## <a name="change-headers-styling"> </a>變更頁面元素的樣式

入口網站上任何頁面的色彩、字型、大小、空格和其他樣式相關的元素，都會透過樣式規則加以定義。 

當您以系統管理員身分登入時，可從 [開發人員入口網站] 完成編輯樣式規則。 若要到達該處，請開啟 Azure 入口網站，然後從 API 管理執行個體的服務工具列按一下 [發行者入口網站]。

![發行者入口網站][api-management-management-console]

接著按一下右上角的 [開發人員入口網站]。 

![發行者入口網站上的開發人員入口網站連結][api-management-pp-dp-link]

若要開啟自訂工具列，請將滑鼠移動到自訂圖示上方 (或選取它)，然後按一下工具列上的 [樣式]。

![自訂工具列][api-management-customization-toolbar-button]

編輯樣式規則的主要方式有兩種 - 您可以查看任何地方使用的所有樣式規則清單 (預設會顯示) 並視需要修改樣式，也可以選擇 [選取頁面上的元素]，然後按一下頁面上的任意處，以查看該元素的樣式。

![Customization toolbar][api-management-customization-toolbar]

針對此範例按一下 [選取頁面上的元素] 選項。  現在，當您將滑鼠移至元素上方時，元素會變成反白顯示狀態，以象徵您按一下就會開始編輯的元素樣式。 將滑鼠移到標頭中的文字上方 (此處通常會有公司名稱)，然後按一下它。 樣式編輯器中會出現一組已具名且分類的樣式規則。 每個規則均代表所選取元素的樣式屬性。 例如，針對以上選取的頁首文字，文字的大小是 @font-size-h1，而替代字型的名稱是 @headings-font-family。

> 如果您熟悉 [bootstrap][bootstrap]，事實上，這些規則就是開發人員入口網站所使用的 bootstrap 佈景主題內的 [LESS 變數][LESS variables]。
> 
> 

讓我們變更頁首文字的色彩。 選取 **@headings-color** 欄位中的項目，然後輸入 **#000000**。 這是黑色的十六進位碼。 執行此動作時，您會看見文字方塊末端出現方形色彩指示器。 如果您按一下此指示器，即會出現色彩選擇器，讓您能夠選擇色彩。

![Color picker][api-management-customization-toolbar-color-picker]

您可以在進行變更時即時預覽變更，但只有系統管理員可以看到。 若要讓每個人都能看見這些變更，可在樣式編輯器中按一下 [發佈]  按鈕並確認變更。

![Publish menu][api-management-customization-toolbar-publish-form]

> 若要變更套用至頁面上其他元素的樣式規則，請遵循您用以變更標頭的相同程序。 從樣式編輯器中按一下 [選取頁面上的元素]、選取您感興趣的元素，然後開始修改畫面上顯示的樣式規則值。
> 
> 


## <a name="next-steps"> </a>後續步驟
* 了解如何使用 [開發人員入口網站範本](api-management-developer-portal-templates.md)，自訂開發人員入口網站頁面內容。

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/
