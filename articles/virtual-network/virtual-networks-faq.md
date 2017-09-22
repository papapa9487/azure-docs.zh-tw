---
title: "Azure 虛擬網路常見問題 | Microsoft Docs"
description: "Microsoft Azure 虛擬網路的常見問題解答。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/18/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 9520e4c2680e1dd0158a35b4ee65496a42a02563
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Azure 虛擬網路的常見問題 (FAQ)

## <a name="virtual-network-basics"></a>虛擬網路基本概念

### <a name="what-is-an-azure-virtual-network-vnet"></a>什麼是 Azure 虛擬網路 (VNet)？
Azure 虛擬網路 (VNet) 是您的網路在雲端中的身分。 它是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 您可以使用 VNet 在 Azure 中佈建和管理虛擬私人網路 (VPN)，也可以選擇性地連結 VNet 與其他 Azure 中的 VNet，或連結您的內部部署 IT 基礎結構，以便建立混合式或跨單位的解決方案。 您建立的每個 VNet 皆具有自己的 CIDR 區塊，且只要 CIDR 區塊沒有重疊，就可以連結至其他 VNet 和內部部署網路。 針對 VNet 和分割的 VNet 子網路，您也擁有 DNS 伺服器設定的控制項。

您可以使用 VNet：

* 建立專用的僅限私人雲端 VNet 您並非每次都需要為解決方案取得跨單位組態。 當您建立 VNet 時，您的服務和 VNet 中的 VM 可以直接且安全地在雲端中彼此通訊。 這可確保 VNet 中流量的安全性，但仍可讓您設定 VM 的端點連接，以及需要網際網路通訊作為解決方案一部分的服務。
* 安全地擴充資料中心 您可以使用 VNet 建置傳統的站台對站台 (S2S) VPN，安全地擴充資料中心容量。 S2S VPN 使用 IPSEC 在您的公司 VPN 閘道與 Azure 之間提供安全連接。
* 啟用混合式雲端案例 VNet 可讓您彈性地支援許多混合式雲端案例。 您可以將雲端型應用程式安全地連接到任何類型的內部部署系統，例如大型主機和 Unix 系統。

### <a name="how-do-i-know-if-i-need-a-vnet"></a>如何得知是否需要 VNet？
[虛擬網路概觀](virtual-networks-overview.md)一文內附有決策資料表，可協助您決定最佳網路設計選項。

