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
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 48b75233e1a485e8fb3fbce9776a6ef398c4e99c
ms.contentlocale: zh-tw
ms.lasthandoff: 09/27/2017

---
# <a name="delegating-offers-in-azure-stack"></a>在 Azure Stack 中委派提供項目

「適用於：Azure Stack 整合系統和 Azure Stack 開發套件」

身為 Azure Stack 操作員，您往往想要了解如何讓其他人負責建立供應項目和為您註冊使用者。 例如，如果您是服務提供者，並且想要轉銷商註冊客戶並代表您管理客戶。 如果您是中央 IT 群組的一部分，而且想要部門或子公司在不需要您介入的情況下註冊使用者，則在企業中也可能發生。

委派可協助您進行這些工作，協助您連接和管理超過您自己可直接管理數量的使用者。 下圖顯示一個層級的委派，但 Azure Stack 支援多個層級。 委派的提供者可以依次委派給其他提供者，最多五個層級。

![](media/azure-stack-delegated-provider/image1.png)

Azure Stack 操作員可以藉由使用委派功能，將供應項目和使用者的建立委派給其他使用者。

## <a name="roles-and-steps-in-delegation"></a>委派中的角色和步驟
若要了解委派，請記住，這牽涉到三個角色：

* Azure Stack 操作員會管理 Azure Stack 基礎結構、建立供應項目範本，並委派其他人來提供給使用者。
* 委派的 Azure Stack 操作員稱為**委派提供者**。 他們可以隸屬於其他組織 (例如其他 Azure Active Directory 使用者)。
* **使用者**註冊使用供應項目，並用它們來管理其工作負載、建立 VM、儲存資料等等。

如下圖所示，設定委派有兩個步驟。

1. **識別委派的提供者**：根據僅包含訂用帳戶服務的方案，讓委派的提供者訂閱供應項目。
   訂閱此供應項目的使用者會獲得一些 Azure Stack 操作員的能力，包括擴充供應項目和為使用者註冊的能力。
2. **向委派的提供者委派供應項目**：此供應項目可做為委派的提供者可提供項目的範本。 委派的提供者現在就可以取得供應項目、為其選擇名稱 (但不能變更其服務和配額)，並提供給客戶。

![](media/azure-stack-delegated-provider/image2.png)

若要做為委派的提供者，使用者必須與主要提供者建立關係；也就是說，他們必須建立訂用帳戶。 在此案例中，此訂用帳戶會將委派提供者識別為具有權限可代表主要提供者呈現供應項目。

建立此關聯性之後，Azure Stack 操作員即可委派供應項目給委派的提供者。 委派的提供者現在就可以取得供應項目、為其重新命名 (但不能變更其本質)，並提供給客戶。

下列章節描述如何建立委派的提供者、委派供應項目，以及確認使用者可以註冊使用它。

## <a name="set-up-roles"></a>設定角色

若要查看工作的委派提供者，除了您的 Azure Stack 操作員帳戶，您需要額外的 Azure Active Directory 帳戶。 如果您沒有帳戶，請建立兩個帳戶。 帳戶可以屬於任何 AAD 使用者。 我們將它們稱為委派的提供者 (DP) 和使用者。

| **角色** | **組織的權限** |
| --- | --- |
| 委派的提供者 |User |
| User |User |

## <a name="identify-the-delegated-providers"></a>識別委派的提供者
1. 以 Azure Stack 操作員身分登入。
2. 建立可讓使用者成為委派提供者的供應項目。 這需要您根據它建立方案和供應項目：
   
   a.  [建立方案](azure-stack-create-plan.md)。
       此方案應該僅包含訂閱服務。 在本文章中，我們會使用名為 PlanForDelegation 的方案。
   
   b.  根據此方案[建立供應項目](azure-stack-create-offer.md)。 在本文章中，我們會使用名為 OfferToDP 的供應項目。
   
   c.  供應項目的建立完成之後，請將委派的提供者新增為此供應項目的訂閱者，方法是依序按一下 [訂閱] &gt; [新增] &gt; [新的租用戶訂用帳戶]。
   
   ![](media/azure-stack-delegated-provider/image3.png)

> [!NOTE]
> 如同所有 Azure Stack 供應項目，您可以選擇讓供應項目公開並讓使用者註冊使用它，或是讓它保持在私人並由 Azure Stack 操作員管理註冊。 委派的提供者通常是一個小型群組，而且您想要控制可進入它的成員，所以在大部分情況下讓它保持為私人是有意義的。
> 
> 

