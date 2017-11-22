---
title: "在 Azure 虛擬機器上為內部部署 Active Directory 網域安裝複本網域控制站 | Microsoft Docs"
description: "如何在 Azure 虛擬網路中的 Azure 虛擬機器 (VM) 上為內部部署 Active Directory 網域安裝複本網域控制站。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 60839f93954bfe38f0346b235259f68e479b8a00
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>在 Azure 虛擬網路中安裝複本 Active Directory 網域控制台
本文探討如何在 Azure 虛擬網路中的 Azure 虛擬機器 (VM) 上為內部部署 Active Directory 網域安裝其他網域控制站 (DC) 以作為複本網域控制站。 您也可以[在 Azure 虛擬網路上安裝新的 Windows Server Active Directory 樹系](active-directory-new-forest-virtual-machine.md)。 如需在 Azure 虛擬網路上安裝 Active Directory Domain Services (AD DS) 的相關資訊，請參閱[在 Azure 虛擬機器上部署 Windows Server Active Directory 的方針](https://msdn.microsoft.com/library/azure/jj156090.aspx) (英文)。

## <a name="scenario-diagram"></a>案例圖表
在此案例中，外部使用者需要存取在加入網域的伺服器上執行的應用程式。 執行應用程式伺服器和複本 DC 的 VM 安裝在 Azure 虛擬網路中。 虛擬網路可透過 [ExpressRoute](../expressroute/expressroute-locations-providers.md) 來連接到內部部署網路，或者您可以使用[站對站 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) 連線，如下所示： 

![Active Directory 網域控制器 Azure vnet 複本][1]

應用程式伺服器和 DC 會部署在個別的雲端服務中來分散計算處理工作，以及部署在可用性設定組中來改善容錯功能。
網域控制站會使用 Active Directory 複寫功能，在彼此之間以及與內部部署網域控制站互相複寫。 不需要任何同步處理工具。

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>建立 Azure 虛擬網路的內部部署 Active Directory 站台
您可以在 Active Directory 中建立一個站台來代表對應虛擬網路的網路區域。 此站台有助於最佳化驗證、複寫及其他 DC 位置的作業。 下列步驟說明如何建立站台，如需詳細背景，請參閱 [加入新的站台](https://technet.microsoft.com/library/cc781496.aspx)。

1. 開啟 [Active Directory 站台及服務]：[伺服器管理員] > [工具] > [Active Directory 站台及服務]。
2. 建立站台來代表您建立 Azure 虛擬網路的區域：按一下 [站台] > [動作] > [新增站台] 輸入新站台的名稱 (例如「Azure 美國西部」) > 選取站台連結 > [確定]。
3. 建立子網路並與新站台關聯：按兩下 [站台] > 在 [子網路] 上按一下滑鼠右鍵 > [新增子網路] > 輸入虛擬網路的 IP 位址範圍 (例如案例圖表中的 10.1.0.0/16) > 選取新的 Azure 站台 > [確定]。

## <a name="create-an-azure-virtual-network"></a>建立 Azure 虛擬網路
若要建立 Azure 虛擬網路並設定站對站 VPN，請依照[建立站對站連線](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)文章中的步驟進行。 

您也可以設定虛擬網路閘道器來建立安全的站台對站台 VPN 連線。 在新的虛擬網路與內部部署 VPN 裝置之間建立站台對站台 VPN 連線。 如需指示，請參閱[設定虛擬網路閘道](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md)。

## <a name="create-azure-vms-for-the-dc-roles"></a>建立 DC 角色的 Azure VM
若要建立裝載 DC 角色的虛擬機器，請視需要重複進行[使用 Azure 入口網站建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)中的步驟。 至少部署兩部虛擬網域控制站以提供容錯和冗餘。 如果 Azure 虛擬網路至少有兩部設定類似的網域控制站，您可以將執行這些網域控制站的虛擬機器放置在可用性設定組中，以提高容錯能力。

若要使用 Windows PowerShell 而非 Azure 入口網站建立虛擬機器，請參閱[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

為將執行 DC 角色的 VM 保留靜態 IP 位址。 若要保留靜態 IP 位址，請下載 Microsoft Web Platform Installer， [安裝 Azure PowerShell](/powershell/azure/overview) 並執行 Set-AzureStaticVNetIP Cmdlet。 例如：

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
如需設定靜態 IP 位址的詳細資訊，請參閱[設定 VM 的靜態內部 IP 位址](../virtual-network/virtual-networks-reserved-private-ip.md)。

## <a name="install-ad-ds-on-azure-vms"></a>在 Azure VM 上安裝 AD DS
登入 VM，並確認您具備整個站台對站台 VPN 的連線能力，或是對內部部署網路上的資源具備 ExpressRoute 連線功能。 然後在 Azure VM 上安裝 AD DS。 您可以使用您用來在內部部署網路上安裝其他網域控制站的相同程序 (UI、Windows PowerShell 或回應檔案)。 當您安裝 AD DS 時，請務必指定新磁碟區的 AD 資料庫、記錄檔和 SYSVOL 的位置。 如果您需要複習一下 AD DS 安裝，請參閱[安裝 Active Directory Domain Services (等級 100)](https://technet.microsoft.com/library/hh472162.aspx) 或[在現有網域中安裝複本 Windows Server 2012 網域控制站 (等級 200)](https://technet.microsoft.com/library/jj574134.aspx)。

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>重新設定虛擬網路的 DNS 伺服器
1. 若要取得虛擬網路名稱清單，請在 [Azure 入口網站](https://portal.azure.com)中，搜尋*虛擬網路*，然後選取 [虛擬網路] 以檢視清單。 
2. 開啟您想要管理的虛擬網路，然後[重新設定虛擬網路的 DNS 伺服器 IP 位址](../virtual-network/virtual-network-manage-network.md#dns-servers)，以使用指派給複本 DC 的靜態 IP 位址，而不是內部部署 DNS 伺服器的 IP 位址。
3. 確保虛擬網路上所有複本 DC 虛擬機器都設定為使用虛擬網路上的 DNS 伺服器：
  1. 選取 [虛擬機器]。
  2. 選取虛擬機器，然後選取 [重新啟動]。 
  3. 等到虛擬機器再次**執行**，再登入該機器。

## <a name="create-vms-for-application-servers"></a>建立應用程式伺服器的 VM

若要建立裝載應用程式伺服器角色的虛擬機器，請視需要重複進行[使用 Azure 入口網站建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)中的步驟。 若要使用 Microsoft PowerShell 而非 Azure 入口網站建立虛擬機器，請參閱[使用 Azure PowerShell 建立和設定以 Windows 為基礎的虛擬機器](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 下表包含建議的設定。

| 設定 | 值 |
| --- | --- |
|  **選擇映像** | Windows Server 2012 R2 Datacenter |
|  **虛擬機器組態** |<p>虛擬機器名稱：輸入單一標籤名稱 (例如 AppServer1)。</p><p>新的使用者名稱：輸入使用者的名稱。 此使用者將會是 VM 上本機 Administrators 群組的成員。 第一次登入 VM 時，您將需要此名稱。 內建的系統管理員帳戶會無法運作。</p><p>新密碼/確認：輸入密碼</p> |
|  **虛擬機器組態** |<p>雲端服務：第一個 VM 選擇 [建立新的雲端服務]，然後在您建立更多裝載應用程式的 VM 時，選取該相同的雲端服務名稱。</p><p>雲端服務 DNS 名稱：指定全域唯一名稱</p><p>區域/同質群組/虛擬網路：指定虛擬網路名稱 (例如 WestUSVNet)。</p><p>儲存體帳戶：選擇 [使用自動產生的儲存體帳戶]，然後在您建立更多裝載應用程式的 VM 時，選取該相同的儲存體帳戶名稱。</p><p>可用性設定組：選擇 [建立可用性設定組]。</p><p>可用性設定組名稱：請在您建立第一個 VM 時輸入可用性設定組的名稱，然後在您建立更多 VM 時選擇該相同名稱。</p> |
|  **虛擬機器組態** |<p>選取 [安裝 VM 代理程式]<b></b> 以及您所需的任何其他延伸模組。</p> |
  
佈建每個 VM 之後，登入並將 VM 加入網域。 
1. 在 [伺服器管理員] &gt;[本機伺服器] &gt;[工作群組] &gt;[變更...] 中，選取 [網域]。
2. 輸入您的內部部署網域名稱。 
3. 提供網域使用者的認證。
4. 重新啟動 VM。

## <a name="additional-resources"></a>其他資源

* 如需有關使用 Windows PowerShell 的詳細資訊，請參閱[開始使用 Azure Cmdlet](/powershell/azure/overview) 和 [Azure Cmdlet 參考](/powershell/azure/get-started-azureps)。
* [在 Azure 虛擬機器上部署 Windows Server Active Directory 的指導方針](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [如何使用 Azure PowerShell 將現有的內部部署 Hyper-V 網域控制站上傳到 Azure](http://support.microsoft.com/kb/2904015)
* [在 Azure 虛擬網路上安裝新的 Active Directory 樹系](active-directory-new-forest-virtual-machine.md)
* [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IT Pro IaaS：(01) 虛擬機器基本概念](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS：(05) 建立虛擬網路和跨單位連線](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure 管理 Cmdlet](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
