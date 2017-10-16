---
title: "在組織中啟用 Microsoft Windows Hello 企業版 | Microsoft Docs"
description: "在組織中啟用 Microsoft Passport 的部署指示。"
services: active-directory
documentationcenter: 
keywords: "設定 Microsoft Passport、Microsoft Windows Hello 企業版部署"
author: MarkusVi
manager: femila
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.openlocfilehash: 58943e1e29755c983e55c675dd4fe7b75ac47b34
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>在組織中啟用 Microsoft Windows Hello 企業版
[連接已加入網域的 Windows 10 裝置與 Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md) 後，請依下列方式在組織中啟用 Microsoft Windows Hello 企業版。

1. 部署 System Center Configuration Manager  
2. 設定原則設定
3. 設定憑證設定檔  

## <a name="deploy-system-center-configuration-manager"></a>部署 System Center Configuration Manager
如果要根據 Windows Hello 企業版金鑰部署使用者憑證，您需要下列項目：

* **System Center Configuration Manager 最新分支** - 您必須安裝 1606 版或更新版本。 如需詳細資訊，請參閱 [System Center Configuration Manager 文件](https://technet.microsoft.com/library/mt346023.aspx)和 [System Center Configuration Manager 小組部落格](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)。
* **發佈金鑰基礎結構 (PKI)** - 若要利用使用者憑證啟用 Microsoft Windows Hello 企業版，您必須備妥 PKI。 如果您沒有帳戶，或您不想要用於使用者憑證，您可以部署已安裝 Windows Server 2016 組建 10551 (或更新版本) 的新網域控制站。 依照步驟在現有網域中[安裝複本網域控制站](https://technet.microsoft.com/library/jj574134.aspx)或[安裝新的 Active Directory 樹系 (如果您要建立新的環境)](https://technet.microsoft.com/library/jj574166)。 (ISO 可在 [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true) 下載。)

## <a name="configure-policy-settings"></a>設定原則設定
若要設定 Microsoft Windows Hello 企業版原則設定，您有兩個選項：

* Active Directory 中的群組原則 
* System Center Configuration Manager 

使用 Active Directory 中的群組原則是設定 Microsoft Windows Hello 企業版原則設定的建議方法。 

當您也使用它來部署憑證時，使用 System Center Configuration Manager 是偏好的方法。 此案例：

* 可確保與較新的部署案例的相容性
* 需要用戶端使用 Windows 10 版本 1607 或更新版本。

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>透過 Active Directory 中的群組原則設定 Microsoft Windows Hello 企業版
**步驟**：

1. 開啟 [伺服器管理員] 並瀏覽至 [工具]  >  [群組原則管理]。
2. 從 [群組原則管理]，瀏覽至與您想要啟用 [加入 Azure AD] 的網域相對應的網域節點。
3. 以滑鼠右鍵按一下 [群組原則物件]，選取 [新增]。 指定群組原則物件的名稱，例如「啟用 Windows Hello 企業版」。 按一下 [確定] 。
4. 以滑鼠右鍵按一下新的群組原則物件，然後選取 [編輯] 。
5. 瀏覽至 [電腦設定]  >  [原則]  >  [系統管理範本]  >  [Windows 元件]  >  [Windows Hello 企業版]。
6. 以滑鼠右鍵按一下 [啟用 Windows Hello 企業版]，然後選取 [編輯]。
7. 選取 已啟用 選項按鈕，然後按一下套用。 按一下 [確定] 。
8. 您現在可以將群組原則物件連結到您所選擇的位置。 若要對組織中所有加入網域的 Windows 10 裝置啟用此原則，請將群組原則連結到網域。 例如：
   * Active Directory 中將放置已加入網域的 Windows 10 電腦的特定組織單位 (OU)
   * 已加入網域且會向 Azure AD 自動註冊的 Windows 10 電腦所屬的特定安全性群組

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>使用 System Center Configuration Manager設定 Windows Hello 企業版
**步驟**：

1. 開啟 **System Center Configuration Manager**，然後瀏覽至 [資產與相容性] > [相容性設定] > [公司資源存取] > [Windows Hello 企業版設定檔]。
   
    ![設定 Windows Hello 企業版](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. 在頂端工具列中，按一下 [建立 Windows Hello 企業版設定檔] 。
   
    ![設定 Windows Hello 企業版](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. 在 [一般]  對話方塊上，執行下列步驟：
   
    ![設定 Windows Hello 企業版](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. 在 [名稱] 文字方塊中，輸入您的設定名稱 (例如：**My WHfB Profile**)。
   
    b. 按一下 [下一步] 。
4. 在 [支援的平台] 對話方塊中，選取將使用此 Windows Hello 企業版設定檔佈建的平台，然後按 [下一步]。
   
    ![設定 Windows Hello 企業版](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. 在 [設定]  頁面上，執行下列步驟：
   
    ![設定 Windows Hello 企業版](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. 對於 [設定 Windows Hello 企業版]，選取 [已啟用]。
   
    b. 對於 [使用信賴平台模組 (TPM)]，選取 [必要]。 
   
    c. 對於 [驗證方法]，選取 [憑證型]。
   
    d. 按一下 [下一步] 。
6. 在 [摘要] 對話方塊上，按 [下一步]。
7. 在 [完成] 對話方塊上，按一下 [關閉]。
8. 在頂端工具列中，按一下 [部署] 。
   
    ![設定 Windows Hello 企業版](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>設定憑證設定檔
如果您使用憑證型驗證進行內部部署驗證，您需要設定及部署憑證設定檔。 這項工作需要您在 System Center Configuration Manager 中設定 NDES 伺服器和憑證登錄點網站角色。 如需詳細資訊，請參閱 [Configuration Manager 中憑證設定檔的必要條件](https://technet.microsoft.com/library/dn261205.aspx)。

1. 開啟 **System Center Configuration Manager**，然後瀏覽至 [資產與相容性] > [相容性設定] > [公司資源存取] > [憑證設定檔]。
2. 選取一個含有智慧卡登入擴充金鑰使用方法 (EKU) 的範本。

在憑證設定檔的 [SCEP 註冊] 頁面上，您必須選擇 [安裝至 Passport for Work，否則便失敗] 做為 [金鑰儲存體提供者]。

## <a name="next-steps"></a>後續步驟
* [適合企業使用的 Windows 10：使用裝置工作的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [透過 Microsoft Passport 不需要密碼就能驗證身分識別](active-directory-azureadjoin-passport.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

