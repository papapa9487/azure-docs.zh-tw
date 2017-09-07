---
title: "Azure AD Domain Services︰網路指導方針 | Microsoft Docs"
description: "Azure Active Directory Domain Services 的網路考量"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 08ea5f557498f64825da8fe03d146cace0c53526
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Azure AD 網域服務的網路考量
## <a name="how-to-select-an-azure-virtual-network"></a>如何選取 Azure 虛擬網路
下列指導方針可協助您選取要與 Azure AD 網域服務搭配使用的虛擬網路。

### <a name="type-of-azure-virtual-network"></a>Azure 虛擬網路類型
* 您可以啟用傳統 Azure 虛擬網路中的 Azure AD 網域服務。 不過，很快將會取代傳統虛擬網路的支援。 建議所有新建立的受管理網域使用資源管理員虛擬網路。
* 使用 Azure Resource Manager 建立的虛擬網路可以啟用 Azure AD Domain Services。
* 您可以將其他虛擬網路連線到啟用 Azure AD Domain Services 的虛擬網路。 如需詳細資訊，請參閱[網路連線](active-directory-ds-networking.md#network-connectivity)。
* **區域虛擬網路**：如果您計劃使用現有的虛擬網路，請確定它是區域虛擬網路。

  * 使用舊版同質群組機制的虛擬網路不能與 Azure AD 網域服務搭配使用。
  * 若要使用 Azure AD 網域服務， [請將傳統的虛擬網路移轉到區域虛擬網路](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。

### <a name="azure-region-for-the-virtual-network"></a>虛擬網路的 Azure 區域
* 您的 Azure AD 網域服務受管理網域已部署在與您選擇用來啟用該服務的虛擬網路所在的同一 Azure 區域。
* 選取虛擬網路，其位於 Azure AD 網域服務支援的 Azure 區域中。
* 請參閱 [依區域提供的 Azure 服務](https://azure.microsoft.com/regions/#services/) 頁面，以了解可使用 Azure AD 網域服務的 Azure 區域。

### <a name="requirements-for-the-virtual-network"></a>虛擬網路需求
* **Azure 工作負載的近接感測**：選取需要存取 Azure AD 網域服務的目前主控/即將主控的虛擬網路。 如果您的工作負載是部署在與受管理網域不同的虛擬網路，也可以選擇連線虛擬網路。
* **自訂/自備 DNS 伺服器**：請確定沒有針對虛擬網路設定的自訂 DNS 伺服器。 自訂 DNS 伺服器的範例，是您已部署在虛擬網路中的 Windows Server VM 上執行之 Windows Server DNS 的執行個體。 Azure AD Domain Services 不會與虛擬網路內部署的任何自訂 DNS 伺服器進行整合。
* **網域名稱相同的現有網域**：請確定現有網域的名稱並未與該虛擬網路上可用的網域名稱相同。 例如，假設您有名為 'contoso.com' 的網域已可用於選取的虛擬網路。 接著，您可嘗試在該虛擬網路上啟用具有相同網域名稱 (即 'contoso.com') 的 Azure AD 網域服務受管理網域。 您在嘗試啟用 Azure AD 網域服務時發生錯誤。 這個錯誤是因為名稱與該虛擬網路上的網域名稱衝突。 在此情況下，您必須使用不同的名稱來設定 Azure AD 網域服務受管理網域。 或者，您可以解除佈建現有的網域，然後繼續啟用 Azure AD 網域服務。

> [!WARNING]
> 在您啟用網域服務之後，便無法將該服務移到不同的虛擬網路。
>
>

## <a name="network-security-groups-and-subnet-design"></a>網路安全性群組與子網路設計
[網路安全性群組 (NSG)](../virtual-network/virtual-networks-nsg.md) 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕虛擬網路中 VM 執行個體的網路流量。 NSG 可與子網路或該子網路內的個別 VM 執行個體相關聯。 當 NSG 與子網路相關聯時，ACL 規則便會套用至該子網路中的所有 VM 執行個體。 此外，將 NSG 直接關聯至該 VM，即可進一步限制個別 VM 的流量。

![建議的子網路設計](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

### <a name="best-practices-for-choosing-a-subnet"></a>選擇子網路的最佳作法
* 將 Azure AD 網域服務部署到 Azure 虛擬網路中**不同的專用子網路**。
* 請勿將 NSG 套用至受管理網域的專用子網路。 如果您必須將 NSG 套用至專用子網路，請確保**不會封鎖服務及管理您的網域所需的連接埠**。
* 請勿過度限制受管理網域的專用子網路內可用的 IP 位址數目。 此限制會使服務無法將兩個網域控制站提供給受管理的網域使用。
* **請勿在虛擬網路的閘道子網路中啟用 Azure AD 網域服務**。

> [!WARNING]
> 當您讓 NSG 與已啟用 Azure AD 網域服務的子網路產生關聯時，可能會中斷 Microsoft 服務及管理網域的功能。 此外，Azure AD 租用戶與受管理網域之間的同步處理已中斷。 **SLA 不適用於已套用 NSG 的部署，因為 NSG 會阻止 Azure AD 網域服務更新和管理您的網域。**
>
>

### <a name="ports-required-for-azure-ad-domain-services"></a>Azure AD 網域服務所需的連接埠
下列是 Azure AD 網域服務維護及服務受管理網域所需的連接埠。 請確保未針對已啟用受管理網域的子網路封鎖這些連接埠。

| 連接埠號碼 | 目的 |
| --- | --- |
| 443 |與 Azure AD 租用戶同步處理 |
| 3389 |管理您的網域 |
| 5986 |管理您的網域 |
| 636 |保護受管理網域的 LDAP (LDAPS) 存取 |

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>具有 Azure AD Domain Services 之虛擬網路的範例 NSG
下表說明您可以針對具有 Azure AD Domain Services 受管理網域之虛擬網路設定的範例 NSG。 這個規則允許從上述指定的連接埠輸入流量，以確保您受管理的網域保持修補、更新，並且可由 Microsoft 監視。 預設 'DenyAll' 規則適用於來自網際網路的所有其他輸入流量。

此外，NSG 也會說明如何透過網際網路來鎖定安全 LDAP 存取。 如果您尚未透過網際網路啟用安全 LDAP 存取至受管理的網域，請跳過此規則。 NSG 包含一組規則，允許僅從一組指定 IP 位址透過 TCP 連接埠 636 的輸入 LDAPS 存取。 允許從指定的 IP 位址透過網際網路之 LDAPS 存取的 NSG 規則，其優先順序高於 DenyAll NSG 規則。

![透過網際網路之安全 LDAP 存取的範例 NSG](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**更多資訊** - [建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)。


## <a name="network-connectivity"></a>網路連線
Azure AD Domain Services 受管理網域只可在 Azure 的單一虛擬網路中啟用。

### <a name="scenarios-for-connecting-azure-networks"></a>Azure 網路連線的案例
在下列各部署案例中，連接 Azure 虛擬網路來使用受管理的網域︰

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>在多個 Azure 虛擬網路中使用受管理的網域
您可以將其他 Azure 虛擬網路連線到已啟用 Azure AD Domain Services 的 Azure 虛擬網路。 此 VPN/VNet 對等連線可讓您使用受管理的網域，而您的工作負載已部署在其他虛擬網路中。

![傳統的虛擬網路連線](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>在以資源管理員為基礎的虛擬網路中，使用受管理的網域
您可以將以資源管理員為基礎的虛擬網路連接到已啟用 Azure AD 網域服務的 Azure 傳統虛擬網路。 此連線可讓您使用受管理的網域，而您的工作負載已部署在以資源管理員為基礎的虛擬網路中。

![傳統虛擬網路連線的資源管理員](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>網路連線選項
* **使用虛擬網路對等互連的 VNet 對 VNet 連線**：虛擬網路對等互連是透過 Azure 骨幹網路來連接同一區域中兩個虛擬網路的機制。 一旦對等互連，針對用作連線而言兩個虛擬網路看起來就像一個。 它們仍做為不同的資源進行管理，但這些虛擬網路中的虛擬機器可以使用私人 IP 位址彼此直接通訊。

    ![使用對等互連的虛擬網路連線](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [詳細資訊 - 虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)
    
* **使用站對站 VPN 連線的 VNet 對 VNet 連線**：將一個虛擬網路連接到另一個虛擬網路 (VNet 對 VNet) 類似於將虛擬網路連接到內部部署網站位置。 這兩種連線類型都使用 VPN 閘道提供使用 IPsec/IKE 的安全通道。

    ![使用 VPN 閘道的虛擬網路連線](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [詳細資訊 - 使用 VPN 閘道連接虛擬網路](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>相關內容
* [Azure 虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)
* [設定傳統部署模型的 VNet 對 VNet 連接](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Azure 網路安全性群組](../virtual-network/virtual-networks-nsg.md)
* [建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

