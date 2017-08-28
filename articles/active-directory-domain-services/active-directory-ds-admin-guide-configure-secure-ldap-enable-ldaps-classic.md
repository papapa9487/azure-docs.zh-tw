---
title: "在 Azure AD Domain Services 中設定安全的 LDAP (LDAPS) | Microsoft Docs"
description: "針對 Azure AD 網域服務受管理網域設定安全的 LDAP (LDAPS)"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9d563c45-9578-410d-96c8-355af64feae8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 3aafe209aad7383cd0610d147b5fdba673023c93
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>針對 Azure AD 網域服務受管理網域設定安全的 LDAP (LDAPS)

## <a name="before-you-begin"></a>開始之前
確定您已完成[工作 2 - 將安全的 LDAP 憑證匯出到 .PFX 檔案](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)。

選擇要使用預覽 Azure 入口網站體驗或 Azure 傳統入口網站來完成這項工作。
> [!div class="op_single_selector"]
> * **Azure 入口網站 (預覽)**：[使用 Azure 入口網站啟用安全 LDAP](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
> * **Azure 傳統入口網站**：[使用傳統 Azure 入口網站啟用安全 LDAP](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps-classic.md)
>
>


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal"></a>工作 3 - 使用傳統 Azure 入口網站為受管理的網域啟用安全 LDAP
若要啟用安全的 LDAP，請執行下列設定步驟：

1. 瀏覽至 **[Azure 傳統入口網站](https://manage.windowsazure.com)**。
2. 在左窗格中，選取 [Active Directory]  節點。
3. 選取已啟用 Azure AD 網域服務的 Azure AD 目錄 (也稱為「租用戶」)。

    ![選取 Azure AD 目錄](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. 按一下 [設定]  索引標籤。

    ![設定目錄的索引標籤](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. 向下捲動到標題為 [網域服務] 的區段。 您應該會看到標題為 [安全 LDAP (LDAPS)]  的選項，如下列螢幕擷取畫面所示：

    ![網域服務組態區段](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)
6. 按一下 [設定憑證...] 按鈕以顯示 [設定安全 LDAP 的憑證] 對話方塊。

    ![設定安全 LDAP 的憑證](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)
7. 按一下 [含有憑證的 PFX 檔案] 後面的資料夾圖示，指定要用於對受管理網域進行安全 LDAP 存取之憑證所在的 PFX 檔案。 此外，也請輸入將憑證匯出到 PFX 檔案時所指定的密碼。 然後，按一下底部的完成按鈕。

    ![指定安全 LDAP 的 PFX 檔案和密碼](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)
8. [設定] 索引標籤的 [網域服務] 區段應該會變成灰色，而且有幾分鐘的時間處在 [擱置...] 狀態。 在此期間，LDAPS 憑證會進行驗證以確認是否正確，並為受管理的網域設定安全 LDAP。

    ![安全 LDAP - 擱置中狀態](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

   > [!NOTE]
   > 為受管理的網域啟用安全 LDAP 需要約 10 到 15 分鐘的時間。 如果提供的安全 LDAP 憑證不符合所需的準則，則不會為您的目錄啟用安全 LDAP，而且您會看到失敗。 例如，網域名稱不正確、憑證已到期或即將到期等。
   >
   >

9. 在為受管理的網域成功啟用安全 LDAP 後，[擱置...]  訊息應該會消失。 您應該會看到已顯示憑證的指紋。

    ![安全 LDAP 已啟用](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>

## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>工作 4 - 透過網際網路啟用安全 LDAP 存取
**選擇性工作** - 如果您不打算使用 LDAPS 來透過網際網路存取受管理的網域，請略過這項設定工作。

開始這項工作之前，請先確定您已完成 [工作 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal)中所述的步驟。

1. 您應該會在 [設定] 頁面的 [網域服務] 區段中看到 [透過網際網路啟用安全 LDAP 存取] 的選項。 此選項會設定為 [否]  ，因為依預設會停用透過安全 LDAP 對受管理網域的網際網路存取。

    ![安全 LDAP 已啟用](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)
2. 將 [透過網際網路啟用安全 LDAP 存取] 切換為 [是]。 按一下底部面板上的 [儲存]  按鈕。
    ![安全 LDAP 已啟用](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)
3. [設定] 索引標籤的 [網域服務] 區段應該會變成灰色，而且有幾分鐘的時間處在 [擱置...] 狀態。 稍後會啟用透過安全 LDAP 對受管理網域的網際網路存取。

    ![安全 LDAP - 擱置中狀態](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

   > [!NOTE]
   > 為受管理網域啟用透過安全 LDAP 的網際網路存取需要約 10 分鐘的時間。
   >
   >
4. 在成功啟用透過網際網路對受管理網域進行安全 LDAP 存取後，[擱置...]  訊息應該會消失。 您應該會在 [LDAPS 存取的外部 IP 位址] 欄位中，看到可用來透過 LDAPS 存取您目錄的外部 IP 位址。

    ![安全 LDAP 已啟用](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>工作 5 - 設定 DNS 以從網際網路存取受管理的網域
**選擇性工作** - 如果您不打算使用 LDAPS 來透過網際網路存取受管理的網域，請略過這項設定工作。

開始這項工作之前，請先確定您已完成 [工作 4](#task-4---enable-secure-ldap-access-over-the-internet)中所述的步驟。

為受管理的網域啟用了透過網際網路的安全 LDAP 存取後，您需要更新 DNS 以便用戶端電腦可以找到此受管理網域。 在工作 4 的最後階段，[設定] 索引標籤的 [LDAPS 存取的外部 IP 位址] 中會顯示外部 IP 位址。

請設定外部 DNS 提供者，讓受管理網域的 DNS 名稱 (例如 'ldaps.contoso100.com') 指向這個外部 IP 位址。 在我們的範例中，我們需要建立下列 DNS 項目︰

    ldaps.contoso100.com  -> 52.165.38.113

這樣就大功告成了。您現在已準備好可使用安全 LDAP 透過網際網路連線到受管理網域。

> [!WARNING]
> 請記住，用戶端電腦必須信任 LDAPS 憑證的簽發者，才能成功使用 LDAPS 連線到受管理網域。 如果您使用企業憑證授權單位或公開的受信任憑證授權單位，您不需要採取任何動作，因為用戶端電腦會信任這些憑證簽發者。 如果您使用自我簽署憑證，則必須在用戶端電腦的受信任憑證存放區中安裝自我簽署憑證的公開部分。
>
>


## <a name="lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>鎖定透過網際網路對於受管理網域的安全 LDAP 存取
> [!NOTE]
> **選擇性工作** - 如果您尚未啟用透過網際網路對於受管理的網域之 LDAPS 存取，請略過這項設定工作。
>
>

開始這項工作之前，請先確定您已完成 [工作 4](#task-4---enable-secure-ldap-access-over-the-internet)中所述的步驟。

公開受管理的網域透過網際網路進行 LDAPS 存取，代表著安全性威脅。 受管理的網域可以從用於安全 LDAP 之通訊埠的網際網路 (也就是連接埠 636) 存取。 因此，您可以選擇將受管理的網域存取限制為特定已知 IP 位址。 為了提升安全性，建立網路安全性群組 (NSG)，並將它與您已啟用 Azure AD Domain Services 的子網路產生關聯。

下表說明您可以設定的範例 NSG，以鎖定透過網際網路的安全 LDAP 存取。 NSG 包含一組規則，允許僅從一組指定 IP 位址透過 TCP 連接埠 636 的輸入 LDAPS 存取。 預設 'DenyAll' 規則適用於來自網際網路的所有其他輸入流量。 允許從指定的 IP 位址透過網際網路之 LDAPS 存取的 NSG 規則，其優先順序高於 DenyAll NSG 規則。

![透過網際網路之安全 LDAP 存取的範例 NSG](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**詳細資訊** - [網路安全性群組](../virtual-network/virtual-networks-nsg.md)。

<br>

## <a name="related-content"></a>相關內容
* [Azure AD Domain Services - 入門指南](active-directory-ds-getting-started.md)
* [Administer an Azure AD Domain Services managed domain (管理 Azure AD 網域服務受管理的網域)](active-directory-ds-admin-guide-administer-domain.md)
* [管理 Azure AD Domain Services 受管理網域上的群組原則](active-directory-ds-admin-guide-administer-group-policy.md)
* [網路安全性群組](../virtual-network/virtual-networks-nsg.md)
* [建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

