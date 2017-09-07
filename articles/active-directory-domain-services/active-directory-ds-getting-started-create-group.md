---
title: "Azure Active Directory Domain Services：建立 Azure AD DC 系統管理員群組 | Microsoft Docs"
description: "使用 Azure 傳統入口網站啟用 Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 22e311b530b32504931c39491dc8324894ab4091
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-classic-portal"></a>使用 Azure 傳統入口網站啟用 Azure Active Directory Domain Services
> [!IMPORTANT]
> 即將移除啟用本文所示 Azure AD Domain Services 的 Azure 傳統入口網站體驗。 對於新的部署，[**請改用新的 (預覽) Azure 入口網站體驗**](active-directory-ds-getting-started.md)。
>

本文將協助您使用 Azure 傳統入口網站來啟用 Azure Active Directory (Azure AD) 租用戶的 Azure Active Directory Domain Services (Azure AD DS)。


## <a name="task-1-create-the-azure-ad-dc-administrators-group"></a>工作 1：建立 Azure AD DC 系統管理員群組
第一個工作是在您的 Azure AD 租用戶中建立系統管理群組。 這個特殊的系統管理群組稱為 *AAD DC 系統管理員*。 此群組的成員會獲得電腦的系統管理權限，而這類電腦已加入受 Azure Active Directory Domain Services 管理的網域。 在加入網域的電腦上，這個群組會新增到系統管理員群組。 此外，此群組的成員可以使用遠端桌面，從遠端連接到已加入網域的電腦。  

> [!NOTE]
> 您在使用 Azure Active Directory Domain Services 所建立的受管理網域內，沒有「網域系統管理員」或「企業系統管理員」權限。 在受管理的網域上，服務會保留這些權限，並不會提供給租用戶中的使用者使用。 不過，您可以使用在此組態工作中建立的特殊系統管理群組，執行某些特殊權限的作業。 這些作業包括將電腦加入網域、隸屬於已加入網域之電腦上的管理群組，以及設定群組原則。
>

在這個組態工作中，您會建立系統管理群組，並將目錄中的一或多位使用者加入該群組。 若要為 Azure Active Directory Domain Services 建立系統管理群組，請執行下列步驟：

1. 前往 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 在左窗格中選取 [Active Directory] 按鈕。
3. 選取您要啟用 Azure Active Directory Domain Services 的 Azure AD 租用戶 (目錄)。 您只能針對每個 Azure AD 目錄建立一個網域。

    ![選取 Azure AD 目錄](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. 在 [預覽目錄] 頁面上，按一下 [群組] 索引標籤。
5. 若要將群組新增至 Azure AD 租用戶，請在視窗底部的工作窗格中，按一下 [新增群組]。

    ![[新增群組] 按鈕](./media/active-directory-domain-services-getting-started/add-group-button.png)
6. 在 [新增群組] 對話方塊中，建立名為 **AAD DC 系統管理員**的群組，然後將 [群組類型] 設定為 [安全性]。

   > [!WARNING]
   > 若要在受 Azure Active Directory Domain Services 管理的網域內啟用存取，請以這個確切名稱建立群組。
   >
   >

    ![[新增群組] 對話方塊](./media/active-directory-domain-services-getting-started/create-admin-group.png)
7. 在 [描述] 方塊中輸入描述，讓其他人了解此群組可授與 Azure Active Directory Domain Services 內的系統管理權限。
8. 建立該群組之後，按一下群組名稱以檢視其屬性。
9. 若要將使用者新增為此群組的成員，請按一下視窗底部的 [新增成員] 按鈕。

    ![新增群組成員按鈕](./media/active-directory-domain-services-getting-started/add-group-members-button.png)
10. 在 [新增成員] 對話方塊中，選取應成為此群組成員的使用者，然後按一下右下角的核取記號圖示。

    ![將使用者新增至系統管理員群組](./media/active-directory-domain-services-getting-started/add-group-members.png)


## <a name="next-step"></a>後續步驟
[工作 2：建立或選取 Azure 虛擬網路](active-directory-ds-getting-started-vnet.md)

