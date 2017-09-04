---
title: "疑難排解群組的動態成員資格 | Microsoft Docs"
description: "Azure AD 中群組動態成員資格的疑難排解秘訣。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 89bb04b6-a379-49c2-8465-fe386641816a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 8b4251c39a8883dfcb97ded8e4b533f073dce685
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>疑難排解群組的動態成員資格
**我針對某個群組設定了一個規則，但是群組中的成員資格沒有任何更新**<br/>請確認 [設定] 索引標籤中的 [啟用委派的群組管理] 設定是設為 [是]。只有當您以 Azure Active Directory Premium 授權指派對象的使用者身分登入時，才看得到這項設定。 請針對規則確認使用者屬性的值：是否有符合規則的使用者？

**我已設定一個規則，但是目前現有的規則成員已被移除**<br/>這是預期行為。 因為在啟用或變更規則時，現有群組的成員會遭到移除。 從評估規則所傳回的使用者會當作成員，新增至群組。     

**當我新增或變更規則時，沒有立即看到成員資格變更，為什麼？**<br/>專用的成員資格評估會定期在非同步的背景處理序中完成。 該程序所需的時間，取決於您目錄中的使用者數目以及因規則所建立的群組大小。 具有少量使用者的目錄通常會在很短的時間內看到群組成員資格變更。 具有大量使用者的目錄則可能需要 30 分鐘或更長的時間填入。

### <a name="next-steps"></a>後續步驟
這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [什麼是 Azure Active Directory？](active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

