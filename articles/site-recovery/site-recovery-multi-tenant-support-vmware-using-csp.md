---
title: "VMware VM 複寫至 Azure (CSP 計劃) 的多租用戶支援 | Microsoft Docs"
description: "描述如何使用 Azure 入口網站在多租用戶環境中部署 Azure Site Recovery，以透過 CSP 計畫協調內部部署 VMware 虛擬機器 (VM) 至 Azure 的複寫、容錯移轉和復原"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 9db7e276fbbc064abe16cab2d2df668d2b1c8f7d
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Azure Site Recovery 中的多租用戶支援，適用於透過 CSP 將 VMware 虛擬機器複寫到 Azure

Azure Site Recovery 支援租用戶訂用帳戶的多租用戶環境。 對於透過 Microsoft Cloud 解決方案提供者 (CSP) 方案建立及管理的租用戶訂用帳戶，它也支援多租用戶。 本文中詳述實作及管理多租用戶 VMware 到 Azure 案例的指南。 其中也涵蓋透過 CSP 建立及管理租用戶訂用帳戶。

本指南與複寫 VMware 虛擬機器到 Azure 的現有文件密切相關。 如需詳細資訊，請參閱[使用 Site Recovery 將 VMware 虛擬機器複寫至 Azure](site-recovery-vmware-to-azure.md)。

## <a name="multi-tenant-environments"></a>多租用戶環境
多租用戶模型主要有三種：

* **共用主機服務提供者 (HSP)**：合作夥伴擁有實體基礎結構，而使用者則共用資源 (如 vCenter、資料中心、實體儲存體等) 以將多租用戶的虛擬機器裝載在同一個基礎結構。 合作夥伴可提供災害復原管理做為受管理的服務，租用戶也可以擁有災害復原做為自助服務方案。

* **專用主機服務提供者**：合作夥伴擁有實體基礎結構，但使用專用資源 (如多個 vCenters、實體資料存放區等) 在個別的基礎結構上裝載每個租用戶的虛擬機器。 合作夥伴可提供災害復原管理做為受管理的服務，租用戶也可以擁有災害復原做為自助服務方案。

* **受管理的服務提供者 (MSP)** – 客戶擁有裝載虛擬機器的實體基礎結構，而合作夥伴則提供災害復原支援及管理。

## <a name="shared-hosting-multi-tenant-guidance"></a>共用主機多租用戶指南
本節詳述共用主機案例。 另外兩個案例是共用主機案例的子集，且使用相同的原則。 共用主機指南最後面會詳述其中的差異。

多租用戶案例的基本需求是隔離不同的租用戶。 一個租用戶無法觀察另一個租用戶裝載的內容。 這個需求在自助管理環境中，比在合作夥伴管理的環境中來得重要，甚至有重大影響。 本指南假設租用戶隔離是必要條件。

下圖說明這個架構：

