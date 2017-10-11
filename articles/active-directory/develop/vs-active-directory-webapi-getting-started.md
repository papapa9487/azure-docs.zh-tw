---
title: "開始使用 Visual Studio WebApi 專案中的 Azure AD | Microsoft Docs"
description: "使用 Visual Studio 已連接服務連接 Azure AD 或建立 Azure AD 後，如何在 WebApi 專案中開始使用 Azure Active Directory"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: a756316054dd3bb63f3b0a9f59621bb1345bc693
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="get-started-with-azure-active-directory-and-visual-studio-connected-services-webapi-projects"></a>開始使用 Azure Active Directory 和 Visual Studio 已連接服務 (WebApi 專案)
> [!div class="op_single_selector"]
> * [開始使用](vs-active-directory-webapi-getting-started.md)
> * [發生什麼情形](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="requiring-authentication-to-access-controllers"></a>存取控制器之前需要驗證
專案中的所有控制器都加上 **Authorize** 屬性做裝飾。 此屬性要求使用者必須經過驗證，才能存取這些控制器所定義的 API。 若要允許以匿名方式存取控制器，請從控制器中移除此屬性。 如果您要以更精確地設定權限，請將此屬性套用至每一個需要授權的方法，而非套用至控制器類別。

## <a name="next-steps"></a>後續步驟
[深入了解 Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

