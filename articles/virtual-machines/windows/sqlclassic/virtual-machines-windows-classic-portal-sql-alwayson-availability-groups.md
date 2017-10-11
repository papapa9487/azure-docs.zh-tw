---
title: "設定 Azure 虛擬機器中的 Always On 可用性群組 (傳統) | Microsoft Docs"
description: "使用 Azure 虛擬機器建立 Always On 可用性群組。 本教學課程主要是透過使用者介面作業，而非編寫指令碼。"
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 8d48a3d2-79f8-4ab0-9327-2f30ee0b2ff1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: b360fe9f28eeb9b10c82fce729165b1b572ac3c6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-virtual-machines-classic"></a>設定 Azure 虛擬機器中的 Always On 可用性群組 (傳統)
> [!div class="op_single_selector"]
> * [傳統：UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [傳統：PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

請在開始之前仔細思考一下，您現在已經可以在 Azure Resource Manager 模型中完成這項工作。 我們建議新的部署採用 Azure Resource Manager 模型。 請參閱 [Azure 虛擬機器上的 SQL Server Always On 可用性群組](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md)。

> [!IMPORTANT] 
> Microsoft 建議讓大部分的新部署使用資源管理員模式。 Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文說明如何使用傳統部署模型。 

若要使用 Azure Resource Manager 模型來完成這項工作，請參閱 [Azure 虛擬機器上的 SQL Server Always On 可用性群組](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md)。

本端對端教學課程將示範如何透過在 Azure 虛擬機器上執行的 SQL Server Always On 實作可用性群組。

在本教學課程結束時，您 Azure 中的 SQL Server Always On 解決方案將包含下列項目：

* 包含多個子網路並包括前端和後端子網路的虛擬網路。
* 具有 Active Directory (Azure AD) 網域的網域控制站
* 執行 SQL Server 的兩部虛擬機器已部署至後端子網路並加入 Azure AD 網域
* 具有節點多數仲裁模型的 3 節點容錯移轉叢集
* 具有兩份可用性資料庫同步認可複本的可用性群組

下圖將解決方案以圖形呈現。

![Azure 中可用性群組的測試實驗室架構](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

請注意以下這是一個可用的組態。 例如，您可以盡量減少兩個複本的可用性群組的虛擬機器數目。 此設定使用網域控制器作為兩個節點叢集中的仲裁檔案共用見證，以節省 Azure 中的計算時數。 這個方法可讓設定圖例中虛擬機器數目減少一個。

本教學課程假設您已句備下列條件：

* 您已經有 Azure 帳戶。
* 您已經知道如何在虛擬機器資源庫中使用 GUI，以佈建執行 SQL Server 的傳統虛擬機器。
* 您已非常熟悉 Always On 可用性群組的功能。 如需詳細資訊，請參閱 [Always On 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。

> [!NOTE]
> 如想將 Always On 可用性群組與 SharePoint 搭配使用，另請參閱[為 SharePoint 2013 設定 SQL Server 2012 Always On 可用性群組](https://technet.microsoft.com/library/jj715261.aspx)。
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>建立虛擬網路和網域控制站伺服器
一開始先建立新的 Azure 試用帳戶。 設定您的帳戶後，系統會將您導向 Azure 傳統入口網站的主畫面。

1. 按一下頁面左下角的 [新增]  按鈕，如下列螢幕擷取畫面所示。
   
    ![在入口網站中按一下 [新增]](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. 依序按一下 [新增]  >  [網絡服務]  >  [虛擬網路]  [自訂建立]，如下列螢幕擷取畫面所示。
   
    ![建立虛擬網路](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. 在 [建立虛擬網路]  對話方塊中，透過套用下表設定以通過精靈中的頁面，來建立新的虛擬網路。 
   
   | Page | 設定 |
   | --- | --- |
   | 虛擬網路詳細資料 |**名稱 = ContosoNET**<br/>**地區 = 美國西部** |
   | DNS 伺服器和 VPN 連線能力 |None |
   | 虛擬網路位址空間 |設定如下列螢幕擷取畫面所示： ![建立虛擬網路](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. 建立要做為網域控制站 (DC) 的虛擬機器。 依序按一下 [新增] >  [計算]  >  [虛擬機器]  >  [從資源庫] ，如下列螢幕擷取畫面所示。
   
    ![建立虛擬機器](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. 在 [建立虛擬網路]  對話方塊中，透過套用下表設定以通過精靈中的頁面，來設定新的虛擬機器。 
   
   | Page | 設定 |
   | --- | --- |
   | 選取虛擬機器作業系統 |Windows Server 2012 R2 Datacenter |
   | 虛擬機器組態 |**版本發行日期** = (最新版本)<br/>**虛擬機器名稱** = ContosoDC<br/>**層** = 標準<br/>**大小** = A2 (2 核心)<br/>**新使用者名稱** = AzureAdmin<br/>**新密碼** = Contoso!000<br/>**確認** = Contoso!000 |
   | 虛擬機器組態 |**雲端服務** = 建立新的雲端服務<br/>**雲端服務 DNS 名稱** = 唯一的雲端服務名稱<br/>**DNS 名稱** = 唯一的名稱 (例如：ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = Back(10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = (無) |
   | 虛擬機器選項 |使用預設值 |

您設定新的虛擬機器之後，請等候虛擬機器佈建完成。 這個程序需要一些時間才能完成。 此程序需要一些時間才能完成，如果您按一下 Azure 傳統入口網站中的 [虛擬機器] 索引標籤，您會看到 ContosoDC 的循環狀態從 [啟動中 (佈建中)] 變成 [停止]、[啟動中]、[執行中 (佈建中)]，最後 [執行中]。

現在已成功佈建 DC 伺服器。 接下來，您將在這個 DC 伺服器上設定 Active Directory 網域。

## <a name="configure-the-domain-controller"></a>設定網域控制站
下列步驟將帶您把 ContosoDC 電腦設定為 corp.contoso.com 的網域控制站。

1. 在入口網站中，選取 [ContosoDC]  電腦。 在 [儀表板]  索引標籤上按一下 [連接] ，開啟遠端桌面存取的遠端桌面 (RDP) 檔案。
   
    ![連接至虛擬機器](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. 以所設定的系統管理員帳戶 (**\AzureAdmin**) 和密碼 (**Contoso!000**) 登入。
3. 根據預設，應會顯示 [伺服器管理員]  儀表板。
4. 按一下儀表板上的 [新增角色與功能]  連結。
   
    ![透過 [伺服器總管] 新增角色](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. 按一下 [下一步] ，直到進入 [伺服器角色]  區段。
6. 選取 [Active Directory Domain Services] 和 [DNS 伺服器] 角色。 出現提示時，新增這些角色需要的更多功能。
   
   > [!NOTE]
   > 螢幕會顯示無靜態 IP 位址的驗證警告。 如果您是要測試組態，請按一下 [繼續] 。 實際操作時，請 [使用 PowerShell 設定網域控制站電腦的靜態 IP 位址](../../../virtual-network/virtual-networks-reserved-private-ip.md)。
   > 
   > 
   
    ![新增角色對話方塊](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. 連續按 [下一步] 直到到達 [確認] 區段。 選取 [必要時自動重新啟動目的地伺服器] 核取方塊。
8. 按一下 [Install] 。
9. 功能安裝完畢後，請回到 [伺服器管理員]  儀表板。
10. 在左側窗格中選取新的 [AD DS]  選項。
11. 按一下黃色警告列上的 [更多]  連結。
    
     ![DNS 伺服器虛擬機器上的 AS DS 對話方塊](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. 在 [所有伺服器工作詳細資料]  對話方塊的 [動作]  欄中，按一下 [將此伺服器升級為網域控制站]。
13. 在 **Active Directory 網域服務組態精靈**中，使用下列值：
    
    | Page | 設定 |
    | --- | --- |
    | 部署組態 |**新增樹系** = 已選取<br/>**根網域名稱** = corp.contoso.com |
    | 網域控制站選項 |**密碼** = Contoso!000<br/>**確認密碼** = Contoso!000 |
14. 按 [下一步]，以通過精靈中的其他頁面。 在 [檢查先決條件] 頁面上，確認是否出現下列訊息：**已順利通過所有先決條件檢查**。 請注意，務必要檢閱所有適用您情況的警告訊息，但是仍可以繼續進行安裝。
15. 按一下 [Install] 。 **ContosoDC** 虛擬機器便會自動重新開機。

## <a name="configure-domain-accounts"></a>設定網域帳戶
接下來的步驟，將帶您設定 Active Directory 帳戶，以供之後使用。

1. 重新登入 **ContosoDC** 電腦。
2. 在 [伺服器管理員]  中，按一下 [工具]  >  [Active Directory 管理中心] 。
   
    ![Active Directory 管理中心](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. 在 [Active Directory 管理中心] 中，選取左窗格中的 [企業 (本機)]。
4. 在右 [工作]  窗格中，按一下 [新增]  >  [使用者] 。 套用下列設定：
   
   | 設定 | 值 |
   | --- | --- |
   | **名字** |Install |
   | **使用者 SamAccountName** |Install |
   | **密碼** |Contoso! 000 |
   | **確認密碼** |Contoso! 000 |
   | **其他密碼選項** |已選取 |
   | **密碼永久有效** |已檢查 |
5. 按一下 [確定]，以建立**安裝**使用者。 此帳戶將用來設定容錯移轉叢集和可用性群組。
6. 透過相同的步驟建立兩個額外的使用者：**CORP\SQLSvc1** 和 **CORP\SQLSvc2**。 這些帳戶將用於 SQL Server 執行個體。 接著，您需要為 **CORP\Install** 提供必要的權限，以設定 Windows 容錯移轉叢集。
7. 在 [Active Directory 管理中心]  中，選取左窗格中的 [企業 (本機)] 。 在 [工作]  窗格中，按一下 [屬性] 。
   
    ![企業使用者內容](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. 選取 [延伸模組]，然後按一下 [安全性] 索引標籤上的 [進階] 按鈕 。
9. 在 [企業的進階安全性設定]  對話方塊上，按一下 [新增] 。
10. 按一下 [選取主體] 、搜尋 [CORP\Install] ，然後按一下 [確定] 。
11. 選取 [讀取所有內容] 和 [建立電腦物件] 權限。
    
     ![企業使用者權限](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. 按一下 [確定]，再按一下 [確定]。 關閉 [企業內容] 視窗。

現在 Active Directory 和使用者物件已設定完畢，請建立三個 SQL Server 虛擬機器，並將這些虛擬機器加入此網域。

## <a name="create-the-sql-server-virtual-machines"></a>建立 SQL Server 虛擬機器
建立三個虛擬機器 一個節點用於叢集節點，兩個節點用於 SQL Server。 為了逐一建立每個虛擬機器，請回到 Azure 傳統入口網站，依序按一下 [新增]  >  [計算]  >  [虛擬機器]  >  [從資源庫] 。 然後使用下表中的範本建立虛擬機器。

| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| 選取虛擬機器作業系統 |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 RTM Enterprise** |**SQL Server 2014 RTM Enterprise** |
| 虛擬機器組態 |**版本發行日期** = (最新版本)<br/>**虛擬機器名稱** = ContosoWSFCNode<br/>**層** = 標準<br/>**大小** = A2 (2 核心)<br/>**新使用者名稱** = AzureAdmin<br/>**新密碼** = Contoso!000<br/>**確認** = Contoso!000 |**版本發行日期** = (最新版本)<br/>**虛擬機器名稱** = ContosoSQL1<br/>**層** = 標準<br/>**大小** = A3 (4 核心)<br/>**新使用者名稱** = AzureAdmin<br/>**新密碼** = Contoso!000<br/>**確認** = Contoso!000 |**版本發行日期** = (最新版本)<br/>**虛擬機器名稱** = ContosoSQL2<br/>**層** = 標準<br/>**大小** = A3 (4 核心)<br/>**新使用者名稱** = AzureAdmin<br/>**新密碼** = Contoso!000<br/>**確認** = Contoso!000 |
| 虛擬機器組態 |**雲端服務** = 先前建立的唯一雲端服務 DNS 名稱 (例如︰ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = Back(10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = 建立可用性設定組<br/>**可用性設定組名稱** = SQLHADR |**雲端服務** = 先前建立的唯一雲端服務 DNS 名稱 (例如︰ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = Back(10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = SQLHADR (也可以在虛擬機器建立後，再設定可用性設定組。 三部虛擬機器都必須指派至 SQLHADR 可用性集合)。 |**雲端服務** = 先前建立的唯一雲端服務 DNS 名稱 (例如︰ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = Back(10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = SQLHADR (也可以在虛擬機器建立後，再設定可用性設定組。 三部虛擬機器都必須指派至 SQLHADR 可用性集合)。 |
| 虛擬機器選項 |使用預設值 |使用預設值 |使用預設值 |

<br/>

> [!NOTE]
> 先前的設定建議標準層虛擬機器，因為基本層機器不支援負載平衡端點。 您稍後才需要負載平衡端點，以建立可用性群組接聽程式。 此外，此處建議的機器大小是為了在 Azure 虛擬機器中測試可用性群組。 為獲得生產工作負載的最佳效能，請參閱 [Azure 虛擬機器中的 SQL Server 效能最佳作法](../sql/virtual-machines-windows-sql-performance.md)中關於 SQL Server 機器大小和組態的建議。
> 
> 

完整佈建三個虛擬機器後，您必須將它們加入 **corp.contoso.com** 網域，並將 CORP\Install 管理權限授與給這些虛擬機器。 若要這樣做，請針對每個虛擬機器執行下列步驟。

1. 首先，變更慣用的 DNS 伺服器位址。 在清單中選取虛擬機器，並按一下 [連接]  按鈕，將每個虛擬機器的 RDP 檔案下載至您的本機目錄。 若要選取虛擬機器，按一下任何一處 (資料列中的第一個資料格除外)，如下列螢幕擷取畫面所示。
   
    ![下載 RDP 檔案](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. 開啟您所下載的 RDP 檔案，並使用所設定的系統管理員帳戶 (**BUILTIN\AzureAdmin**) 和密碼 (**Contoso!000**) 登入虛擬機器。
3. 登入後，會顯示 [伺服器管理員]  儀表板。 按一下左窗格中的 [本機伺服器]  。
4. 按一下 [DHCP 指派的 IPv4 位址，支援 IPv6]  連結。
5. 在 [網路連接]  對話方塊中，按一下網路圖示。
   
    ![變更虛擬機器的慣用 DNS 伺服器](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. 在命令列中，按一下 [變更此連線的設定]。 (視您的視窗大小而定，可能需按一下雙向右箭頭才能看到此命令)。
7. 選取 [網際網路通訊協定第 4 版 (TCP/IPv4)] ，然後按一下 [內容] 。
8. 選取 [使用下列的 DNS 伺服器位址] ，然後指定 [慣用 DNS 伺服器]  中的 [10.10.2.4] 。
9. **10.10.2.4** 位址是指派給 Azure 虛擬網路的 10.10.2.0/24 子網路中的虛擬機器的位址。 虛擬機器是 **ContosoDC**。 若要確認 **ContosoDC** 的 IP 位址，請如下列螢幕擷取畫面所示，在命令提示字元視窗中加入 **nslookup contosodc**。
   
    ![使用 NSLOOKUP 尋找網域控制站的 IP 位址](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. 依序按一下 [確定]  >  [關閉]  以認可變更。 您現在可以將虛擬機器加入 **corp.contoso.com**。
11. 回到 [本機伺服器] 視窗中，按一下 [工作群組] 連結。
12. 在 [電腦名稱] 區段中，按一下 [變更]。
13. 選取 [網域]  核取方塊、在文字方塊中輸入 **corp.contoso.com**，然後按一下 [確定] 。
14. 在 [Windows 安全性]  對話方塊中，指定預設網域的系統管理員帳戶 (**CORP\AzureAdmin**) 和密碼 (**Contoso!000**) 的認證。
15. 出現「歡迎使用 corp.contoso.com 網域」的訊息時，請按一下 [確定] 。
16. 依序按一下對話方塊中的 [關閉]  >  [立即重新啟動] 。

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-virtual-machine"></a>將 Corp\Install 使用者新增為每個虛擬機器的系統管理員
1. 等到虛擬機器重新啟動時，再次開啟 RDP 檔案，以使用 **BUILTIN\AzureAdmin** 帳戶登入虛擬機器。
2. 在**伺服器管理員**按一下**工具** > **電腦管理**。
   
    ![電腦管理](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. 在 [電腦管理 ] 對話方塊中，展開 [本機使用者和群組] ，然後按一下 [群組] 。
4. 按兩下 [系統管理員]  群組。
5. 在 [系統管理員內容]  對話方塊中，按一下 [新增]  按鈕。
6. 輸入 **CORP\Install** 使用者，然後按一下 [確定]。 當系統提示您輸入認證時，請輸入 **AzureAdmin** 帳戶和 **Contoso!000** 密碼。
7. 按一下 [確定] ，以關閉 [系統管理員內容]  對話方塊。

### <a name="add-the-failover-clustering-feature-to-each-virtual-machine"></a>將容錯移轉叢集功能新增至每部虛擬機器
1. 在 [伺服器管理員] 儀表板中按一下 [新增角色及功能]。
2. 在**新增角色及功能精靈**中，連續按 [下一步] 直到到達 [功能] 頁面。
3. 選取 [容錯移轉叢集] 。 出現提示時，請新增其他相依功能。
   
    ![將容錯移轉叢集功能新增至虛擬機器](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. 按 [下一步]，然後按一下 [確認] 頁面上的 [安裝]。
5. **容錯移轉叢集** 功能安裝完畢後，按一下 [關閉] 。
6. 登出虛擬機器。
7. 針對三個伺服器：**ContosoWSFCNode**、**ContosoSQL1**，和 **ContosoSQL2** 重複本節中的步驟。

SQL Server 虛擬機器現已佈建完成和執行，但每部虛擬機器都採用 SQL Server 的預設選項。

## <a name="create-the-failover-cluster"></a>建立容錯移轉叢集
在本節中，您將會建立容錯移轉叢集，該叢集將裝載您稍後要建立的可用性群組。 到目前為止，您應該已針對將用於容錯移轉叢集的三個虛擬機器中的每一個，完成下列作業：

* 虛擬機器已在 Azure 中佈建完畢
* 將虛擬機器加入網域
* 將 **CORP\Install** 新增至本機系統管理員群組
* 新增容錯移轉叢集功能

以上都是將虛擬機器加入容錯移轉叢集之前，每個虛擬機器需完成的必要條件。

此外，請注意 Azure 虛擬網路的運作方式和其在內部部署網路中的運作方式不同。 您必須依照下列順序建立叢集：

1. 在一個節點上 (**ContosoSQL1**) 建立單一節點叢集。
2. 將叢集的 IP 位址修改成未使用的 IP 位址 (**10.10.2.101**)。
3. 讓叢集名稱上線。
4. 新增其他節點 (**ContosoSQL2** 和 **ContosoWSFCNode**)。

依照下列步驟來完成完整設定叢集的工作。

1. 開啟 **ContosoSQL1** 的 RDP 檔案，並使用網域帳戶 **CORP\Install** 登入。
2. 在 [伺服器管理員]  儀表板中，依序按一下 [工具]  >  [容錯移轉叢集管理員] 。
3. 如下列螢幕擷取畫面所示，在左窗格中，以滑鼠右鍵按一下 [容錯移轉叢集管理員] **Failover Cluster Manager**，再按一下 [建立叢集] 。
   
    ![建立叢集](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. 在「建立叢集精靈」中，以下表中的設定逐步完成每個頁面來建立單節點叢集：
   
   | Page | 設定 |
   | --- | --- |
   | 開始之前 |使用預設值 |
   | 選取伺服器 |在 [輸入伺服器名稱] 中輸入 **ContosoSQL1**，然後按一下 [新增] |
   | 驗證警告 |選取 [**否。我不需要 Microsoft 提供此叢集的支援，也不需要執行驗證測試。請在我按一下 [下一步] 後，繼續建立叢集**]。 |
   | 用於管理叢集的存取點 |在 [叢集名稱] 中輸入 **Cluster1** |
   | 確認 |除非您使用的是儲存空間，否則請使用預設值。 請詳閱此表之後的警告。 |
   
   > [!WARNING]
   > 如果您使用的是會將多個磁碟組成存放集區的 [儲存空間](https://technet.microsoft.com/library/hh831739)，您就必須在 [確認]  頁面上清除 [新增適合的儲存裝置到叢集]  核取方塊。 如果不清除此選項，在群集程序進行期間虛擬磁碟會中斷連結。 因此，虛擬磁碟不會顯示在 [磁碟管理員] 或 [Explorer] 中，直到您將儲存空間從叢集中移除，並使用 PowerShell 重新連接虛擬磁碟。
   > 
   > 
5. 在左窗格中，展開 [容錯移轉叢集管理員] ，然後按一下 [Cluster1.corp.contoso.com] 。
6. 在中央窗格中向下捲動至 [叢集核心資源]  區段，並展開 [名稱：Clutser1]  的詳細資料。 在 [失敗] 狀態中，應該會同時出現 [名稱] 和 [IP 位址] 資源 。 由於指派給叢集的 IP 位址與虛擬機器的 IP 位址相同，是個重複的位址，因此無法讓該 IP 位址資源上線。
7. 以滑鼠右鍵按一下失敗的 [IP 位址] 資源，然後按一下 [內容]。
   
    ![叢集屬性](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. 選取 [靜態 IP 位址]，並在 [位址]  文字方塊中指定 [10.10.2.101] ，然後按一下 [確定] 。
9. 在 [叢集核心資源] 區段中，以滑鼠右鍵按一下 [名稱：Cluster1]，再按一下 [上線]。 等待兩個資源上線。 叢集名稱資源上線後，會以新的 Active Directory 電腦帳戶更新 DC 伺服器。 此 Active Directory 帳戶稍後將用來執行可用性群組叢集服務。
10. 將剩餘的節點新增至叢集。 如下列螢幕擷取畫面所示，在瀏覽器樹狀目錄中以滑鼠右鍵按一下 [Cluster1.corp.contoso.com]，再按一下 [新增節點]。
    
     ![將節點新增至叢集](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. 在 [新增節點精靈]  中，按一下 [選取伺服器]  頁面上的 [下一步] ，然後在 [輸入伺服器名稱]  中輸入伺服器名稱，再按一下 [新增] ，將 **ContosoSQL2** 和 **ContosoWSFCNode** 節點新增至清單。 完成之後，按 [下一步]。
12. 在 [驗證警告]  頁面上，按一下 [否] ，雖然在實際操作時，您應執行驗證測試。 然後按 [下一步] 。
13. 在 [確認]  頁面中按 [下一步] ，以新增節點。
    
    > [!WARNING]
    > 如果您使用的是會將多個磁碟組成存放集區的[儲存空間](https://technet.microsoft.com/library/hh831739)，您就必須清除 [新增適合的儲存裝置到叢集]  核取方塊。 如果不清除此選項，在群集程序進行期間虛擬磁碟會中斷連結。 因此，虛擬磁碟不會顯示在 [磁碟管理員] 或 [總管] 中，直到您將儲存空間從叢集中移除，並使用 PowerShell 重新連接虛擬磁碟。
    > 
    > 
14. 將節點新增至叢集後，請按一下 [完成] 。 容錯移轉叢集管理員現在應該會顯示您的叢集具有三個節點，並將這些節點列在**節點**容器中。
15. 登出遠端桌面工作階段。

## <a name="prepare-the-sql-server-instances-for-availability-groups"></a>針對可用性群組準備 SQL Server 執行個體
本節將針對 **ContosoSQL1** 和 **contosoSQL2** 進行下列工作：

* 為具有預設 SQL Server 執行個體必要權限的 **NT AUTHORITY\System** 新增登入
* 將 **CORP\Install** 新增為預設 SQL Server 執行個體的系統管理員角色
* 開啟防火牆以供 SQL Server 進行遠端存取。
* 啟用 Always On 可用性群組功能。
* 將 SQL Server 服務帳戶分別變更為 **CORP\SQLSvc1** 和 **CORP\SQLSvc2**。

執行這些動作沒有順序限制。 不過，下列步驟將依順序執行這些動作。 針對 **ContosoSQL1** 和 **ContosoSQL2**，依照下列步驟執行：

1. 如果您尚未將虛擬機器登出遠端桌面工作階段，請現在登出。
2. 開啟 **ContosoSQL1** 和 **ContosoSQL2** 的 RDP 檔案，並以 **BUILTIN\AzureAdmin** 的身分登入。
3. 將 **NT AUTHORITY\System** 和必要權限新增至 SQL Server 登入。 開啟 **SQL Server Management Studio**。
4. 按一下 [連接]  ，連接至預設 SQL Server 執行個體。
5. 在 [物件總管] 中，依序展開 [安全性]、[登入]。
6. 以滑鼠右鍵按一下 [NT AUTHORITY\System]  登入，然後按一下 [內容]。
7. 在 [安全性實體]  頁面中，對本機伺服器，針對下列權限選取 [授與] ，然後按一下 [確定] 。
   
   * 更改所有可用性群組
   * 連接 SQL
   * 檢視伺服器狀態
8. 將 **CORP\Install** 新增為預設 SQL Server 執行個體的 **系統管理員** 角色 在 [物件總管]  中，以滑鼠右鍵按一下 [登入] ，再按一下 [新增登入] 。
9. 在 [登入名稱] 中輸入 **CORP\Install**。
10. 在 [伺服器角色] 頁面上，按一下 [sysadmin]，然後按一下 [確定]。  建立登入之後，展開 [物件總管]  中的 [登入]  便可看到該登入。
11. 若要建立 SQL Server 的防火牆規則，請在 [開始]  畫面上，開啟 [具有進階安全性的 Windows 防火牆] 。
12. 在左側窗格中，選取 [內送規則] 。 在右窗格中，按一下 [新增規則] 。
13. 在 [規則類型]  頁面上，按一下 [程式]  >  [下一步] 。
14. 在 [程式]  頁面上，選取[此程式路徑] 、在文字方塊中輸入 **%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe**，然後按一下 [下一步] 。 如果正依照這些指示進行，但使用 SQL Server 2012，則 SQL Server 目錄是**MSSQL11.MSSQLSERVER**。
15. 在 [動作] 頁面中，保持選取 [允許連接]，然後按 [下一步]。
16. 在 [設定檔]  頁面上，接受預設設定，然後按一下 [下一步] 。
17. 在 [名稱]  頁面上，指定規則名稱，例如在 [名稱]  文字方塊中指定 [SQL Server (程式規則)] ，然後按一下 [完成] 。
18. 若要啟用 **Always On 可用性群組** 功能，請在 [開始]  畫面上，開啟 [SQL Server 組態管理員] 。
19. 在瀏覽器樹狀目錄中按一下 [SQL Server 服務] ，然後以滑鼠右鍵按一下 [SQL Server (MSSQLSERVER)]  服務，再按一下 [內容] 。
20. 如下列螢幕擷取畫面所示，按一下 [AlwaysOn 高可用性]  索引標籤，然後選取 [啟用 AlwaysOn 可用性群組]，再按一下 [套用] 。 按一下對話方塊中的 [確定] ，先不要關閉 [內容]  對話方塊。 變更服務帳戶之後，將重新啟動 SQL Server 服務。
    
     ![啟用 Always On 可用性群組](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. 若要變更 SQL Server 服務帳戶，按一下 [登入]  索引標籤，在 [帳戶名稱]  中輸入 **CORP\SQLSvc1** (若為 **ContosoSQL1**)，或 **CORP\SQLSvc2** (若為 **ContosoSQL2**)，填入密碼並加以確認，然後按一下 [確定]。
22. 在對話方塊中按一下 [是]  ，重新啟動 SQL Server 服務。 SQL Server 服務重新啟動之後，您在 [內容]  對話方塊中所進行的變更便會生效。
23. 登出虛擬機器。

## <a name="create-the-availability-group"></a>建立可用性群組
現在已準備就緒，可以開始設定可用性群組。 以下列出需執行的動作：

* 在 **ContosoSQL1** 上建立新的資料庫 (**MyDB1**)。
* 建立資料庫的完整備份和交易記錄備份。
* 透過 [NORECOVERY]  選項將完整備份和記錄備份還原至 **ContosoSQL2**。
* 藉由同步認可、自動容錯移轉及可讀取的次要複本，建立「可用性群組」(**AG1**)。

### <a name="create-the-mydb1-database-on-contososql1"></a>在 ContosoSQL1 上建立 MyDB1 資料庫
1. 如果您有您尚未將 **ContosoSQL1** 和 **ContosoSQL2** 登出遠端桌面工作階段，請現在登出。
2. 開啟 **ContosoSQL1** 的 RDP 檔案，並以 **CORP\Install** 的身分登入。
3. 在 [檔案總管]  中，於 **\\C:** 磁碟中，建立名為 **備份** 的目錄。 此目錄將用來備份和還原您的資料庫。
4. 如下方所示，以滑鼠右鍵按一下新目錄，指向 [共用對象]，然後按一下 [特定人員]。
   
    ![建立備份資料夾](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. 新增 **CORP\SQLSvc1**，然後指定 **讀取/寫入** 權限。 如下列螢幕擷取畫面所示，新增 **CORP\SQLSvc2**，然後指定 **讀取** 權限，再按一下 [共用] 。 檔案共用程序完成後，按一下 [完成] 。
   
    ![將權限授與備份資料夾](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. 若要建立資料庫，請透過 [開始]  功能表，開啟 **SQL Server Management Studio**，然後按一下 [連接] ，連接至預設的 SQL Server 執行個體。
7. 在 [物件總管] 中，以滑鼠右鍵按一下 [資料庫] ，然後按一下 [新增資料庫] 。
8. 在 [資料庫名稱]  中，輸入 **MyDB1**，然後按一下 [確定] 。

### <a name="make-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>建立 MyDB1 的完整備份，然後將其還原至 ContosoSQL2
1. 若要建立完整的資料庫備份，請在 [物件總管]  中，展開 [資料庫] ，以滑鼠右鍵按一下 [MyDB1] ，指向 [工作] ，然後按一下 [備份] 。
2. 在 [來源] 區段中，讓 [備份類型] 的設定保持為 [完整]。 在 [目的地] 區段中按一下 [移除]，移除備份檔案的預設檔案路徑。
3. 在 [目的地] 區段中，按一下 [新增]。
4. 在 [檔案名稱]  文字方塊中，輸入 **\\ContosoSQL1\backup\MyDB1.bak**，按一下 [確定] ，然後再按一下 [確定]  來備份資料庫。 備份作業完成後，再次按一下 [確定]  ，關閉對話方塊。
5. 若要建立資料庫的交易記錄備份，請在 [物件總管]  中，展開 [資料庫] ，以滑鼠右鍵按一下 [MyDB1] ，指向 [工作] ，然後按一下 [備份] 。
6. 在 [備份類型]  中，選取 [交易記錄] 。 讓 [目的地]  的檔案路徑設定保持為您稍早指定的路徑，然後按一下 [確定] 。 備份作業完成後，再次按一下 [確定] 。
7. 若要還原 **ContosoSQL2** 的完整和交易記錄備份，請開啟 **ContosoSQL2** 的RDP 檔案，並以 **CORP\Install** 的身分登入。 讓 **ContosoSQL1** 的遠端桌面工作階段保持開啟。
8. 透過 [開始]  功能表，啟動 **SQL Server Management Studio**，然後按一下 [連接] ，連接至預設的 SQL Server 執行個體。
9. 在 [物件總管]  中，以滑鼠右鍵按一下 [資料庫] ，再按一下 [還原資料庫] 。
10. 在 [來源]  區段中，選取 [裝置] ，然後按一下 […]  省略符號 按鈕。
11. 在 [選取備份裝置] 中按一下 [新增]。
12. 在 [備份檔案位置]  中，輸入 **\\ContosoSQL1\backup**，按一下 [重新整理] **Refresh**，選取 **MyDB1.bak**，按一下 [確定] ，然後再按一次 [確定] **OK**。 在 [要還原的備份組]  窗格中，現在應會顯示完整備份和記錄備份。
13. 移至 [選項]  頁面中，在 [復原狀態]  中選取 [使用 NORECOVERY 還原]，然後按一下 [確定] ，以還原資料庫。 還原作業完成後，按一下 [確定] 。

### <a name="create-the-availability-group"></a>建立可用性群組
1. 回到 **ContosoSQL1**的遠端桌面工作階段。 如下列螢幕擷取畫面所示，在 SQL Server Management Studio 的 [物件總管]  中，以滑鼠右鍵按一下 [AlwaysOn 高可用性] ，然後按一下 [新增可用性群組精靈] 。
   
    ![啟動新增可用性群組精靈](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. 在 [簡介]  頁面上，按一下 [下一步] 。 在 [指定可用性群組名稱]  頁面上，於 [可用性群組名稱]  中輸入 [AG1] ，然後再次按 [下一步] 。
   
    ![新增可用性群組精靈，指定可用性群組名稱](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. 在 [選取資料庫]  頁面上，選取 [MyDB1] ，然後按 [下一步] 。 由於您已經為預定的主要複本建立了至少一份完整備份，所以現在這些資料庫已符合建立可用性群組的先決條件。
   
    ![新增可用性群組精靈：選取資料庫](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. 在 [指定複本]  頁面上，按一下 [新增複本] 。
   
    ![新增可用性群組精靈：選取複本](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. 在 [連接到伺服器]  對話方塊至中，在 [伺服器名稱]  中輸入 **ContosoSQL2**，然後按一下 [連接] 。
   
    ![新增可用性群組精靈：連接到伺服器](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. 回到 [指定複本]  頁面，現在 [可用複本]  中應會列出 **ContosoSQL2**。 如下列螢幕擷取畫面所示，設定複本。 完成後，請按 [下一步] 。
   
    ![新增可用性群組精靈：選取複本 (完整)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. 在 [選取初始資料同步處理]  頁面上，按一下 [僅聯結] ，然後按 [下一步] 。 您在建立 **ContosoSQL1** 的完整備份和交易備份，並將備份還原至 **ContosoSQL2** 時，便已手動執行資料同步處理。 您可以改為選擇不在資料庫上執行備份與還原作業，並選取 [完整]  讓新可用性群組精靈為您執行資料同步處理。 不過，我們不建議針對某些企業中的超大型資料庫採用這種選項。
   
    ![新增可用性群組精靈：選取初始資料同步處理](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. 在 [驗證]  頁面中按 [下一步] 。 此頁面應該會看起來如下列螢幕擷取畫面所示： 由於您尚未設定可用性群組接聽程式，所以出現了關於接聽程式組態的警告。 您可以忽略此警告，因為本教學課程不會示範設定接聽程式的步驟。 完成此教學課程之後，若要設定接聽程式，請參閱 [設定 Azure 中 AlwaysOn 可用性群組的 ILB 接聽程式](../classic/ps-sql-int-listener.md)。
   
    ![新增可用性群組精靈：驗證](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. 在 [摘要]  頁面中，按一下 [完成] ，然後等候精靈設定新的「可用性群組」。 在 [進度]  頁面中，按一下 [詳細資料]  可檢視詳細的進度。 精靈完成後，如下列螢幕擷取畫面所示，檢查 [結果]  頁面，確認是否已成功建立可用性群組，然後按一下 [關閉]  以結束精靈。
   
    ![新增可用性群組精靈：結果](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. 在 [物件總管]  中，展開 [AlwaysOn 高可用性] ，再展開 [可用性群組] 。 新的可用性群組應會顯示在此容器中。 以滑鼠右鍵按一下 [AG1 (主要)] ，再按一下 [顯示儀表板] 。
    
     ![顯示可用性群組儀表板](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. [AlwaysOn 儀表板]  的外觀應類似於下列螢幕擷取畫面。 當中會顯示複本、每個複本的容錯移轉模式和同步處理狀態。
    
     ![可用性群組儀表板](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. 回到 [伺服器管理員] ，選取 [工具] ，然後啟動 [容錯移轉叢集管理員] 。
13. 展開 [Cluster1.corp.contoso.com]，然後展開 [服務和應用程式]。 選取 [角色]。請注意，先前已建立了 **AG1** 的可用性群組角色。 請注意，由於您未設定接聽程式，所以 AG1 不具備任何資料庫用戶端可用來連接至可用性群組的 IP 位址。 直接連接至主要節點，可進行讀寫作業，連接至次要節點，則可進行唯讀查詢。
    
     ![容錯移轉叢集管理員中的 AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> 請勿嘗試透過容錯移轉叢集管理員，容錯移轉可用性群組。 所有容錯移轉作業都應在 SQL Server Management Studio 的 [AlwaysOn 儀表板]  中執行。 如需詳細資訊，請參閱[容錯移轉叢集管理員與可用性群組一起使用的限制](https://msdn.microsoft.com/library/ff929171.aspx)。
> 
> 

## <a name="next-steps"></a>後續步驟
現在，您已透過在 Azure 中建立可用性群組的方式，成功實作 SQL Server Always On。 若要為此可用性群組設定接聽程式，請參閱 [設定 Azure 中 AlwaysOn 可用性群組的 ILB 接聽程式](../classic/ps-sql-int-listener.md)。

如需在 Azure 中使用 SQL Server 的其他資訊，請參閱 [Azure 虛擬機器上的 SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)。

