---
title: "將虛擬網路連結到 ExpressRoute 線路：PowerShell：傳統：Azure | Microsoft Docs"
description: "本文提供以下內容的概觀：如何使用傳統部署模型和 PowerShell 將虛擬網路 (VNet) 連結到 ExpressRoute 線路。"
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9b53fd72-9b6b-4844-80b9-4e1d54fd0c17
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2017
ms.author: ganesr
ms.openlocfilehash: 8df8a4c6ff0897c821e13248e0494b17e1a4992d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>使用 PowerShell 將虛擬網路連接到 ExpressRoute 線路 (傳統)
> [!div class="op_single_selector"]
> * [Azure 入口網站](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [影片 - Azure 入口網站](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (傳統)](expressroute-howto-linkvnet-classic.md)
>

本文會協助您使用傳統部署模型和 Azure PowerShell 將虛擬網路 (VNet) 連結到 ExpressRoute 線路。 虛擬網路可以位於相同的訂用帳戶中，或屬於另一個訂用帳戶。

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**關於 Azure 部署模型**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>組態必要條件
1. 您需要最新版的 Azure PowerShell 模組。 您可以從 [Azure 下載頁面](https://azure.microsoft.com/downloads/)的 PowerShell 區段下載最新的 PowerShell 模組。 請遵循 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview) 中的指示，取得如何設定您的電腦以使用 Azure PowerShell 模組的逐步指引。
2. 開始設定之前，請務必檢閱[必要條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)和[工作流程](expressroute-workflows.md)。
3. 您必須擁有作用中的 ExpressRoute 線路。
   * 遵循指示來 [建立 ExpressRoute 線路](expressroute-howto-circuit-classic.md) ，並由您的連線提供者來啟用該線路。
   * 確定您已針對循環設定了 Azure 私用對等。 請參閱 [設定路由](expressroute-howto-routing-classic.md) 一文，以取得路由指示。
   * 請確定已設定 Azure 私用對等，且已開啟您的網路與 Microsoft 之間的 BGP 對等，讓您可以啟用端對端連線。
   * 您必須有已建立且完整佈建的虛擬網路和虛擬網路閘道。 請遵循指示 [設定 ExpressRoute 的虛擬網路](expressroute-howto-vnet-portal-classic.md)。

您最多可以將 10 個虛擬網路連結至 ExpressRoute 電路。 所有的虛擬網路都必須位於同一個地理區域。 如果您已啟用 ExpressRoute 高階附加元件，則可將更大量的虛擬網路連結到您的 ExpressRoute 電路，或是連結其他地理區域的虛擬網路。 如需高階附加元件的詳細資訊，請參閱 [常見問題集](expressroute-faqs.md) 。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>將相同訂用帳戶中的虛擬網路連接到線路
您可以使用下列 Cmdlet，將虛擬網路連結到 ExpressRoute 線路。 執行 Cmdlet 之前，請確定您已建立虛擬網路閘道，並準備好進行連結。

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>將不同訂用帳戶中的虛擬網路連接到線路
您可以讓多個訂用帳戶共用 ExpressRoute 線路。 下圖顯示簡單的圖解，示範多個訂用帳戶共用 ExpressRoute 線路的方式。

大型雲端內的每個較小型雲端，會用來代表屬於組織內不同部門的訂用帳戶。 組織內的每個部門都可以使用自己的訂用帳戶來部署它們的服務，但可共用單一 ExpressRoute 線路，以連接回內部部署網路。 單一部門 (在此範例中：IT) 可以擁有 ExpressRoute 循環。 組織內的其他訂用帳戶可以使用 ExpressRoute 電路。

> [!NOTE]
> ExpressRoute 循環擁有者需支付專用循環的連線和頻寬費用。 所有虛擬網路都會共用相同的頻寬。
> 
> 

![跨訂用帳戶的連線能力](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>系統管理
「線路擁有者」  是訂用帳戶的管理員/共同管理員，而 ExpressRoute 線路即建立於該訂用帳戶中。 線路擁有者可以授權其他訂用帳戶的管理員/共同管理員 (工作流程圖中稱為「線路使用者」 ) 來使用他們擁有的專用線路。 獲得使用組織 ExpressRoute 線路的授權後，這些線路使用者就可以將其訂用帳戶中的虛擬網路連結到 ExpressRoute 線路。

電路擁有者能夠隨時修改及撤銷授權。 如果撤銷授權，則在存取權遭撤銷的訂用帳戶中，所有連結均會被刪除。

### <a name="circuit-owner-operations"></a>循環擁有者作業

**建立授權**

電路擁有者可授權其他訂用帳戶管理員使用指定的電路。 在下列範例中，線路管理員 (Contoso IT) 讓另一個訂用帳戶的管理員 (Dev-Test) 可將最多 2 個虛擬網路連結到線路。 Contoso IT 的系統管理員的做法是指定 Dev-Test Microsoft 識別碼。 此 Cmdlet 不會傳送電子郵件給指定的 Microsoft ID。 電路擁有者必須明確通知其他訂用帳戶擁有者，告知授權已完成。

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**檢閱授權**

線路擁有者可以藉由執行下列 Cmdlet，來檢閱特定線路上發出的所有授權：

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


**更新授權**

線路擁有者可以使用下列 Cmdlet 來修改授權：

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**刪除授權**

線路擁有者可以使用下列 Cmdlet 來撤銷/刪除使用者的授權：

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>循環使用者作業

**檢閱授權**

線路使用者可以使用下列 Cmdlet 來檢閱授權：

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

**兌換連結授權**

線路使用者可以執行下列 Cmdlet 來兌換連結授權：

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

在虛擬網路的新連結訂用帳戶中執行此命令：

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>後續步驟
如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

