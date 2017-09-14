---
title: "虛擬私人網路連線的 Azure Active Directory 條件式存取 (預覽) | Microsoft Docs"
description: "了解虛擬私人網路連線的 Azure Active Directory 條件式存取如何運作。 "
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
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: 540d8974ee2c02f80bccf28764b4d0d243e98d85
ms.contentlocale: zh-tw
ms.lasthandoff: 09/02/2017

---
# <a name="azure-active-directory-conditional-access-for-virtual-private-network-connectivity-preview"></a>虛擬私人網路連線的 Azure Active Directory 條件式存取 (預覽)

透過 [Azure Active Directory (Azure AD) 條件式存取](active-directory-conditional-access-azure-portal.md)，您可以微調授權使用者如何存取您的應用程式。 虛擬私人網路 (VPN) 連線的 Azure AD 條件式存取可讓您使用條件式存取保護您的 VPN 連線。


若要設定 VPN 連線的 Azure AD 條件式存取，需要完成下列步驟： 

1.  設定 VPN 伺服器
2.  設定 VPN 用戶端 
3.  設定條件式存取原則
4.  驗證


## <a name="before-you-begin"></a>開始之前

此主題假設您已熟悉下列主題：

- [Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)
- [VPN 和條件式存取](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

建議查看一下[使用自動 VPN 設定檔強化 Windows 10 中的遠端存取](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)，深入了解 Microsoft 如何實作這項功能。   


## <a name="prerequisites"></a>必要條件

為了設定虛擬私人網路連線的 Azure Active Directory 條件式存取，您需要設定 VPN 伺服器。 



## <a name="step-1---configure-your-vpn-server"></a>步驟 1 - 設定 VPN 伺服器 

這個步驟的目標是設定 Azure AD 的 VPN 驗證所用的根憑證。 為了設定虛擬私人網路連線的條件式存取，您需要：

1. 在 Azure 入口網站中建立 VPN 憑證
2. 下載 VPN 憑證
2. 將憑證部署至您的 VPN 伺服器

VPN 憑證是向 Azure AD 驗證 VPN 連線能力時，Azure AD 簽發給 Windows 10 用戶端的憑證時所用的簽發者。 試想一下，Windows 10 用戶端要求的權杖就是之後要呈現給應用程式的憑證，而在此案例中是 VPN 伺服器。

![條件式存取](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

在 Azure 入口網站中，您可以建立兩種憑證，以便在憑證即將到期時管理轉換。 您建立憑證時，可以選擇是否要將該憑證是當作主要憑證。 主要憑證在驗證時會實際用來簽署連線憑證。


**若要建立 VPN 憑證：**

1. 以全域管理員身分[登入](https://portal.azure.com) Azure 入口網站。

2. 在左側導覽列上，按一下 [Azure Active Directory]。 

    ![VPN 連線能力](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. 在 [Azure Active Directory] 頁面的 [管理] 區段中，按一下 [條件式存取]。

    ![VPN 連線能力](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. 在 [條件式存取] 頁面的 [管理] 區段中，按一下 [VPN 連線 \(預覽\)\]。

    ![VPN 連線能力](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. 在 [VPN 連線能力] 頁面上，按一下 [新增憑證]。

    ![VPN 連線能力](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. 在 [新增] 頁面上，執行下列步驟：

    ![VPN 連線能力](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. [持續時間] 請選取 [1 年]。

    b. [主要] 請選取 [是]。

    c. 按一下 [建立] 。

7. 在 [VPN 連線能力] 頁面上，按一下 [下載憑證]。


此時，您已準備好將新建立的憑證部署到 VPN 伺服器了。 在 VPN 伺服器上，需要新增下載的憑證做為 *VPN 驗證可信任的根 CA*。

對於 Windows RRAS 型部署，在 NPS 伺服器上需要執行下列命令，將根憑證新增到 *Enterprise NTauth* 存放區：

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2---configure-your-vpn-client"></a>步驟 2 - 設定 VPN 用戶端 

在此步驟中，您需要按照 [VPN 和條件式存取](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)所述，設定 VPN 用戶端的連線設定檔。


## <a name="step-3---configure-your-conditional-access-policy"></a>步驟 3 - 設定條件式存取原則

本節說明如何為 VPN 連線能力設定條件式存取原則。

**設定條件式存取原則：** 

1. 在 [條件式存取] 頁面頂端的工具列中，按一下 [新增]。

    ![條件式存取](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. 在 [新增] 頁面的 [名稱] 文字方塊中，輸入您的原則名稱，例如 **VPN 原則**。

    ![條件式存取](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. 在 [指派] 區段中，按一下 [使用者和群組]。

    ![條件式存取](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. 在 [使用者和群組] 頁面上，執行下列步驟︰

    ![條件式存取](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. 按一下 [選取使用者和群組]。

    b.這是另一個 C# 主控台應用程式。 按一下 [選取] 。

    c. 在 [選取] 頁面上，選取您的測試使用者，然後按一下 [選取]。

    d. 在 [使用者和群組] 頁面上，按一下 [完成]。

7. 在 [新增] 頁面上，執行下列步驟。

    ![條件式存取](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. 在 [指派] 區段中，按一下 [雲端應用程式]。

    b. 在 [雲端應用程式] 頁面上，按一下 [選取應用程式]，然後按一下 [選取]。

    c. 在 [選取] 頁面的 [應用程式] 文字方塊中，輸入 **vpn**。

    d. 選取 [VPN 伺服器]。

    e. 按一下 [選取] 。


13. 在 [新增] 頁面上，若要開啟 [授與] 頁面，請在 [控制] 區段中，按一下 [授與]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. 在 [授與] 頁面上，執行下列步驟︰

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. 選取 [需要 Multi-Factor Authentication]。

    b.這是另一個 C# 主控台應用程式。 按一下 [選取] 。

15. 在 [新增] 頁面的 [啟用原則] 之下，按一下 [開啟]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. 在 [新增] 頁面上，按一下 [建立]。



## <a name="next-steps"></a>後續步驟

若要深入了解 Microsoft 如何實作這項功能，請參閱[使用自動 VPN 設定檔強化 Windows 10 中的遠端存取](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)。    


