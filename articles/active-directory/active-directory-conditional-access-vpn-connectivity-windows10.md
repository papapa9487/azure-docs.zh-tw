---
title: "VPN 連線能力的 Azure Active Directory 條件式存取 (預覽) | Microsoft Docs"
description: "了解 VPN 連線能力的 Azure Active Directory 條件式存取如何運作。 "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: e9dadb3291ee760e7b05caedfa6b4128be77aa7d
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>VPN 連線能力的 Azure Active Directory 條件式存取 (預覽)

透過 [Azure Active Directory (Azure AD) 條件式存取](active-directory-conditional-access-azure-portal.md)，您可以微調授權使用者如何存取您的應用程式。 您可以使用虛擬私人網路 (VPN) 連線的 Azure AD 條件式存取，來協助保護您的 VPN 連線能力。


若要設定 VPN 連線能力的條件式存取，需要完成下列步驟： 

1.  設定 VPN 伺服器。
2.  設定 VPN 用戶端。
3.  設定條件式存取原則。


## <a name="before-you-begin"></a>開始之前

此主題假設您熟悉下列主題：

- [Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)
- [VPN 和條件式存取](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

若要深入了解 Microsoft 如何實作這項功能，請參閱[使用自動 VPN 設定檔強化 Windows 10 中的遠端存取](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)。   


## <a name="prerequisites"></a>必要條件

若要設定 VPN 連線能力的 Azure Active Directory 條件式存取，您需要設定 VPN 伺服器。 



## <a name="step-1-configure-your-vpn-server"></a>步驟 1：設定 VPN 伺服器 

這個步驟會設定 Azure AD 進行 VPN 驗證所用的根憑證。 若要設定 VPN 連線能力的條件式存取，您需要執行下列動作：

1. 在 Azure 入口網站中建立 VPN 憑證。
2. 下載 VPN 憑證。
2. 將憑證部署至 VPN 伺服器。

Azure AD 會使用 VPN 憑證，簽署在向 Azure AD 驗證 VPN 連線能力時簽發給 Windows 10 用戶端的憑證。 Windows 10 用戶端要求的權杖就是之後要呈現給應用程式的憑證，而在此案例中是 VPN 伺服器。

![下載憑證進行條件式存取](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

在 Azure 入口網站中，您可以建立兩種憑證，以便在一個憑證即將到期時管理轉換。 建立憑證時，您可以選擇它是否為主要憑證，即驗證期間用來簽署憑證進行連線的憑證。

若要建立 VPN 憑證：

1. 以全域管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側功能表上，按一下 [Azure Active Directory]。 

    ![選取 Azure Active Directory](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. 在 [Azure Active Directory] 頁面的 [管理] 區段中，按一下 [條件式存取]。

    ![選取條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. 在 [條件式存取] 頁面的 [管理] 區段中，按一下 [VPN 連線 \(預覽\)\]。

    ![選取 VPN 連線能力](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. 在 [VPN 連線能力] 頁面上，按一下 [新增憑證]。

    ![選取新的憑證](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. 在 [新增] 頁面上，執行下列步驟：

    ![選取持續時間和主要](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. 針對 [持續時間]，請選取 [1 年]。

    b. 針對 [主要]，請選取 [是]。

    c. 按一下 [建立] 。

7. 在 [VPN 連線能力] 頁面上，按一下 [下載憑證]。


現在，您已準備好將新建立的憑證部署到 VPN 伺服器。 在 VPN 伺服器上，請新增下載的憑證作為 *VPN 驗證可信任的根 CA*。

對於以 Windows RRAS 為基礎的部署，在 NPS 伺服器上執行下列命令，將根憑證新增到 *Enterprise NTauth* 存放區：

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>步驟 2：設定 VPN 用戶端 

在此步驟中，請按照 [VPN 和條件式存取](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)所述，設定 VPN 用戶端的連線能力設定檔。


## <a name="step-3-configure-your-conditional-access-policy"></a>步驟 3：設定條件式存取原則

本節說明如何為 VPN 連線能力設定條件式存取原則。


1. 在 [條件式存取] 頁面頂端的工具列中，按一下 [新增]。

    ![在條件式存取頁面上選取新增](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. 在 [新增] 頁面的 [名稱] 方塊中，輸入您的原則名稱。 例如，輸入 **VPN policy**。

    ![在條件式存取頁面上新增名稱](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. 在 [指派] 區段中，按一下 [使用者和群組]。

    ![選取使用者和群組](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. 在 [使用者和群組] 頁面上，執行下列步驟︰

    ![選取測試使用者](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. 按一下 [選取使用者和群組]。

    b.這是另一個 C# 主控台應用程式。 按一下 [選取] 。

    c. 在 [選取] 頁面上，選取您的測試使用者，然後按一下 [選取]。

    d. 在 [使用者和群組] 頁面上，按一下 [完成]。

7. 在 [新增] 頁面上，執行下列步驟：

    ![選取雲端應用程式](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. 在 [指派] 區段中，按一下 [雲端應用程式]。

    b. 在 [雲端應用程式] 頁面上，按一下 [選取應用程式]，然後按一下 [選取]。

    c. 在 [選取] 頁面的 [應用程式] 方塊中，輸入 **vpn**。

    d. 選取 [VPN 伺服器]。

    e. 按一下 [選取] 。


13. 在 [新增] 頁面上，若要開啟 [授與] 頁面，請在 [控制] 區段中，按一下 [授與]。

    ![選取授與](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. 在 [授與] 頁面上，執行下列步驟︰

    ![選取需要多重要素驗證](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. 選取 [需要 Multi-Factor Authentication]。

    b.這是另一個 C# 主控台應用程式。 按一下 [選取] 。

15. 在 [新增] 頁面的 [啟用原則] 之下，按一下 [開啟]。

    ![啟用原則](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. 在 [新增] 頁面上，按一下 [建立]。



## <a name="next-steps"></a>後續步驟

若要深入了解 Microsoft 如何實作這項功能，請參閱[使用自動 VPN 設定檔強化 Windows 10 中的遠端存取](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)。    

