---
title: "Azure Active Directory 中的授權使用者 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 中進行自身和使用者的授權。"
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: c4509cdb003687083d0456c1957b19cf35ee056a
ms.contentlocale: zh-tw
ms.lasthandoff: 08/23/2017

---
# <a name="quickstart-license-users-in-azure-active-directory"></a>快速入門：在 Azure Active Directory 中授權使用者
以授權為基礎的 Azure AD 服務的運作方式，是在您的 Azure 租用戶中啟用 Azure Active Directory (Azure AD) 訂用帳戶。 在訂用帳戶啟用後，服務的功能便會由 Azure AD 系統管理員管理並供授權使用者使用。 當您購買 Enterprise Mobility + Security、Azure AD Premium 或 Azure AD Basic 時，您的租用戶會隨著訂用帳戶更新，包括其有效期間和預付授權。 您的訂用帳戶資訊，包括已指派或可使用的授權數目，可在 Azure 入口網站中 **Azure Active Directory** 底下藉由開啟 [授權] 圖格來取得。 [授權] 刀鋒視窗也是最適合用來管理授權指派的位置。

雖然只要取得訂用帳戶就能設定付費功能，但您仍必須為付費 Azure AD 付費功能指派使用者授權。 應存取 Azure AD 付費功能或者透過 Azure AD 付費功能管理的人員，都必須獲得授權。 授權指派是一種使用者與購買的服務 (例如 Azure AD Premium、Basic 或 Enterprise Mobility + Security) 之間的對應。

您可以使用[以群組為基礎的授權指派](active-directory-licensing-whatis-azure-portal.md)來設定規則，例如：
* 目錄中的所有使用者都自動取得授權
* 具有適當職稱的所有人員都取得授權
* 您可以將決定權委派給組織中的其他管理員 (方法是使用[自助服務群組](active-directory-accessmanagement-self-service-group-management.md))

> [!TIP]
> 如需指派授權給群組的詳細討論，包括進階案例和 Office 365 授權案例，請參閱[在 Azure Active Directory 中透過群組成員資格對使用者指派授權](active-directory-licensing-group-assignment-azure-portal.md)。

## <a name="assign-licenses-to-users-and-groups"></a>將授權指派給使用者和群組
使用有效的訂用帳戶，應該先將授權指派給自己並且重新整理瀏覽器，以確保您看到訂用帳戶所含的所有預期功能。 下一個步驟是將授權指派給需要存取 Azure AD 功能的使用者。 指派授權的簡單方法是將授權指派給使用者群組，而不是指派給個人。 當您對群組指派授權時，所有群組成員都會獲得授權。 如果在群組中新增或移除使用者，則系統也會自動指派或移除適當的授權。 

> [!NOTE]
> 並非所有位置都可使用某些 Microsoft 服務。 系統管理員必須先指定使用者的 [使用位置] 屬性，才能將授權指派給使用者。 您可以在 Azure 入口網站的 [使用者] &gt; [設定檔] &gt; [設定] 下設定此屬性。 在使用群組授權指派時，未指定使用位置的使用者皆會繼承目錄的位置。

若要指派授權，請在 [Azure Active Directory] &gt; [授權] &gt; [所有產品] 下，選取一或多個產品，然後選取命令列上的 [指派]。

![選取要指派的授權](media/license-users-groups/select-license-to-assign.png)

您可以使用 [使用者和群組] 刀鋒視窗來選擇多個使用者或群組，也可以停用產品中的服務方案。 使用最上方的搜尋方塊來搜尋使用者和群組名稱。

![選取要指派授權的使用者或群組](media/license-users-groups/select-user-for-license-assignment.png)

在指派授權給群組時，所有使用者需要一些時間才能繼承授權，時間長短取決於群組的大小。 您可以在 [群組] 刀鋒視窗的 [授權] 圖格底下查看處理狀態。

![授權指派狀態](media/license-users-groups/license-assignment-status.png)

在 Azure AD 授權指派期間，可能會發生指派錯誤，但在管理 Azure AD 和 Enterprise Mobility + Security 產品時則相對較少發生。 可能發生的指派錯誤僅限於：
- 指派衝突：使用者先前已被指派一個與最新授權不相容的授權。 在此情況下，若要指派新的授權，您必須先移除目前這一個授權。
- 超過可用的授權數目：當指派群組中的使用者數目超過可用的授權數目時，使用者的指派狀態將會反映因缺少授權的指派失敗。

### <a name="azure-ad-b2b-collaboration-licensing"></a>Azure AD B2B 共同作業授權

B2B 共同作業可讓您邀請來賓使用者加入您的 Azure AD 租用戶，讓他們能夠存取您開放使用的 Azure AD 服務與任何 Azure 資源。  

邀請 B2B 使用者並將他們指派給 Azure AD 中的應用程式並無須付費。 B2B 共同作業使用者可以免費使用最多 10 個應用程式 (每位來賓使用者) 和 3 份基本報告。 如果來賓使用者在夥伴的 Azure AD 租用戶中獲得任何適當的授權，則他們也會在您的租用戶中獲得授權。

雖非必要，但如果您想要讓他們存取 Azure AD 付費功能，那麼這些 B2B 來賓使用者必須獲得適當的 Azure AD 授權。 具有 Azure AD 付費授權的邀請方租用戶，可以將 B2B 共同作業使用者權限指派給另外五位受邀加入租用戶的來賓使用者。 如需這方面的案例和資訊，請參閱 [B2B 共同作業授權指引](active-directory-b2b-licensing.md)。

## <a name="view-assigned-licenses"></a>檢視已指派的授權

已指派授權和可用授權的摘要檢視會顯示在 [Azure Active Directory] &gt; [授權] &gt; [所有產品] 下。

![檢視授權摘要](media/license-users-groups/view-license-summary.png)

選取特定產品時，會顯示可用的已指派使用者和群組的詳細清單。 [授權的使用者] 清單會顯示目前取用授權的所有使用者，以及授權是直接指派給使用者或是從群組繼承。

![檢視授權詳細資料](media/license-users-groups/view-license-detail.png)

同樣地，[授權的群組] 清單會顯示已指派授權的所有群組。 選取使用者或群組可開啟 [授權] 刀鋒視窗，其中會顯示已對該物件指派的所有授權。

## <a name="remove-a-license"></a>移除授權

若要移除授權，請移至使用者或群組，然後開啟 [授權] 圖格。 選取授權，並按一下 [移除]。

![移除授權](media/license-users-groups/remove-license.png)

您無法直接移除使用者從群組那裡繼承的授權。 反而是要將使用者從繼承授權的群組之中移除。


## <a name="next-steps"></a>後續步驟
在本快速入門中，您學習到如何將授權指派給 Azure AD 目錄中的使用者和群組。 

您可以使用下列連結，從 Azure 入口網站設定 Azure AD 中的訂用帳戶授權指派。

> [!div class="nextstepaction"]
> [指派 Azure AD 授權](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Overview) 