### <a name="how-do-i-get-started"></a>如何開始使用？
請造訪[虛擬網路文件](https://docs.microsoft.com/azure/virtual-network/)以開始使用。 本內容提供所有 VNet 功能的概觀和部署資訊。

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>我可以使用不包含跨單位連線的 VNet 嗎？
是。 您可以使用不包含混合式連線能力的 VNet。 如果您想要在 Azure 中執行 Microsoft Windows Server Active Directory 網域控制站和 SharePoint 伺服器陣列，這會特別有用。

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>我可以在一或多個 VNet 與我的內部部署資料中心之間執行 WAN 最佳化嗎？

是。 您可以透過 Azure Marketplace，部署來自多個廠商的 [WAN 最佳化網路的虛擬應用裝置](https://azure.microsoft.com/marketplace/?term=wan+optimization)。

## <a name="configuration"></a>組態

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>我可以使用哪些工具來建立 VNet？
您可以使用下列工具來建立或設定 VNet：

* Azure 入口網站 (適用於傳統與資源管理員 VNet)。
* 網路組態檔 (netcfg - 僅適用於傳統 VNet)。 請參閱[使用網路組態檔設定 VNet](virtual-networks-using-network-configuration-file.md)一文。
* PowerShell (適用於傳統與資源管理員 VNet)。
* Azure CLI (適用於傳統與資源管理員 VNet)。

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>我可以在 VNet 中使用哪些位址範圍？
[RFC 1918](http://tools.ietf.org/html/rfc1918) 中定義的任何 IP 位址範圍。 例如：10.0.0.0/16。

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>我可以在 VNet 擁有公用 IP 位址嗎？
是。 如需有關公用 IP 位址範圍的詳細資訊，請參閱[虛擬網路中的公用 IP 位址空間](virtual-networks-public-ip-within-vnet.md)一文。 您無法直接從網際網路存取公用 IP 位址。

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>我的 VNet 中的子網路數目是否有限制？
是。 請參閱 [Azure 限制](../azure-subscription-service-limits.md#networking-limits)一文以取得詳細資料。 子網路位址空間不能互相重疊。

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>在這些子網路內使用 IP 位址是否有任何限制？
是。 Azure 會在每個子網路中保留一些 IP 位址。 子網路的第一個和最後一個 IP 位址會保留給相容的通訊協定，以及用於 Azure 服務的額外 3 個位址。

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>VNet 和子網路的大小限制為何？
我們支援的最小子網路是 /29，最大則是 /8 (使用 CIDR 子網路定義)。

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>我可以使用 VNet 將 VLAN 帶到 Azure 嗎？
否。 VNet 是 Layer-3 重疊。 Azure 不支援任何 Layer-2 語意。

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>可以在我的 Vnet 和子網路上指定自訂路由原則嗎？
是。 您可以使用「使用者定義的路由」(UDR)。 如需有關 UDR 的詳細資訊，請造訪 [使用者定義的路由和 IP 轉送](virtual-networks-udr-overview.md)。

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNet 是否支援多點傳送或廣播？
否。 我們不支援多點傳送或廣播。

### <a name="what-protocols-can-i-use-within-vnets"></a>我可以在 VNet 中使用哪些通訊協定？
您可以在 VNet 中使用 TCP、UDP 和 ICMP TCP/IP 通訊協定。 多點傳送、廣播、IP-in-IP 封裝式封包和 Generic Routing Encapsulation (GRE) 封包在 VNet 內會遭到封鎖。 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>我可以在 VNet 中偵測我的預設路由器嗎？
否。

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>我可以使用 tracert 來診斷連線嗎？
否。

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>我可以在建立 VNet 之後新增子網路嗎？
是。 子網路隨時可以加入至 VNet，前提是子網路位址不是 VNet 中另一個子網路的一部分。

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>我可以在建立子網路之後修改其大小嗎？
是。 如果其中沒有部署任何 VM 或服務，您可以新增、移除、展開或縮小子網路。

### <a name="can-i-modify-subnets-after-i-created-them"></a>我可以在建立子網路之後進行修改嗎？
是。 您可以加入、移除和修改 VNet 所使用的 CIDR 區塊。

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>如果我在 VNet 中執行服務，我可以連線到網際網路嗎？
是。 部署在 VNet 中的所有服務皆可連線到網際網路。 Azure 中部署的每個雲端服務皆已指派公開可定址的 VIP。 您必須定義 PaaS 角色的輸入端點和虛擬機器的端點，啟用這些服務以接受來自網際網路的連接。

### <a name="do-vnets-support-ipv6"></a>VNet 是否支援 IPv6？
否。 目前您無法使用 IPv6 搭配 VNet。

### <a name="can-a-vnet-span-regions"></a>VNet 可以跨區域嗎？
否。 VNet 僅限於單一區域。

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>我可以將 VNet 連線到 Azure 中的另一個 VNet 嗎？
是。 您可以使用下列項目，將一個 VNet 連接到另一個 VNet︰
- Azure VPN 閘道。 請參閱[設定 VNet 對 VNet 連線](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)一文以取得詳細資料。 
- VNet 對等互連。 請參閱 [VNet 對等互連概觀](virtual-network-peering-overview.md)一文以取得詳細資料。

## <a name="name-resolution-dns"></a>名稱解析 (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>適用於 VNet 的 DNS 選項為何？
使用 [ [VM 和角色執行個體的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md) ] 頁面上的決策資料表來引導您完成所有可用的 DNS 選項。

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>我可以指定適用於 VNet 的 DNS 伺服器嗎？
是。 您可以在 VNet 設定中指定 DNS 伺服器 IP 位址。 這將會針對 VNet 中的所有 VM 套用為預設 DNS 伺服器。

### <a name="how-many-dns-servers-can-i-specify"></a>我可以指定多少部 DNS 伺服器？
請參閱 [Azure 限制](../azure-subscription-service-limits.md#networking-limits)一文以取得詳細資料。

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>我可以在建立虛擬網路之後修改 DNS 伺服器嗎？
是。 您可以隨時針對 VNet 變更 DNS 伺服器清單。 如果您變更 DNS 伺服器清單，您必須重新啟動 VNet 中的每個 VM，使其挑選新的 DNS 伺服器。

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>什麼是 Azure 提供的 DNS，以及它是否可搭配 VNet 使用？
Azure 提供的 DNS 是由 Microsoft 所提供的多租用戶 DNS 服務。 Azure 會註冊您在此服務中的所有 VM 和雲端服務角色執行個體。 這個服務可根據相同雲端服務內所包含 VM 和角色的主機名稱，以及根據 VM 的 FQDN 和相同 VNet 中的角色執行個體提供名稱解析功能。 請參閱 [VM 與角色執行個體的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)一文以深入了解 DNS。

> [!NOTE]
> 目前 VNet 中的前 100 個雲端服務具有使用 Azure 所提供 DNS 進行跨租用戶名稱解析的限制。 如果您使用自己的 DNS 伺服器，則不適用這項限制。
> 
> 

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>我是否可以根據每個 VM/服務來覆寫 DNS 設定？
是。 您可以根據雲端服務設定 DNS 伺服器，以便覆寫預設網路設定。 不過，我們建議您盡可能使用全網路 DNS。

### <a name="can-i-bring-my-own-dns-suffix"></a>我可以加上自己的 DNS 尾碼嗎？
否。 您無法針對 VNet 指定自訂的 DNS 尾碼。

## <a name="connecting-virtual-machines"></a>連接虛擬機器

### <a name="can-i-deploy-vms-to-a-vnet"></a>我可以將 VM 部署至 VNet 嗎？
是。 所有連接至透過 Resource Manager 部署模型部署的 VM 網路介面 (NIC) 必須連接到 VNet。 透過傳統部署模型部署的 VM 可以選擇連接至 VNet。

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>我可以將哪些類型的 IP 位址指派至 VM？
* **私人︰** 在每部 VM 內指派至每個 NIC。 您可使用靜態或動態方法來指派位址。 系統會根據您在 VNet 子網路設定中所指定的範圍來指派私人 IP 位址。 即使沒有連線至 VNet，透過傳統部署模型所部署的資源也會獲派私人 IP 位址。 以動態方法所指派的私人 IP 位址仍會指派至資源，直到您刪除資源 (VM 或雲端服務部署位置) 為止。 如果 VM 在處於已停止 (已解除配置) 狀態之後又重新啟動，以動態方法所指派的私人 IP 位址可能會變更。 以靜態方法指派的私人 IP 位址仍會指派至資源，直到您刪除資源為止。 如果您需要確保資源刪除之前，資源的私人 IP 位址永遠不會變更，請使用靜態方法來指派私人 IP 位址。
* **公用︰**選擇性地指派至與透過 Azure Resource Manager 部署模型部署的 VM 所連接的 NIC。 您可使用靜態或動態配置方法來指派位址。 所有透過傳統部署模型部署的 VM 與雲端服務角色執行個體，皆存在於受指派動態公用虛擬 IP (VIP) 位址的雲端服務內。 公用*靜態* IP 位址，稱為[保留的 IP 位址](virtual-networks-reserved-public-ip.md)，可選擇性地被指派為 VIP。 您可以將公用 IP 位址指派至透過傳統部署模型部署的個別 VM 或雲端服務角色執行個體。 這些位址稱為[執行個體層級公用 IP (ILPIP](virtual-networks-instance-level-public-ip.md)位址，並可動態指派。

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>我可以為稍後建立的 VM 保留私人 IP 位址嗎？
否。 您不能保留私人 IP 位址。 如果有可用的私人 IP 位址，則會由 DHCP 伺服器指派至 VM 或角色執行個體。 該 VM 可能不是您想要指派私人 IP 位址的目的地。 不過，您可以將已建立 VM 的私人 IP 位址變更為任何可用的私人 IP 位址。

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>VNet 中的私人 IP 位址會根據 VM 進行變更嗎？
這要看狀況。 VM 中仍會保留動態私人 IP 位址，直到 IP 位址停止 (重新分配) 或刪除為止。 靜態私人 IP 位址在刪除後才會從 VM 中釋放。

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>我可以在 VM 作業系統中將 IP 位址手動指派至 NIC 嗎？
可以，但我們不建議這樣做。 在 VM 作業系統內將 IP 位址手動指派至 NIC，可能會使指派至 Azure VM 中 NIC 的 IP 位置變更，導致失去與 VM 的連線。

### <a name="what-happens-to-my-ip-addresses-if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system"></a>若我在作業系統內停止雲端服務部署位置或關閉 VM，我的 IP 位址會受到影響嗎？
不會受到影響。 IP 位址 (公用 VIP、公用與私人) 仍會指派至雲端服務部署位置或 VM。 動態位址只會在 VM 停止 (重新分配) 或刪除，或雲端服務部署位置已刪除時釋放。 在 Azure 入口網站中的 VM 按一下 [停止] 按鈕，會將 Azure 入口網站的狀態設為「已停止 (已重新分配)」。 在此情況下，VM 將會遺失其 IP 位址。

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>我可以將 VM 從子網路移動至 VNet 中的其他子網路而不需重新部署嗎？
是。 您可以在[如何將 VM 或角色執行個體移至不同的子網路](virtual-networks-move-vm-role-to-subnet.md)一文內找到更多資訊。

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>我可以針對 VM 設定靜態 MAC 位址嗎？
否。 MAC 位址無法以靜態方式設定。

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>當我建立 VM 之後 MAC 位址會保持相同的狀態嗎？
是，若是建立透過 Resource Manager 和傳統部署模型部署的 VM，MAC 位址會維持不變，直至刪除為止。 以往，當 VM 狀態為已停止 (已重新分配) 時，系統會釋放 MAC 位址，但現在即使 VM 狀態為重新分配時，MAC 位址仍會保持不變。

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>我可以從 VNet 中的 VM 連線到網際網路嗎？
是。 部署在 VNet 中的所有 VM 與雲端服務角色執行個體皆可連線到網際網路。

## <a name="azure-services-that-connect-to-vnets"></a>連線到 VNet 的 Azure 服務

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>我可以搭配使用 Azure App Service Web Apps 和 VNet 嗎？
是。 您可以使用 ASE (App Service 環境) 在 VNet 中部署 Web Apps。 如果您已針對 VNet 設定點對站連線，則 Web Apps 可以安全地連線並存取 Azure VNet 中的資源。 如需詳細資訊，請參閱下列文章。

* [在 App Service 環境中建立 Web Apps](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [將您的應用程式與 Azure 虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md)
* [使用 VNet 整合和混合式連接搭配 Web Apps](../app-service/web-sites-integrate-with-vnet.md#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>我可以在 VNet 中使用 Web 和背景工作角色 (PaaS) 部署雲端服務嗎？
是。 您可以 (選擇性地) 在 VNet 內部署雲端服務角色執行個體。 若要這樣做，請在服務組態的網路組態區段中，指定 VNet 名稱和角色/子網路對應。 您不需要更新任何二進位檔。

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>我可以將虛擬機器擴展集 (VMSS) 連接至 VNet 嗎？
是。 您必須將 VMSS 連接至 VNet。

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>我可以將服務移入和移出 VNet 嗎？
否。 您無法將服務移入和移出 VNet。 您必須刪除並重新部署服務，才能將它移動到另一個 VNet。

## <a name="security"></a>安全性

### <a name="what-is-the-security-model-for-vnets"></a>什麼是 VNet 的安全性模型？
VNet 會與另一個 VNet，以及其他裝載於 Azure 基礎結構中的服務完全隔離。 VNet 是一種信任邊界。

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>我可以將輸入或輸出流量限制為與 VNet 連接的資源嗎？
是。 您可以將 [網路安全性群組](virtual-networks-nsg.md) 套用至 VNet、連接至 VNet 的 NIC 或同時在兩者以內的獨立子網路。

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>我可以在與 VNet 連線的資源之間實作防火牆嗎？
是。 您可以透過 Azure Marketplace 部署來自多個廠商的[防火牆網路虛擬應用裝置](https://azure.microsoft.com/en-us/marketplace/?term=firewall)。

### <a name="is-there-information-available-about-securing-vnets"></a>我可以怎樣取得關於保護 VNet 的資訊？
是。 請參閱 [Azure 網路安全性概觀](../security/security-network-overview.md)一文以取得詳細資料。

## <a name="apis-schemas-and-tools"></a>API、結構描述和工具

### <a name="can-i-manage-vnets-from-code"></a>我可以從程式碼管理 VNet 嗎？
是。 您可以使用 [Azure Resource Manage](https://msdn.microsoft.com/library/mt163658.aspx) 中適用於 VNet 的 REST API，與[傳統 (服務管理) ](http://go.microsoft.com/fwlink/?LinkId=296833)) 部署模型。

### <a name="is-there-tooling-support-for-vnets"></a>是否有工具支援 VNet？
是。 深入了解如何使用：
- Azure 入口網站來透過 [Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) 與 [傳統](virtual-networks-create-vnet-classic-pportal.md)部署模型部署 VNet。
- PowerShell 來透過 [Resource Manager](/powershell/resourcemanager/azurerm.network/v3.1.0/azurerm.network.md)與[傳統](/powershell/module/azure/?view=azuresmps-3.7.0)部署模型管理 VNet。
- [Azure 命令列介面 (CLI)](../virtual-machines/azure-cli-arm-commands.md#azure-network-commands-to-manage-network-resources) 來透過兩種部署模型部署管理 VNet。  

