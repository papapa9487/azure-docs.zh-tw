---
title: "Azure Active Directory Domain Services︰啟用 Azure Active Directory Domain Services | Microsoft Docs"
description: "使用 Azure 傳統入口網站啟用 Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c659da59-f4b5-4edd-b702-1727a8ccb36f
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: e3f8dd4b820da8a14437aa313e4296dc17111674
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-classic-portal"></a>使用 Azure 傳統入口網站啟用 Azure Active Directory Domain Services

> [!IMPORTANT]
> 即將移除啟用本文所示 Azure AD Domain Services 的 Azure 傳統入口網站體驗。 對於新的部署，[**請改用新的 (預覽) Azure 入口網站體驗**](active-directory-ds-getting-started.md)。
>

## <a name="task-3-enable-azure-active-directory-domain-services"></a>工作 3︰啟用 Azure Active Directory Domain Services
在此工作中，您執行下列步驟來啟用您目錄的 Azure Active Directory Domain Services (Azure AD DS)︰

1. 前往 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 在左窗格中選取 [Active Directory] 按鈕。
3. 選取您要啟用 Azure AD DS 的 Azure Active Directory (Azure AD) 租用戶 (目錄)。

    ![選取 Azure AD 目錄](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. 在 [預覽目錄] 頁面上，按一下 [設定] 索引標籤。

    ![設定目錄的索引標籤](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. 在 [網域服務] 下，將 [啟用此目錄的網域服務] 選項變更為 [是]。  
    其他的 Azure Active Directory Domain Services 組態選項會出現在頁面上。

    ![啟用網域服務](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

   > [!NOTE]
   > 當您針對租用戶啟用 Azure Active Directory Domain Services 時，Azure AD 會產生並儲存驗證使用者所需的 Kerberos 和 NTLM 認證雜湊。
   >
   >
6. 指定 **網域服務的 DNS 網域名稱**。

   * 根據預設，將會選取目錄的預設網域名稱 (具有 **.onmicrosoft.com** 尾碼)。

   * 清單包含所有已針對 Azure AD 目錄設定的網域，包括您在 [網域] 索引標籤中設定的已驗證以及未驗證的網域。

   * 您也可以輸入自訂網域名稱。 在此範例中，自訂網域名稱是 contoso100.com。

     > [!WARNING]
     > 指定網域名稱的前置詞 (例如，contoso100.com 網域名稱中的 contoso100) 必須包含 15 個以內的字元。 您無法使用包含超過 15 個字元的前置詞建立 Azure Active Directory Domain Services。
     >
     >
7. 確定虛擬網路中還沒有您為受管理網域選擇的 DNS 網域名稱。 具體來說，檢查是否︰

   * 您在虛擬網路上已有包含相同 DNS 網域名稱的網域。

   * 您選取的虛擬網路已透過 VPN 連線到內部部署網路，而您在內部部署網路上有 DNS 網域名稱相同的網域。

   * 您在虛擬網路上已有具有該名稱的雲端服務。
8. 選取您想要 Azure Active Directory Domain Services 可用的虛擬網路。 在 [將網域服務連線至此虛擬網路] 下拉式清單中，選取您建立的虛擬網路和專用子網路。 並請考慮下列項目：

   * 請確定您指定的虛擬網路屬於 Azure Active Directory Domain Services 所支援的 Azure 區域。 如需可使用 Azure Active Directory Domain Services 的 Azure 區域清單，請參閱[依區域提供的 Azure 服務](https://azure.microsoft.com/regions/#services/)頁面。

   * 屬於不支援 Azure Active Directory Domain Services 區域的虛擬網路，不會出現在下拉式清單中。

   * 在虛擬網路內使用適用於 Azure Active Directory Domain Services 的專用子網路。 請勿選取閘道子網路。 請參閱[網路考量](active-directory-ds-networking.md)。

   * 同樣地，使用 Azure Resource Manager 建立的虛擬網路也不會出現在下拉式清單中。 Resource Manager 型虛擬網路目前不為 Azure Active Directory Domain Services 所支援。
9. 若要啟用 Azure Active Directory Domain Services，請按一下頁面底部工作窗格中的 [儲存]。
    * 在為您的目錄啟用 Azure Active Directory Domain Services 時，頁面會顯示 [暫止] 狀態。

        ![啟用 [網域服務] 視窗](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

        > [!NOTE]
        > Azure Active Directory Domain Services 可針對您的受管理網域提供高可用性。 啟用 Azure Active Directory Domain Services 後，可在虛擬網路上使用網域服務的 IP 位址會逐一顯示。 一旦服務針對您的網域啟用高可用性之後，第二個 IP 位址便會立即顯示在第一個之後。 在針對您的網域設定高可用性並使它成為作用中狀態時，您應該會在 [設定] 索引標籤的 [網域服務] 區段中看到兩個 IP 位址。
        >
        >
    * 大約 20 至 30 分鐘後，會在 [設定] 頁面的 [IP 位址] 欄位中看見可在您虛擬網路上使用「網域服務」的第一個 IP 位址。

        ![顯示第一次佈建 IP 的 [網域服務] 視窗](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
    * 若您的網域可支援高可用性，則會在頁面上顯示兩個 IP 位址。 在所選虛擬網路上，從這兩個 IP 位址可使用您的受管理網域。

10. 記下這兩個 IP 位址，以更新您虛擬網路的 DNS 設定。 如此會讓虛擬網路上的虛擬機器連線到網域，以進行像是加入網域等作業。

    ![顯示兩個已佈建 IP 的 [網域服務] 視窗](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> 視 Azure AD 租用戶大小而定 (例如，使用者或群組等的數目)，同步處理至您的受管理網域需要一些時間。 這個同步處理程序會在背景執行。 對於具有成千上萬個物件的大型租用戶，則可能需要一到兩天的時間，所有使用者、群組成員資格和認證才會完成同步處理。
>
>

## <a name="next-step"></a>後續步驟
[工作 4：更新 Azure 虛擬網路的 DNS 設定](active-directory-ds-getting-started-update-dns.md)

