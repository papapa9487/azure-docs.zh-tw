---
title: "Azure Active Directory B2C：自訂屬性 | Microsoft Docs"
description: "如何在 Azure Active Directory B2C 中使用自訂屬性收集取用者相關資訊"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 055ffb0a-197b-4716-8dad-1fd8a01e174f
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 356aaeff3a78fc7b682d621e8e0de9312582b2fe
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active Directory B2C：使用自訂屬性收集您的取用者的相關資訊
您的 Azure Active Directory (Azure AD) B2C 目錄隨附一組內建資訊 (屬性)：名字、姓氏、城市、郵遞區號及其他屬性。 不過，每個取用者面向應用程式對於向取用者收集的屬性，皆有獨特需求。 Azure AD B2C 可讓您擴充目錄儲存在每個取用者帳戶上的屬性組合。 您可以在 [Azure 入口網站](https://portal.azure.com/) 上建立自訂屬性，然後如下方所示，將這些屬性用於您的註冊原則中。 您也可以使用 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)讀取和寫入這些屬性。

> [!NOTE]
> 自訂屬性會使用 [Azure AD Graph API 目錄結構描述擴充](https://msdn.microsoft.com/library/azure/dn720459.aspx)。
> 
> 

## <a name="create-a-custom-attribute"></a>建立自訂屬性
1. 遵循下列步驟以[瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) (位於 Azure 入口網站上)。
2. 按一下 [使用者屬性] 。
3. 按一下刀鋒視窗頂端的 [新增]  。
4. 提供自訂屬性的**名稱** (例如 "ShoeSize") 和**說明** (選擇性)。 按一下 [建立] 。
   
   > [!NOTE]
   > 目前僅提供「字串」 **資料類型** 。
   > 
   > 

**使用者屬性**清單現已提供自訂屬性，您可用於註冊原則中。

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>在您的註冊原則中使用自訂屬性
1. 遵循下列步驟以[瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) (位於 Azure 入口網站上)。
2. 按一下 [註冊原則] 。
3. 按一下以開啟註冊原則 (例如 "B2C_1_SiUp")。 按一下刀鋒視窗頂端的 [編輯]  。
4. 按一下 [註冊屬性]  ，然後選取自訂屬性 (例如「ShoeSize」)。 按一下 [確定] 。
5. 按一下 [應用程式宣告]  ，然後選取自訂屬性。 按一下 [確定] 。
6. 按一下刀鋒視窗頂端的 [儲存]  。

您可以使用原則上的「立即執行」功能來驗證取用者體驗。 現在您應會在於取用者註冊期間收集之屬性的清單中看到「ShoeSize」，其亦會在傳回至您應用程式的權杖中出現。

## <a name="notes"></a>注意事項
* 除了註冊原則，自訂屬性也可以使用於註冊或登入原則和設定檔編輯原則。
* 自訂屬性有已知的限制。 只有在第一次用於任何原則時才會建立，而不是在您將它加入至 [使用者屬性] 清單時建立。