![含一個 vCenter 的共用 HSP](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**含一個 vCenter 的共用主機案例**

如上圖所示，每個客戶都有個別的管理伺服器。 這個設定可以將租用戶限制於只能存取租用戶特定虛擬機器，以達到租用戶隔離的目的。 VMware 虛擬機器複寫案例會使用組態伺服器來管理帳戶以探索虛擬機器及安裝代理程式。 我們會針對多租用戶環境遵循相同的原則，但是另外透過 vCenter 存取控制來限制 VM 探索。

資料隔離的需求會使得基礎結構機密資訊 (如存取認證) 對租用戶保持公開。 基於這個理由，建議所有管理伺服器的元件都維持在合作夥伴的獨佔控制之下。 管理伺服器元件包括：
* 組態伺服器 (CS)
* 處理序伺服器 (PS)
* 主要目標伺服器 (MT)

向外延展 PS 也是在合作夥伴的控制之下。

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>多租用戶案例中的每個 CS 都使用兩個帳戶

- **vCenter 存取帳戶**：此帳戶可用來探索租用戶虛擬機器。 帳戶有獲得指派的 vCenter 存取權限 (如下一節所述)。 為了避免意外的存取權限洩漏，建議夥伴自行在設定工具中輸入這些認證。

- **虛擬機器存取帳戶**：此帳戶是用來在租用戶上，透過自動推送安裝行動代理程式。 這通常是租用戶可能會提供給合作夥伴的網域帳戶，或可能由合作夥伴直接管理的網域帳戶。 如果租用戶不要直接與合作夥伴分享詳細資料，租用戶可以透過限時的 CS 存取權來輸入認證，也可以在合作夥伴的協助下手動安裝行動代理程式。

### <a name="requirements-for-a-vcenter-access-account"></a>vCenter 存取帳戶的需求

如前一節所述，您必須使用獲指派特殊角色的帳戶設定 CS。 針對每個 vCenter 物件，必須將角色指派套用於 vCenter 存取帳戶，而且不要傳播到子物件。 此設定可以確保租用戶隔離，因為存取傳播可能會導致意外存取其他物件。

![傳播到子物件選項](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

替代方案是在資料中心物件指派使用者帳戶和角色，並將這些傳播到子物件。 然後將每個物件 (例如其他租用戶的虛擬機器) 的*無存取*角色授與應該無法存取特定租用戶的帳戶。 這個設定相當繁瑣，而且會意外公開存取控制，因為會對每個新的子物件自動授與從父物件繼承的存取權。 因此建議採用第一種方法。

vCenter 帳戶存取程序如下：

1. 藉由複製預先定義的「唯讀」角色來建立新的角色，並以易記名稱 (例如此範例中顯示的 Azure_Site_Recovery) 命名。

2. 將下列權限指派給這個角色：

    * **資料存放區**：配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案

    * **網路**：網路指派

    * **資源**：指派虛擬機器至資源集區、移轉已關閉電源的虛擬機器、移轉已開啟電源的虛擬機器

    * **工作**：建立工作、更新工作

    * **虛擬機器**：
        * 組態 > 全部
        * 互動 > 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝
        * 清查 > 從現有建立、建立新的、註冊、取消註冊
        * 佈建 > 允許虛擬機器下載、允許虛擬機器檔案上傳
        * 快照集管理 > 移除快照集

    ![[編輯角色] 對話方塊](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. 針對不同物件，指派存取層級給 vCenter 帳戶 (在租用戶 CS 中使用)：

>| Object | 角色 | 備註 |
>| --- | --- | --- |
>| vCenter | 唯讀 | 只用以允許 vCenter 存取以管理其他物件。 如果帳戶不會提供給租用戶，或用於任何 vCenter 上的管理作業，則可以移除此權限。 |
>| 資料中心 | Azure_Site_Recovery |  |
>| 主機和主機叢集 | Azure_Site_Recovery | 再次確認存取權在物件層級，以限制只有可存取的主機在容錯移轉前和容錯回復後有租用戶虛擬機器。 |
>| 資料存放區和資料存放區叢集 | Azure_Site_Recovery | 同上。 |
>| 網路 | Azure_Site_Recovery |  |
>| 管理伺服器 | Azure_Site_Recovery | 如果有任何元件在 CS 機器以外，包括所有元件 (CS、PS 及 MT) 的存取權。 |
>| 租用戶 VM | Azure_Site_Recovery | 確保特定租用戶的任何新租用戶虛擬機器也會有此存取權，否則將無法透過 Azure 入口網站探索它們。 |

現在已經完成 vCenter 帳戶存取權。 這個步驟可以滿足完成容錯回復作業的最小權限需求。 也可以搭配現有的原則使用這些存取權限。 只要將現有的權限集修改成包含上面步驟 2 詳述的角色權限即可。

若要限制災害復原作業僅進行到容錯移轉狀態，而沒有容錯回復的功能，請遵循上述程序，但是不要指派 *Azure_Site_Recovery*給 vCenter 存取帳戶，而是改為只指派*唯讀*角色。 這個權限集合會允許虛擬機器複寫和容錯移轉，而不允許容錯回復。 以上程序的其餘部分都保持原狀。 為了確保租用戶隔離及限制虛擬機器探索，所有權限仍僅指派到物件層級而未傳播到子物件。

## <a name="other-multi-tenant-environments"></a>其他多租用戶環境

上節描述如何針對共用主機解決方案設定多租用戶環境。 另外兩個主要解決方案是專用主機和受管理的服務。 下列各節將說明這些解決方案的架構。

### <a name="dedicated-hosting-solution"></a>專用主機解決方案

如下圖所示，專用主機方案的架構差異是每個租用戶的基礎結構完全針對該租用戶設定。 因為租用戶是透過個別的 vCenter 隔離，所以主機提供者仍必須按照針對共用主機提供的 CSP 步驟來執行，但不需要擔心租用戶隔離。 CSP 安裝程式會保持不變。

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**含多個 vCenters 的專用主機案例**

### <a name="managed-service-solution"></a>受管理的服務解決方案

如下圖所示，受管理的服務方案本身的架構差異是每個租用戶的基礎結構在實體上與其他租用戶的基礎結構分開。 此案例通常是在租用戶擁有基礎結構時存在，而且需要解決方案提供者來管理災害復原。 同樣地，因為租用戶在實體上透過不同的基礎結構來隔離，所以合作夥伴仍需要按照針對共用主機提供的 CSP 步驟來執行，但不需要擔心租用戶隔離。 CSP 佈建保持不變。

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  
**含多個 vCenters 的受管理的服務案例**

## <a name="csp-program-overview"></a>CSP 計畫概觀
[CSP 計畫](https://partner.microsoft.com/en-US/cloud-solution-provider)致力於和合作夥伴合作提供所有 Microsoft 雲端服務 (包括 Office 365、Enterprise Mobility Suite 和 Microsoft Azure) 以達到雙贏。 藉由 CSP，我們的合作夥伴全面掌握與客戶的端對端關係，並且成為主要的關係連絡窗口。 合作夥伴可以為客戶部署 Azure 訂用帳戶，並將這些訂用帳戶與他們自己的加值自訂優惠結合。

藉由 Azure Site Recovery，合作夥伴可以直接透過 CSP 管理客戶的完整災害復原解決方案。 或者，也可以使用 CSP 設定 Site Recovery 環境，並讓客戶藉由自助服務的方式管理自己的災害復原需求。 在這兩種情況下，合作夥伴是 Site Recovery 與客戶之間的連絡窗口。 合作夥伴負責維護客戶關係，以及向客戶收取 Site Recovery 使用量的費用。

## <a name="create-and-manage-tenant-accounts"></a>建立及管理租用戶帳戶

### <a name="step-0-prerequisite-check"></a>步驟 0：必要條件檢查

虛擬機器的必要條件和 [Azure Site Recovery 文件](site-recovery-vmware-to-azure.md)中所述相同。 除了這些必要條件之外，也應該要先設定好上述存取控制，才能透過 CSP 進行租用戶管理。 針對每個租用戶建立可以和租用戶虛擬機器及合作夥伴的 vCenter 通訊的個別「管理伺服器」。 只有合作夥伴有此伺服器的存取權限。

### <a name="step-1-create-a-tenant-account"></a>步驟 1︰建立租用戶帳戶

1. 透過 [Microsoft 合作夥伴中心](https://partnercenter.microsoft.com/) 登入 CSP 帳戶。

2. 在 [儀表板] 功能表上，選取 [客戶]。

    ![Microsoft 夥伴中心客戶連結](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

3. 在開啟的頁面上，按一下 [新增客戶] 按鈕。

    ![[新增客戶] 按鈕](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

4. 在 [新客戶] 頁面上，填入租用戶的所有帳戶資訊詳細資料，然後按一下 [下一步: 訂閱]。

    ![[帳戶資訊] 頁面](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

5. 在訂閱選取頁面上，勾選 [Microsoft Azure] 核取方塊。 您可以立即或在其他任何時候新增其他訂用帳戶。

    ![[Microsoft Azure 訂用帳戶] 核取方塊](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

6. 在 [檢閱] 頁面上，確認租用戶詳細資料，然後按一下 [提交]。

    ![[檢閱] 頁面](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)  

    您建立租用戶帳戶後，確認網頁隨即出現，顯示預設帳戶的詳細資料和該訂用帳戶的密碼。

7. 儲存資訊並在稍後視需要透過 Azure 入口網站登入網頁來變更密碼。  

    您可以與租用戶共用這些既有的資訊，也可以視需要建立和共用不同的帳戶。

### <a name="step-2-access-the-tenant-account"></a>步驟 2︰存取租用戶帳戶

如「步驟 1：建立租用戶帳戶」所述，您可以透過 Microsoft 夥伴中心儀表板存取租用戶的訂用帳戶。

1. 移至 [客戶] 頁面，然後按一下租用戶帳戶的名稱。

2. 在租用戶帳戶的 [訂用帳戶] 頁面上，您可以監視現有帳戶的訂用帳戶，並且視需要新增更多訂用帳戶。 若要管理租用戶的災復原作業，請選取 [所有資源 (Azure 入口網站)]。

    ![[所有資源] 連結](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)  

    按一下 [所有資源] 將授與租用戶的 Azure 訂用帳戶存取權限。 您可以按一下 Azure 入口網站右上方的 [Azure Active Directory] 連結，以確認存取權限。

    ![[Azure Active Directory] 連結](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

您現在可以透過 Azure 入口網站為租用戶執行所有 Site Recovery 作業，以及管理災害復原作業。 若要透過 CSP 存取租用戶訂用帳戶來進行受管理的災害復原，請遵循先前所述的程序。

### <a name="step-3-deploy-resources-to-the-tenant-subscription"></a>步驟 3：將資源部署到租用戶訂用帳戶
1. 在 Azure 入口網站上，建立「資源群組」，然後對於每個一般程序部署「復原服務」保存庫。

2. 下載保存庫註冊金鑰。

3. 使用保存庫註冊金鑰為租用戶註冊 CS。

4. 輸入兩個存取帳戶 (vCenter 存取帳戶和虛擬機器存取帳戶) 的認證。

    ![管理員設定伺服器帳戶](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>步驟 4：向復原服務保存庫註冊 Site Recovery 基礎結構
1. 在 Azure 入口網站中，對於稍早建立的保存庫，將 vCenter 伺服器註冊到您在「步驟 3：將資源部署到租用戶訂用帳戶」中註冊的 CS。 將 vCenter 存取帳戶用於此目的。
2. 針對每個一般程序的 Site Recovery 完成「準備基礎結構」程序。
3. 現在可以開始複寫 VM。 確認 [複寫] 選項下的 [選取虛擬機器] 刀鋒視窗僅顯示租用戶的虛擬機器。

    ![[選取虛擬機器] 刀鋒視窗上的租用戶虛擬機器清單](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-the-subscription"></a>步驟 5︰將對訂用帳戶的存取權指派給租用戶

對於自助災害復原，向租用戶提供帳戶詳細資料，如「步驟 1：建立租用戶帳戶」一節的步驟 6 所述。 合作夥伴設定災害復原基礎結構之後，請執行此動作。 無論災害復原類型是否為受管理或自助，合作夥伴都必須透過 CSP 入口網站存取租用戶訂用帳戶。 合作夥伴可設定合作夥伴擁有的保存庫，並將基礎結構註冊到租用戶訂用帳戶。

合作夥伴也可以透過 CSP 入口網站將新的使用者加入租用戶訂用帳戶，方法如下：

1. 移至租用戶的 CSP 訂用帳戶頁面，然後選取 [使用者與授權] 選項。

    ![租用戶的 CSP 訂用帳戶頁面](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    您現在可以輸入相關詳細資料並選取權限，也可以使用 CSV 檔案上傳使用者的清單，以便建立新的使用者。

2. 建立新的使用者之後，返回 Azure 入口網站，並在 [訂用帳戶] 刀鋒視窗選取相關訂用帳戶。

3. 在開啟的刀鋒視窗上選取 [存取控制 (IAM)]，然後按一下 [新增] 以使用相關的存取層級新增使用者。      
    在按一下存取層級之後開啟的刀鋒視窗上，會自動顯示透過 CSP 入口網站建立的使用者。

    ![新增使用者](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    *參與者*角色就足以執行大多數的管理作業。 除了變更存取層級 (必須具有「擁有者」存取層級) 之外，具有此存取層級的使用者可以在訂用帳戶上執行任何作業。 您也可以視需要微調存取層級。
