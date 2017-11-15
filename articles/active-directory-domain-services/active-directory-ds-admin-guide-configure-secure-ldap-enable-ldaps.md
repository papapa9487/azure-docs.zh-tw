---
title: "在 Azure AD Domain Services 中設定安全的 LDAP (LDAPS) | Microsoft Docs"
description: "針對 Azure AD 網域服務受管理網域設定安全的 LDAP (LDAPS)"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: maheshu
ms.openlocfilehash: d2ef65bb4dc8e12a18265ae8264def2bb32e191f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>針對 Azure AD 網域服務受管理網域設定安全的 LDAP (LDAPS)

## <a name="before-you-begin"></a>開始之前
確定您已完成[工作 2 - 將安全的 LDAP 憑證匯出到 .PFX 檔案](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)。


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>工作 3 - 使用 Azure 入口網站為受管理網域啟用安全 LDAP
若要啟用安全的 LDAP，請執行下列設定步驟：

1. 瀏覽至 **[Azure 入口網站](https://portal.azure.com)**。

2. 在 [搜尋資源] 搜尋方塊中搜尋「網域服務」。 從搜尋結果選取 [Azure AD Domain Services]。 [Azure AD Domain Services] 頁面會列出受管理網域。

    ![找出正在佈建的受管理的網域](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. 按一下受管理的網域名稱 (例如，'contoso100.com')，以查看網域的詳細資料。

    ![Domain Services - 佈建狀態](./media/getting-started/domain-services-provisioning-state.png)

3. 按一下導覽窗格中的 [安全 LDAP]。

    ![Domain Services - 安全 LDAP 頁面](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. 根據預設，已停用受管理的網域的安全 LDAP 存取。 將 [安全 LDAP] 切換至 [啟用]。

    ![啟用安全 LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. 根據預設，已停用透過網際網路之受管理的網域的安全 LDAP 存取。 視需要將 [在網際網路上允許安全 LDAP 存取] 切換為 [啟用]。 

    > [!WARNING]
    > 當您啟用透過網際網路的安全 LDAP 存取時，您的網域將容易遭受網際網路上的暴力密碼破解攻擊。 因此，建議您設定 NSG，以將存取權鎖定在必要的來源 IP 位址範圍。 請參閱指示，以[鎖定透過網際網路對於受管理網域的安全 LDAP 存取](#task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet)。
    >

6. 按一下 [.PFX 檔案]\(具備安全 LDAP 的憑證\) 後面的資料夾圖示。 指定具備憑證之 PFX 檔案的路徑，對受管理的網域進行安全 LDAP 存取。

7. 指定 [用以解密 PFX 檔案的密碼]。 提供將憑證匯出到 PFX 檔案時所使用的相同密碼。

8. 完成時，請按一下 [儲存] 按鈕。

9. 您會看到通知，通知您安全 LDAP 已針對受管理的網域進行設定。 這項作業完成之前，您無法修改網域的其他設定。

    ![為受管理的網域設定安全 LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> 為受管理的網域啟用安全 LDAP 需要約 10 到 15 分鐘的時間。 如果提供的安全 LDAP 憑證不符合所需的準則，則不會為您的目錄啟用安全 LDAP，而且您會看到失敗。 例如，網域名稱不正確、憑證已到期或即將到期等。 在此情況下，使用有效憑證重試。
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>工作 4 - 設定 DNS 以從網際網路存取受管理的網域
> [!NOTE]
> **選擇性工作** - 如果您不打算使用 LDAPS 來透過網際網路存取受管理的網域，請略過這項設定工作。
>
>

開始這項工作之前，請先確定您已完成 [工作 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview)中所述的步驟。

為受管理的網域啟用了透過網際網路的安全 LDAP 存取後，您需要更新 DNS 以便用戶端電腦可以找到此受管理網域。 在工作 3 的最後階段，[屬性] 索引標籤的 [可透過 LDAPS 存取的外部 IP 位址] 中會顯示外部 IP 位址。

請設定外部 DNS 提供者，讓受管理網域的 DNS 名稱 (例如 'ldaps.contoso100.com') 指向這個外部 IP 位址。 例如，建立下列 DNS 項目︰

    ldaps.contoso100.com  -> 52.165.38.113

這樣就大功告成了。您現在已準備好可使用安全 LDAP 透過網際網路連線到受管理網域。

> [!WARNING]
> 請記住，用戶端電腦必須信任 LDAPS 憑證的簽發者，才能成功使用 LDAPS 連線到受管理網域。 如果您使用公開的受信任憑證授權單位，您不需要採取任何動作，因為用戶端電腦會信任這些憑證簽發者。 如果您使用自我簽署憑證，在用戶端電腦的受信任憑證存放區中安裝自我簽署憑證的公開部分。
>
>


## <a name="task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>工作 5 - 鎖定透過網際網路對受管理網域進行的安全 LDAP 存取
> [!NOTE]
> 如果您尚未啟用透過網際網路對受管理網域進行的 LDAPS 存取，請略過這項設定工作。
>
>

開始這項工作之前，請先確定您已完成 [工作 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview)中所述的步驟。

公開受管理的網域透過網際網路進行 LDAPS 存取，代表著安全性威脅。 受管理的網域可以從用於安全 LDAP 之通訊埠的網際網路 (也就是連接埠 636) 存取。 因此，您可以選擇將受管理的網域存取限制為特定已知 IP 位址。 為了提升安全性，建立網路安全性群組 (NSG)，並將它與您已啟用 Azure AD Domain Services 的子網路產生關聯。

下表說明您可以設定的範例 NSG，以鎖定透過網際網路的安全 LDAP 存取。 NSG 包含一組規則，能夠只允許從一組指定的 IP 位址透過 TCP 連接埠 636 進行輸入的安全 LDAP 存取。 預設 'DenyAll' 規則適用於來自網際網路的所有其他輸入流量。 允許從指定的 IP 位址透過網際網路之 LDAPS 存取的 NSG 規則，其優先順序高於 DenyAll NSG 規則。

![透過網際網路之安全 LDAP 存取的範例 NSG](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**詳細資訊** - [網路安全性群組](../virtual-network/virtual-networks-nsg.md)。

<br>


## <a name="troubleshooting"></a>疑難排解
如果您無法使用安全 LDAP 來連線到受管理的網域，請執行下列疑難排解步驟：
* 確定安全 LDAP 憑證的簽發者鏈結在用戶端上受到信任。 您可以選擇將根憑證授權單位新增到用戶端上受信任的根憑證存放區，以建立信任。
* 確認安全 LDAP 憑證的簽發者不是全新 Windows 電腦上預設即不信任的中繼憑證受權單位。
* 確認 LDAP 用戶端 (例如 ldp.exe) 是使用 DNS 名稱來連線至安全 LDAP 端點，而不是使用 IP 位址。
* 確認 LDAP 用戶端所連線的 DNS 名稱會解析成受管理網域上安全 LDAP 的公用 IP 位址。
* 確認受管理網域之安全 LDAP 憑證的「主體」和「主體別名」屬性中有 DNS 名稱。

如果您仍然無法使用安全 LDAP 來連線至受管理網域，請[連絡產品小組](active-directory-ds-contact-us.md)以取得協助。 請包含下列資訊來協助進一步診斷問題：
* ldp.exe 進行連線及失敗時的螢幕擷取畫面。
* 您的 Azure AD 租用戶識別碼，以及受管理網域的 DNS 網域名稱。
* 您嘗試作為繫結身分的確切使用者名稱。


## <a name="related-content"></a>相關內容
* [Azure AD Domain Services - 入門指南](active-directory-ds-getting-started.md)
* [Administer an Azure AD Domain Services managed domain (管理 Azure AD 網域服務受管理的網域)](active-directory-ds-admin-guide-administer-domain.md)
* [管理 Azure AD Domain Services 受管理網域上的群組原則](active-directory-ds-admin-guide-administer-group-policy.md)
* [網路安全性群組](../virtual-network/virtual-networks-nsg.md)
* [建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
