---
title: "疑難排解：'Active Directory' 項目遺失或無法使用 | Microsoft Docs"
description: "若 Active Directory 功能表項目未出現在 Azure 管理入口網站中，該怎麼做。"
services: active-directory
documentationcenter: na
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.openlocfilehash: be3a797c4a405fd2f6636e67f4c961dd6d143486
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>疑難排解：'Active Directory' 項目遺失或無法使用
許多使用 Azure Active Directory 功能和服務的相關指示都是以「前往 Azure 管理入口網站並按一下 **Active Directory**」為開頭。 但是，如果 Active Directory 擴充功能或功能表項目未出現，或者標示為 [無法使用]，該怎麼辦？ 本主題旨在提供協助。 它將說明 **Active Directory** 未出現或無法使用的情況，並說明如何繼續執行。

## <a name="active-directory-is-missing"></a>Active Directory 遺失
一般而言， **Active Directory** 項目會出現在左側導覽功能表中。 Azure Active Directory 程序中的指示假設此項目是在您的檢視中。

![螢幕擷取畫面：Azure 中的 Active Directory](./media/active-directory-troubleshooting/typical-view.png)

當下列任一條件成立時，Active Directory 項目就會出現在左側導覽功能表中。 否則，該項目便不會出現。

* 目前的使用者是使用 Microsoft 帳戶 (之前稱為 Windows Live ID) 登入。
  
    或
* Azure 租用戶都擁有一個目錄，且目前的帳戶為目錄系統管理員。
  
    或
* Azure 租用戶至少具有一個 Azure AD 存取控制 (ACS) 命名空間。 如需詳細資訊，請參閱 [存取控制命名空間](https://msdn.microsoft.com/library/azure/gg185908.aspx)。
  
    或
* Azure 租用戶至少具有一個 Azure Multi-Factor Authentication 提供者。 如需詳細資訊，請參閱 [管理 Azure Multi-Factor Authentication 提供者](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)。

若要建立存取控制命名空間或 Multi-Factor Authentication 提供者，請按一下 [+新增]  >  [應用程式服務]  >  [Active Directory]。

若要取得目錄的系統管理權限，必須要求系統管理員將系統管理員角色指派給您的帳戶。 如需詳細資訊，請參閱 [指派系統管理員角色](active-directory-assign-admin-roles.md)。

## <a name="active-directory-is-not-available"></a>Active Directory 無法使用
當您按一下 [+新增]  >  [應用程式服務] 時，[Active Directory] 項目即會出現。 具體而言，當任何 Active Directory 功能 (例如，目錄、存取控制或 Multi-Factor Auth 提供者) 可供目前使用者使用時，Active Directory 項目即會出現。

不過，當網頁正在載入時，該項目會呈暗灰色且標示為 [無法使用] 。 這是暫時性狀態。 如果您稍待片刻，該項目將變成可用狀態。 如果延遲太久，重新整理網頁通常就能解決問題。

![螢幕擷取畫面：Active Directory 無法使用](./media/active-directory-troubleshooting/not-available.png)

