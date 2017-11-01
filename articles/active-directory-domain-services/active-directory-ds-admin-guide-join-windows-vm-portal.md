---
title: "Azure Active Directory Domain Services：將 Windows Server VM 加入受管理的網域 | Microsoft Docs"
description: "將 Windows Server 虛擬機器加入 Azure AD 網域服務"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 5f661dba2e647ac905e7d84927fdbf6dbc76094f
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>將 Windows Server 虛擬機器加入受管理的網域
本文將說明如何使用 Azure 入口網站來部署 Windows Server 虛擬機器。 然後會說明如何將虛擬機器加入 Azure AD Domain Services 受管理網域。

## <a name="step-1-create-the-windows-server-virtual-machine"></a>步驟 1︰建立 Windows Server 虛擬機器
執行下列步驟，以建立 Windows 虛擬機器，並將其加入已在其中啟用 Azure AD 網域服務的虛擬網路。

1. 登入 Azure 入口網站，網址是 [http://portal.azure.com](http://portal.azure.com)。
2. 按一下 Azure 入口網站左上角的 [新增] 按鈕。
3. 選取 [計算]，然後選取 [Windows Server 2016 Datacenter]。

    ![選取映像](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. 在精靈的 [基本] 頁面上設定虛擬機器的基本設定。

    ![設定虛擬機器的基本設定](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > 您在此處輸入的使用者名稱和密碼是用於登入虛擬機器的本機系統管理員帳戶。 挑選強式密碼來保護虛擬機器，防止暴力密碼破解攻擊。 請勿在此輸入網域使用者帳戶的認證。
    >

5. 選取虛擬機器的 [大小]。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。

    ![選取虛擬機器的大小](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. 在精靈的 [設定] 頁面中，選取您的 Azure AD Domain Services 受管理網域部署所在的虛擬網路。 挑選與您的受管理網域部署所在的不同子網路。 對於其他設定，請保留預設值並按一下 [確定]。

    ![選取虛擬機器的虛擬網路](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **挑選適當的虛擬網路和子網路。**
    > 選取您的受管理網域部署所在的虛擬網路，或使用虛擬網路對等互連與其連線的虛擬網路兩者之一。 如果您選取未連線的虛擬網路，則無法將虛擬機器加入受管理的網域中。
    > 建議您將受管理網域部署至專用的子網路。 因此，請勿挑選已啟用受管理網域所在的子網路。

7. 在 [購買] 頁面上，檢閱設定然後按一下 [確定] 來部署虛擬機器。
8. VM 部署已釘選到 Azure 入口網站儀表板。

    ![完成](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
9. 部署完成之後，您可以在 [概觀] 頁面中查看虛擬機器的相關資訊。


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>步驟 2︰使用本機系統管理員帳戶連線到 Windows Server 虛擬機器
現在，連線到新建立的 Windows Server 虛擬機器，以便將其加入網域。 使用建立虛擬機器時所指定的本機系統管理員認證。

執行下列步驟以連線到虛擬機器。

1. 在 [概觀] 頁面上按一下 [連線] 按鈕。 隨即建立並下載遠端桌面通訊協定 (.rdp) 檔案。

    ![連線至 Windows 虛擬機器](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. 若要連線至您的 VM，請開啟下載的 RDP 檔案。 出現提示時，按一下 [連線]。
3. 在登入提示中，輸入您在建立虛擬機器時所指定的 [本機系統管理員認證] 。 例如，我們在此範例中使用 'localhost\mahesh'。
4. 您可能會在登入過程中收到憑證警告。 按一下 [是] 或 [繼續] 以繼續進行連線。

此時，您應該已使用本機系統管理員認證登入到新建立的 Windows 虛擬機器。 下一個步驟是將虛擬機器加入網域。


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>步驟 3︰將 Windows Server 虛擬機器加入 AAD-DS 受管理網域
請執行下列步驟，以將 Windows Server 虛擬機器加入 AAD-DS 受管理網域。

1. 如步驟 2 所示連線到 Windows Server。 在 [開始] 畫面中開啟 [伺服器管理員] 。
2. 按一下 [伺服器管理員] 視窗之左窗格中的 [本機伺服器]  。

    ![啟動虛擬機器上的伺服器管理員](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. 按一下 [屬性] 區段底下的 [工作群組]。 在 [系統屬性] 屬性頁中，按一下 [變更] 來加入網域。

    ![[系統屬性] 頁面](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. 在 [網域] 文字方塊中指定 Azure AD Domain Services 受管理網域的網域名稱，然後按一下 [確定]。

    ![指定要加入的網域](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. 系統會提示您輸入認證以便加入網域。 請確定您是 **指定屬於 AAD DC 系統管理員群組之使用者的認證** 。 只有此群組的成員才有權限可以將機器加入受管理的網域。

    ![指定用於加入網域的認證](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. 您可以透過下列任一方式指定認證︰

   * **UPN 格式：(建議)** 指定 Azure AD 中所設定使用者帳戶的 UPN 尾碼。 在此範例中，使用者 'bob' 的 UPN 尾碼是 'bob@domainservicespreview.onmicrosoft.com'。
   * **SAMAccountName 格式**：您可以使用 SAMAccountName 格式指定帳戶名稱。 在此範例中，使用者 'bob' 必須輸入 'CONTOSO100\bob'。

     > [!TIP]
     > **建議使用 UPN 格式來指定認證。**
     > 如果使用者的 UPN 前置詞太長 (例如 'joehasareallylongname')，就可能自動產生 SAMAccountName。 如果您的 Azure AD 租用戶中有多個使用者擁有相同的 UPN 前置詞 (例如 'bob')，則服務可能會自動產生它們的 SAMAccountName 格式。 在這些情況下，您可以使用 UPN 格式可靠地登入網域。
     >

7. 順利加入網域後，您會看到下列訊息歡迎您加入網域。 重新啟動虛擬機器，以便完成加入網域作業。

    ![歡迎加入網域](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>針對加入網域進行疑難排解
### <a name="connectivity-issues"></a>連線能力問題
如果虛擬機器找不到網域，請參閱下列疑難排解步驟︰

* 確定虛擬機器已連線到您已在其中啟用網域服務的相同虛擬網路。 若非如此，虛擬機器便無法連線到網域，因此無法加入網域。
* 確定虛擬機器所在的虛擬網路會輪流連接到您已啟用網域服務的虛擬網路。
* 嘗試使用受管理網域的網域名稱來 ping 網域 (例如 'ping contoso100.com')。 如果您無法這麼做，請嘗試 ping 頁面上所顯示、您已在其中啟用 Azure AD 網域服務之網域的 IP 位址 (例如 'ping 10.0.0.4')。 如果您能夠 ping 該 IP 位址，但無法 ping 網域，則表示 DNS 的設定可能不正確。 檢查網域的 IP 位址是否設定為虛擬網路的 DNS 伺服器。
* 請嘗試排清虛擬機器上的 DNS 解析程式快取 ('ipconfig /flushdns')。

如果您看到對話方塊要求您提供認證以加入網域，則表示您沒有連線問題。

### <a name="credentials-related-issues"></a>與認證相關的問題
如果您的認證有問題因而無法加入網域，請參閱下列步驟。

* 嘗試使用 UPN 格式來指定認證。 如果您的租用戶中有多個使用者具有相同的 UPN 前置詞，或您的 UPN 前置詞太長，可能就會自動為您的帳戶產生 SAMAccountName。 因此，您帳戶的 SAMAccountName 格式可能會與您在內部部署網域中預期或使用的格式不同。
* 嘗試使用屬於「AAD DC 系統管理員」群組的使用者帳戶認證。
* 確定您已根據《入門指南》中所述的步驟來 [啟用密碼同步處理](active-directory-ds-getting-started-password-sync.md) 。
* 確定您使用 Azure AD 中所設定的使用者 UPN (例如 'bob@domainservicespreview.onmicrosoft.com') 來登入。
* 確定您已如《入門指南》中所指定的等候夠久的時間以讓密碼同步處理完成。

## <a name="related-content"></a>相關內容
* [Azure AD Domain Services - 入門指南](active-directory-ds-getting-started.md)
* [Administer an Azure AD Domain Services managed domain (管理 Azure AD 網域服務受管理的網域)](active-directory-ds-admin-guide-administer-domain.md)
