---
title: "在 Azure Stack 中委派供應項目 | Microsoft Docs"
description: "了解如何讓其他人負責建立供應項目和為您註冊使用者。"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: alfredop
ms.openlocfilehash: 1a1f2789076b610ee557bde5981a331c55cc1c95
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="delegate-offers-in-azure-stack"></a>在 Azure Stack 中委派優惠

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

身為 Azure Stack 操作員，您往往會想要了解如何讓其他人負責建立供應項目和註冊使用者。 例如，如果您是服務提供者，或許會想要請轉銷商註冊客戶並代表您管理客戶。 或者，如果您是公司內中央 IT 群組的一員，或許您會想要讓子公司在不需要您介入的情況下註冊使用者。

委派可協助您進行這些工作，讓您可以接觸和管理超過您可以直接接觸和管理的使用者數量。 下圖顯示一個層級的委派，但 Azure Stack 支援多個層級。 委派的提供者 (DP) 可以依次委派給其他提供者，最多五個層級。

![委派層級](media/azure-stack-delegated-provider/image1.png)

Azure Stack 操作員可以藉由使用委派功能，將供應項目和使用者的建立委派給其他使用者。

## <a name="roles-and-steps-in-delegation"></a>委派中的角色和步驟
若要了解委派，請記住，這牽涉到三個角色：

* 「Azure Stack 操作員」會管理 Azure Stack 基礎結構、建立供應項目範本，並委派其他人來提供給使用者。

* 委派的 Azure Stack 操作員稱為*委派提供者*。 他們可以隸屬於其他組織 (例如其他 Azure Active Directory (Azure AD) 使用者)。

* 「使用者」會註冊使用供應項目，並用它們來管理其工作負載、建立 VM、儲存資料等等。

如下圖所示，設定委派有兩個步驟。

1. 識別委派的提供者。 若要做到這一點，您需要根據只包含訂用帳戶服務的方案，讓委派的提供者訂閱供應項目。 訂閱此供應項目的使用者會獲得一些 Azure Stack 操作員的能力，包括擴充供應項目和為使用者註冊的能力。

2. 向委派的提供者委派供應項目。 此供應項目可做為委派的提供者可提供項目的範本。 委派的提供者現在能夠取得該供應項目。 為該供應項目選擇名稱 (但不要變更其服務和配額)，並將它提供給客戶。

![建立委派的提供者，並讓它們能夠註冊使用者](media/azure-stack-delegated-provider/image2.png)

若要做為委派的提供者，使用者必須與主要提供者建立關係。 也就是說，他們必須建立訂用帳戶。 在此案例中，此訂用帳戶會將委派提供者識別為具有權限可代表主要提供者呈現供應項目。

在建立此關聯性之後，Azure Stack 操作員即可委派供應項目給委派的提供者。 委派的提供者現在就可以取得供應項目、為其重新命名 (但不能變更其本質)，並提供給客戶。

下列章節描述如何建立委派的提供者、委派供應項目，以及確認使用者可以註冊使用它。

## <a name="set-up-roles"></a>設定角色

若要查看工作的委派提供者，除了您的 Azure Stack 操作員帳戶，您需要額外的 Azure AD 帳戶。 如果您沒有帳戶，請建立兩個帳戶。 帳戶可以屬於任何 Azure AD 使用者。 我們將它們稱為委派的提供者和使用者。

| **角色** | **組織的權限** |
| --- | --- |
| 委派的提供者 |User |
| User |User |

## <a name="identify-the-delegated-providers"></a>識別委派的提供者
1. 以 Azure Stack 操作員身分登入。

2. 建立可讓使用者成為委派提供者的供應項目：
   
   a.  [建立方案](azure-stack-create-plan.md)。
       此方案應該僅包含訂閱服務。 在本文章中，我們會使用名為 **PlanForDelegation** 的方案。
   
   b.  根據此方案[建立供應項目](azure-stack-create-offer.md)。 在本文章中，我們會使用名為 **OfferToDP** 的供應項目。
   
   c.  在供應項目建立完成後，請將委派的提供者新增為此供應項目的訂閱者。 方法是選取 [訂用帳戶] > [新增] > [新增租用戶訂用帳戶]。
   
   ![新增委派的提供者來做為訂閱者](media/azure-stack-delegated-provider/image3.png)

> [!NOTE]
> 如同所有 Azure Stack 供應項目，您可以選擇讓供應項目公開並讓使用者註冊使用它，或是讓它保持私用並由 Azure Stack 操作員管理註冊。 委派的提供者通常是一個小型群組。 您會想控制可加入該群組的成員，所以在大部分情況下讓它保持私用是有意義的。
> 
> 

