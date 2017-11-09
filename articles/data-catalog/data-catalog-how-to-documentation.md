---
title: "如何記載資料來源 | Microsoft Docs"
description: "專門說明如何在 Azure 資料目錄中記載資料資產的操作說明文章。"
services: data-catalog
documentationcenter: 
author: spelluru
manager: NA
editor: 
tags: 
ms.assetid: 053b1701-b848-4ada-b726-6f485caa9961
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: spelluru
ms.openlocfilehash: ea17dc0914966b9c3ca611c3d359635216ecf1ca
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2017
---
# <a name="document-data-sources"></a>記載資料來源
## <a name="introduction"></a>簡介
 是全面管理的雲端服務，可作為企業資料來源的註冊系統和探索系統。 換句話說，**Azure 資料目錄**的重點在於協助人們探索、了解，以及使用資料來源，並可協助組織從現有的資料獲得更多價值。

當資料來源向 **Azure 資料目錄**註冊之後，該服務會複製其中繼資料並建立索引，但不僅止於此。 **Azure 資料目錄** 也可讓使用者提供自己的完整說明文件來描述資料來源的使用方式和常見案例。

在 [如何註解資料來源](data-catalog-how-to-annotate.md)中，您已了解知道資料來源的專家可以使用標記和描述來為資料來源加上註解。 **Azure 資料目錄** 入口網站含有 RTF 編輯器，因此使用者可以完整記載資料資產和容器。 該編輯器包括段落格式化，例如標題、文字格式化、項目符號清單、編號清單和資料表。

標記和描述非常適合用於簡單的註解。 不過，為了協助資料取用者深入了解資料來源的使用方式和資料來源的商務案例，專家可以提供完整而詳細的說明文件。 想要記載資料來源很容易。 請選取資料資產或容器，然後選擇 [說明文件] 。

![](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>記載資料資產
**Azure 資料目錄** 說明文件的優點可讓您使用資料目錄做為內容儲存機制，以建立完整的資料資產敘述。 您可以探索說明容器和資料表的詳細內容。 如果您在其他內容儲存機制 (例如 SharePoint 或檔案共用) 已有內容，您可以新增資產說明文件連結來參考此現有內容。 此功能可讓您更容易找到現有的說明文件。

> [!NOTE]
> 說明文件不會包含在搜尋索引中。
>
>

![](media/data-catalog-documentation/data-catalog-documentation2.png)

說明文件層級的範圍可從描述資料資產容器的特性和值，到詳細描述容器內的資料表結構描述。 所提供的說明文件層級應以商務需求為準。 但一般來說，記載資料資產的優缺點如下︰

* 只記載容器︰所有內容集中放置，但可能缺少可供使用者做出明智決策的必要詳細資料。
* 只記載資料表︰內容專用於該物件，但使用者會將文件放在多個地方。
* 記載容器和資料表︰最全面的方法，但可能需要花更多時間維護文件。

## <a name="summary"></a>摘要
在 **Azure 資料目錄** 中記載資料來源可依所需詳細程度建立資料資產的相關敘述。  藉由使用連結，您可以連結至現有內容儲存機制中儲存的內容，以結合您現有的文件和資料資產。 一旦使用者找到合適的資料資產，就能取得一組完整的說明文件。
