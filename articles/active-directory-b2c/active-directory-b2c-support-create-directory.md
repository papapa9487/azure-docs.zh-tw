---
title: "Azure Active Directory B2C：針對建立租用戶進行疑難排解 | Microsoft Docs"
description: "關於建立 Azure Active Directory 或 Azure Active Directory B2C 租用戶的問題與解決方法。"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 7ba4c6b2-161b-45b5-b3bd-ccb662f5d7a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 81af4536fc223319369aff262d42149cfbf1a1e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>針對建立 Azure Active Directory 或 Azure Active Directory B2C 租用戶進行疑難排解 

## <a name="create-an-azure-ad-tenant"></a>建立 Azure AD 租用戶
如果您第一次嘗試時無法建立 Azure Active Directory (Azure AD) 租用戶，請再試一次。 若問題持續發生，請連絡 Azure 支援中心。

## <a name="create-an-azure-ad-b2c-tenant"></a>建立 Azure AD B2C 租用戶
如果您在[建立 Azure Active Directory B2C (Azure AD B2C) 租用戶](active-directory-b2c-get-started.md)時遇到問題，請嘗試下列選項：

* 若您的租用戶清單未顯示 Azure AD B2C 租用戶，請再試一次建立租用戶。
* 如果 Azure AD B2C 租用戶未顯示在您的租用戶清單中，而且您看到下列錯誤訊息，請刪除租用戶，然後再一次建立：

    「無法完成建立 B2C 租用戶 'contosob2c'。 如需詳細指引，請造訪此[連結](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409)。」
* 當您刪除現有的 Azure AD B2C 租用戶並使用相同的網域名稱加以重建時，會發生已知的問題。 當您建立新的 Azure AD B2C 租用戶時，您必須使用不同的網域名稱。
* 若這些解決方法無效，請連絡 Azure 支援中心。 如需詳細資訊，請參閱[提出 Azure AD B2C 的支援要求](active-directory-b2c-support.md)。

