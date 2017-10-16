---
title: "如何使用 Azure AD 存取權檢閱來檢閱存取權 | Microsoft Docs"
description: "如何使用 Azure AD 存取權檢閱來檢閱存取權。"
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
ms.openlocfilehash: c9115bb5bc3d22c67ea0ac77b8972ae7f919926b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-review-access-with-azure-ad-access-reviews"></a>如何使用 Azure AD 存取權檢閱來檢閱存取權

Azure Active Directory 使用稱為存取權檢閱的方式，簡化了企業在 Azure AD 和其他 Microsoft Online Services 中管理應用程式及群組成員的存取權。  您可能已收到 Microsoft 的電子郵件，要求您檢閱群組成員或能存取應用程式之使用者的存取權。 

## <a name="opening-an-access-review"></a>開啟存取權檢閱

您可以按一下電子郵件中的連結，即可查看擱置中的存取權檢閱。  如果您沒有電子郵件，則您可以執行下列動作來尋找存取權檢閱：

1. 登入到 [Azure AD 存取面板](https://myapps.microsoft.com)。
2. 按一下頁面右上角的使用者圖示，其顯示您的名稱和預設組織。 如果列出多個組織，選取已要求存取權檢閱的組織。
3. 若頁面右側有標示為**存取權檢閱**的磚，請按一下它。 否則，如果看不到磚，則沒有針對該組織執行的存取權檢閱，此時也不需要採取任何動作。

## <a name="filling-out-an-access-review"></a>填寫存取權檢閱

從清單中選取存取權檢閱，您會看到需要檢閱的使用者名稱。  如果要求檢閱您自己的存取權，則您可能只會看到一個名稱，即您自己的名稱。

對於清單上的每個資料列，您可以決定要核准或拒絕使用者的存取權。 您可以按一下資料列，然後選擇要核准或拒絕。 (如果您不認識使用者，您也可以回應。)

檢閱者可能會要求您提供理由來核准持續存取或群組成員資格。

## <a name="next-steps"></a>後續步驟

請注意，不會立即移除使用者的拒絕存取，可在完成檢閱或系統管理員停止檢閱時移除它。 因此，如果您想要變更您的答案及核准先前已拒絕的使用者，或拒絕先前已核准的使用者，請按一下資料列，重設回應並挑選新的回應。  您可以執行這項操作，直到存取權檢閱完成為止。



