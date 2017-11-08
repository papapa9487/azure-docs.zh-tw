---
title: "使用 Azure AD 存取權檢閱來檢閱存取權 | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 存取權檢閱來檢閱存取權。"
services: active-directory
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 9cd11d41c68c29bfcba44673ae6dbd154fc463f6
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2017
---
# <a name="review-access-with-azure-ad-access-reviews"></a>使用 Azure AD 存取權檢閱來檢閱存取權

Azure Active Directory (Azure AD) 使用稱為存取權檢閱的功能，簡化了企業在 Azure AD 和其他 Microsoft Online Services 中管理應用程式存取權及群組成員的方式。 您可能已收到 Microsoft 的電子郵件，要求您檢閱群組成員或可存取應用程式之使用者的存取權。 

## <a name="open-an-access-review"></a>開啟存取權檢閱

如果要查看待處理的存取權檢閱，請選取電子郵件中的連結。 如果未收到電子郵件，您可以執行下列步驟來尋找存取權檢閱：

1. 登入 [Azure AD 存取面板](https://myapps.microsoft.com)。

2. 選取頁面右上角的使用者符號，其顯示您的名稱和預設組織。 如果列出多個組織，請選取已要求存取權檢閱的組織。

3. 若頁面右側有標示為「存取權檢閱」的磚，請選取它。 如果看不到磚，則沒有針對該組織執行的存取權檢閱，此時也不需要採取任何動作。

## <a name="fill-out-an-access-review"></a>填寫存取權檢閱

從清單中選取存取權檢閱，您會看到需檢閱之使用者的名稱。 如果要求是檢閱您自己的存取權，您可能只會看到一個名稱，即您自己的名稱。

對於清單上的每個資料列，您可以決定要核准或拒絕使用者的存取權。 選取資料列，然後選擇要核准或拒絕。 (如果您不認識使用者，也可以表明)。

檢閱者可能會要求您提供理由來核准持續存取權或群組成員資格。

## <a name="next-steps"></a>後續步驟

拒絕的使用者存取權不會立即移除。 它可以在檢閱完成時或系統管理員停止檢閱時移除。 如果您想要變更您的答案及核准先前拒絕的使用者，或拒絕先前核准的使用者，請選取資料列、重設回應並選取新的回應。 您可以執行這個步驟，直到存取權檢閱完成為止。