## <a name="azure-stack-operator-creates-the-delegated-offer"></a>Azure Stack 操作員建立委派的供應項目

您現在已建立您的委派提供者。 下一個步驟是建立您要委派以及您的客戶將會使用的方案和供應項目。 建議您完全依照要客戶看見它的方式定義此供應項目，因為委派的提供者將無法變更方案和它所包含的配額。

1. 身為 Azure Stack 操作員，請根據它[建立方案](azure-stack-create-plan.md)和[供應項目](azure-stack-create-offer.md)。 在本文章中，我們會使用名為 **DelegatedOffer** 的供應項目。
   
   > [!NOTE]
   > 此供應項目不必是公用。 但您也可以選擇讓其成為公用狀態。 不過在大部分情況下，您只會想讓委派的提供者有其存取權。 在如下列步驟所述委派私用供應項目後，委派的提供者即擁有其存取權。
   > 
   > 
1. 委派供應項目。 移至 **DelegatedOffer**。 然後在 [設定] 窗格中，選取 [委派的提供者] > [新增]。

2. 從下拉式清單方塊為委派的提供者選取訂用帳戶，然後選取 [委派]。

> ![新增委派的提供者](media/azure-stack-delegated-provider/image4.png)
> 
> 

## <a name="delegated-provider-customizes-the-offer"></a>委派的提供者會自訂供應項目

以委派的提供者身分登入使用者入口網站。 然後，以委派的供應項目做為範本建立新的供應項目。

1. 選取 [新增] > [租用戶供應項目 + 方案] > [供應項目]。

    ![建立新的供應項目](media/azure-stack-delegated-provider/image5.png)


1. 將名稱指派給供應項目。 這裡我們選擇 **ResellerOffer**。 選取要做為基礎的委派供應項目，然後選取 [建立]。
   
   ![指派名稱](media/azure-stack-delegated-provider/image6.png)

    >[!NOTE] 
    > 請注意相較於 Azure Stack 操作員所遇到的供應項目建立的差異。 委派的提供者不會透過基底方案和附加方案建構供應項目。 他們只能從已委派給他們的供應項目做出選擇，而無法變更這些供應項目。

1. 藉由依序選取 [瀏覽] 和 [供應項目]，讓供應項目成為公用狀態。 選取供應項目，然後選取 [變更狀態]。

2. 委派的提供者會透過自己的入口網站 URL 公開這些供應項目。 這些供應項目只會透過委派入口網站顯示。 若要尋找並變更此 URL：
   
    a.  選取 [瀏覽] > [更多服務] >  [訂用帳戶]。 然後選取委派的提供者的訂用帳戶。 在我們的案例中為 [DPSubscription] > [屬性]。
   
    b.  將入口網站 URL 複製到不同的位置，例如：記事本。
   
    ![選取委派的提供者的訂用帳戶](media/azure-stack-delegated-provider/dpportaluri.png)  
   
   您現在已透過委派的提供者身分建立委派的供應項目。 以委派的提供者身分登出。 關閉您已在使用的瀏覽器視窗。

## <a name="sign-up-for-the-offer"></a>註冊取得供應項目
1. 在新瀏覽器視窗中，移至您在上一個步驟中儲存的委派入口網站 URL。 以使用者身分登入入口網站。 
   
   >[!NOTE]
   > 對此步驟使用委派的入口網站。 否則，委派的供應項目會不可見。

2. 在儀表板中，選取 [取得訂用帳戶]。 您會看到只有委派的提供者所建立的委派供應項目才會呈現給使用者：

> ![檢視和選取供應項目](media/azure-stack-delegated-provider/image8.png)
> 
> 

委派供應項目的程序便已完成。 使用者現在可以藉由為其取得訂用帳戶，立即註冊使用此供應項目。

## <a name="multiple-tier-delegation"></a>多層委派

多層委派可讓提供者將供應項目委派給其他實體。 例如，這可讓您建立更深入的轉銷商管道，在此管道中，管理 Azure Stack 的提供者會將供應項目委派給散發者。 該散發者再委派給轉銷商。 Azure Stack 支援最多五個層級的委派。

若要建立多層供應項目委派，委派的提供者會依次將供應項目委派給下一個提供者。 此處理程序對委派的提供者與對 Azure Stack 操作員是相同的 (請參閱 [Azure Stack 操作員建立委派的供應項目](#cloud-operator-creates-the-delegated-offer))。

## <a name="next-steps"></a>後續步驟
[佈建 VM](azure-stack-provision-vm.md)

