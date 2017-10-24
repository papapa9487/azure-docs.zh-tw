---
title: "Azure Active Directory Domain Services：開始使用 | Microsoft Docs"
description: "使用 Azure 入口網站啟用 Azure Active Directory Domain Services"
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
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: d86b9e99619dea408e784f32d76ba6296948bdc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>使用 Azure 入口網站啟用 Azure Active Directory Domain Services
本文說明如何使用 Azure 入口網站啟用 Azure Active Directory Domain Services (Azure AD DS)。

若要啟動 [啟用 Azure AD Domain Services] 精靈，請完成下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 在左窗格中，按一下 [新增]。
3. 在 [新增] 頁面中，將「網域服務」輸入到搜尋列。

    ![搜尋網域服務](./media/getting-started/search-domain-services.png)

4. 按一下以從搜尋建議清單中選取 [Azure AD Domain Services]。 在 [Azure AD Domain Services] 頁面中，按一下 [建立] 按鈕。

    ![網域服務檢視](./media/getting-started/domain-services-blade.png)

5. [啟用 Azure AD Domain Services] 精靈隨即啟動。


## <a name="task-1-configure-basic-settings"></a>工作 1：設定基本設定
在精靈的 [基本] 分頁中，您可以指定受管理的網域的 DNS 網域名稱。 您也可以選擇應該部署受管理的網域的資源群組和 Azure 位置。

![設定基本](./media/getting-started/domain-services-blade-basics.png)

1. 為受管理的網域選擇 [DNS 網域名稱]。

   * 根據預設，將會指定目錄的預設網域名稱 (具有 **.onmicrosoft.com** 尾碼)。

   * 您也可以輸入自訂網域名稱。 在此範例中，自訂網域名稱是 contoso100.com。

     > [!WARNING]
     > 指定網域名稱的前置詞 (例如，contoso100.com 網域名稱中的 contoso100) 必須包含 15 個以內的字元。 您無法使用超過 15 個字元的前置詞來建立受管理的網域。
     >
     >

2. 確定虛擬網路中還沒有您為受管理網域選擇的 DNS 網域名稱。 具體來說，請檢查是否有下列情況︰

   * 您在虛擬網路上已有包含相同 DNS 網域名稱的網域。

   * 您打算啟用受管理的網域的虛擬網路具有與內部部署網路的 VPN 連線。 在此案例中，確定您在內部部署網路上沒有使用相同 DNS 網域名稱的網域。

   * 您在虛擬網路上已有具有該名稱的雲端服務。

3. 選擇 [虛擬網路的類型]。 根據預設，會選取 **Resource Manager** 虛擬網路類型。 建議所有新建立的受管理網域使用這種類型的虛擬網路。

    > [!TIP]
    > **傳統的虛擬網路支援已排定要淘汰。** 為所有新部署選取 Resource Manager 虛擬網路類型。 新部署將很快不再支援傳統虛擬網路。 將繼續支援在傳統虛擬網路中部署的現有受管理網域。
    >

4. 選取您要在其中建立受管理的網域的 Azure **訂用帳戶**。

5. 選取受管理的網域應該隸屬的**資源群組**。 您可以選擇**新建**或**使用現有**選項，以選取資源群組。

6. 選擇應該在其中建立受管理的網域的 Azure**位置**。 在精靈的 [網路] 分頁上，您只會看到屬於您所選取之位置的虛擬網路。

7. 完成時，按一下 [確定] 以繼續前往精靈的 [網路] 分頁。


## <a name="next-step"></a>後續步驟
[工作 2：設定網路基本設定](active-directory-ds-getting-started-network.md)
