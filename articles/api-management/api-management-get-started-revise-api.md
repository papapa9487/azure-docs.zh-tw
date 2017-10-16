---
title: "使用修訂以更新 Azure API 管理中的 API | Microsoft Docs"
description: "依照本教學課程的步驟以了解如何使用 API 管理中的修訂進行不間斷的變更。"
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
ms.openlocfilehash: 0d67166a16ae4d640080ad83e7625e594b0dc246
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="make-non-breaking-changes-safely-using-revisions"></a>使用修訂安全地進行不間斷的變更
本教學課程說明如何安全地變更您的 API，並與您的開發人員溝通此變更。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要建立 API 管理服務，並擁有可修改的現有 API (取代會議 API)。

## <a name="about-revisions"></a>關於修訂
當您的 API 已準備就緒，而且開發人員也已準備好使用時，對該 API 的變更通常需要特別小心，以免 API 的呼叫者作業中斷。 這對於讓開發人員了解您所做的更改也相當有用。 我們可以使用**修訂**在 Azure API 管理中進行此動作。

## <a name="walkthrough"></a>逐步介紹
在本逐步介紹中，我們加入新的修訂、加入作業，接著使該修訂為最新狀態 - 在執行時，建立變更記錄項目。

## <a name="add-a-new-revision"></a>新增修訂
1. 瀏覽至 Azure 入口網站之 API 管理服務中的 [API] 頁面。
2. 從 API 清單選取 [會議 API]，然後從頁面頂端附近的功能表選取 [修訂] 索引標籤。
3. 選取 [+ 新增修訂]

    > [!TIP]
    > 您也可以在 API 上選擇操作功能表中的 [新增修訂] \(**...**)。
![畫面頂端附近的修訂功能表](media/api-management-getstarted-revise-api/TopMenu.PNG)

4. 提供新修訂的描述，以協助您記住其用途。
5. 選取 [建立] 
6. 現在已建立新的修訂。

    > [!NOTE]
    > 原始的 API 會保留在**修訂 1**。 這是您的使用者將繼續呼叫的修訂，直到您選擇進行其他修訂為止。

## <a name="make-non-breaking-changes-to-your-revision"></a>對您的修訂進行不間斷變更
1. 選取畫面頂端附近的 [設計] 索引標籤。
2. 請注意，**修訂選取器** (在 [設計] 索引標籤的正上方) 會顯示您目前的修訂為**修訂 2**。

    > [!TIP]
    > 使用修訂選取器在您要進行的修訂之間切換。

3. 選取 [+ 新增作業]。
4. 將您的新作業設為 **POST**，並將作業的「名稱與顯示名稱」設為**意見反應**
5. **儲存**您的新作業。
6. 現在，我們已對**修訂 2** 進行變更。 使用頁面頂端附近的 [修訂選取器]，即可切換回**修訂 1**。
7. 請注意，新的作業不會出現在**修訂 1**。 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>請讓您的修訂為最新版本，並新增變更記錄項目
1. 從頁面頂端附近的功能表選取 [修訂] 索引標籤。
![修訂畫面上的 [修訂] 功能表。](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
2. 針對**修訂 2** 開啟操作功能表 (**...**)。
3. 選取 [設為最新]。
![請讓您的修訂為最新版本，並張貼至變更記錄](media/api-management-getstarted-revise-api/MakeCurrent.PNG)
4. 選取 [發佈到此 API 的公用變更記錄檔]
5. 提供開發人員查看的變更說明，例如**「已新增意見反應作業。」**
6. **修訂 2** 目前為最新狀態。

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>瀏覽開發人員入口網站，以查看變更與變更記錄檔
1. 從頂端功能表選取 [開發人員入口網站]。
2. 選取 [API]，然後選取 [會議 API]。
3. 請注意，您現在可以使用新的**意見反應**作業。
4. 從 API 名稱下方選取 [API 變更記錄]。
5. 請注意，您的變更記錄項目會出現在此清單中。
![開發人員入口網站上的變更記錄檔](media/api-management-getstarted-revise-api/ChangeLogDevPortal.PNG)

## <a name="next-steps"></a>後續步驟
[使用 Azure API 管理發行 API 版本](#api-management-getstarted-publish-versions.md)