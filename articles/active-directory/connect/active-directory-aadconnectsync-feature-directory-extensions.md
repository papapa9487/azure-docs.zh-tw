---
title: "Azure AD Connect 同步：目錄擴充 | Microsoft Docs"
description: "本主題說明 Azure AD Connect 中的目錄擴充功能。"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 16ce7da9321a4a36e892bad75819d1c30ab5197a
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同步處理：目錄擴充
目錄擴充可讓您從內部部署 Active Directory 利用自己的屬性擴充 Azure AD 中的架構。 此功能可讓您建置 LOB 應用程式來取用您在內部部署中持續進行管理的屬性。 透過 [Azure AD Graph 目錄擴充](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)或 [Microsoft Graph](https://graph.microsoft.io/) 即可取用這些屬性。 若要查看可用的屬性，可分別使用 [Azure AD Graph 總管](https://graphexplorer.azurewebsites.net/)和 [Microsoft Graph 總管](https://developer.microsoft.com/en-us/graph/graph-explorer)。

目前沒有任何 Office 365 工作負載取用這些屬性。

您可以在安裝精靈的自訂設定路徑中，設定您想要同步處理的其他屬性。
![結構描述擴充精靈](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  
安裝會顯示下列屬性，這些都是有效的候選項目：

* 使用者和群組物件類型
* 單一值屬性︰字串、布林值、整數、二進位檔
* 多值屬性︰字串、二進位檔


>[!NOTE]
> 儘管 Azure AD Connect 支援將多重值的 AD 屬性同步處理為 Azure AD 作為多重值的目錄擴充，但是 Azure AD 中目前沒有可支援多重值目錄擴充的功能。

屬性清單是從 Azure AD Connect 安裝期間建立的結構描述快取讀取。 如果您已使用其他屬性擴充 Active Directory 結構描述，則[必須重新整理結構描述](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) ，才可看見這些新屬性。

Azure AD 中的物件最多可有 100 個目錄擴充屬性。 長度上限是 250 個字元。 如果屬性值較長，則會被同步處理引擎截斷。

在 Azure AD Connect 安裝期間，會註冊可提供這些屬性的應用程式。 您可以在 Azure 入口網站中看到這個應用程式。  
![結構描述擴充應用程式](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

現在可透過 Graph 提供這些屬性：  
![圖形](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

屬性的前面會加上 extension\_{AppClientId}\_。 Azure AD 租用戶中的所有屬性會有相同的 AppClientId 值。

## <a name="next-steps"></a>後續步驟
深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md) 組態。

深入了解 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。