## <a name="azure-stack-operator-creates-the-delegated-offer"></a>Azure Stack 操作員建立委派的供應項目

您現在已建立您的委派提供者。 下一個步驟是建立您要委派以及您的客戶將會使用的方案和供應項目。 您應該完全依照要客戶看見它的方式定義此供應項目，因為委派的提供者將無法變更方案和它所包含的配額。

1. 身為 Azure Stack 操作員，請根據它[建立方案](azure-stack-create-plan.md)和[供應項目](azure-stack-create-offer.md)。 在本文章中，我們會使用名為 DelegatedOffer 的供應項目。
   
   > [!NOTE]
   > 此供應項目不必是公用。 如果您想要可以設為公用，但是在大部分情況下，您只會要委派的提供者可存取它。 一旦您如下列步驟所述委派私人供應項目，委派的提供者即可存取它。
   > 
   > 
1. 委派供應項目。 移至 DelegatedOffer，並在 [設定] 窗格中，按一下 [委派的提供者] &gt; [新增]。
2. 從下拉式清單方塊選取委派的提供者的訂用帳戶，然後按一下 [委派]。

> ![](media/azure-stack-delegated-provider/image4.png)
> 
> 

## <a name="delegated-provider-customizes-the-offer"></a>委派的提供者會自訂供應項目

以委派的提供者身分登入使用者入口網站，並使用委派的供應項目做為範本建立新的供應項目。

1. 按一下 [新增] &gt; [租用戶供應項目 + 方案] &gt; [提供]。

    ![](media/azure-stack-delegated-provider/image5.png)


1. 將名稱指派給供應項目。 這裡我們選擇 ResellerOffer。 選取要以其為基礎的委派供應項目，然後按一下 [建立]。
   
   ![](media/azure-stack-delegated-provider/image6.png)

    >[!NOTE] 
    > 請注意相較於 Azure Stack 操作員所遇到的供應項目建立的差異。 委派的提供者不會從基礎方案和附加方案建構供應項目；他們只可以從已委派給他們的方案中選擇，並且無法對這些供應項目進行變更。

1. 依序按一下[瀏覽] &gt; [供應項目]、選取供應項目，並按一下 [狀態變更] 來讓供應項目公開。
2. 委派的提供者會透過自己的入口網站 URL 公開這些供應項目。 這些供應項目只會透過委派入口網站顯示。 若要尋找並變更此 URL：
   
    a.  按一下 [瀏覽]&gt; [更多服務]&gt; [訂閱]&gt;選取委派的提供者的訂用帳戶，在我們的案例中，這是 [DPSubscription] &gt; [屬性]。
   
    b.  將入口網站 URL 複製到不同的位置，例如：記事本。
   
    ![](media/azure-stack-delegated-provider/dpportaluri.png)  
   
   您現在已完成以委派的提供者身分建立委派的供應項目。 以委派的提供者身分登出。 關閉您已在使用的瀏覽器索引標籤。

## <a name="sign-up-for-the-offer"></a>註冊取得供應項目
1. 在新瀏覽器視窗中，移至您在上一個步驟中儲存的委派入口網站 URL。 以使用者身分登入入口網站。 附註：對此步驟使用委派的入口網站。 否則，委派的供應項目會不可見。
2. 在儀表板中，按一下 [取得訂用帳戶]。 您會看到只有委派的提供者所建立的委派供應項目才會呈現給使用者：

> ![](media/azure-stack-delegated-provider/image8.png)
> 
> 

這便完成了提供委派的程序。 使用者現在可以藉由為其取得訂用帳戶，立即註冊使用此供應項目。

## <a name="multiple-tier-delegation"></a>多層委派

多層委派可讓提供者將供應項目委派給其他實體。 例如，這允許建立更深入的轉銷商通道，在當中，管理 Azure Stack 的提供者會將供應項目委派給散發者，然後依次委派給轉售商。
Azure Stack 支援最多五個層級的委派。

若要建立多層供應項目委派，委派的提供者會依次將供應項目委派給下一個提供者。 此處理程序對委派的提供者與對 Azure Stack 操作員是相同的 (請參閱 [Azure Stack 操作員建立委派的供應項目](#cloud-operator-creates-the-delegated-offer))。

## <a name="next-steps"></a>後續步驟
[佈建 VM](azure-stack-provision-vm.md)


