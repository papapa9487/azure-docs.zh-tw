---
title: "建立和修改 ExpressRoute 線路︰PowerShell：Azure 傳統| Microsoft Docs"
description: "本文將逐步引導您建立和佈建 ExpressRoute 線路。 本文也會示範如何檢查狀態、更新或刪除和取消佈建線路。"
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 93ddc2975db34053c6a776d1c3b931536f3f8ec7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell-classic"></a>使用 PowerShell 建立和修改 ExpressRoute 線路 (傳統)
> [!div class="op_single_selector"]
> * [Azure 入口網站](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [影片 - Azure 入口網站](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (傳統)](expressroute-howto-circuit-classic.md)
>

本文將逐步引導您使用 PowerShell Cmdlet 和傳統部署模型建立 Azure ExpressRoute 線路。 本文也會示範如何檢查狀態、更新或刪除和取消佈建 ExpressRoute 線路。

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]


**關於 Azure 部署模型**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>開始之前
### <a name="step-1-review-the-prerequisites-and-workflow-articles"></a>步驟 1. 檢閱必要條件和工作流程文章
開始設定之前，請確定您已經檢閱過[必要條件](expressroute-prerequisites.md)和[工作流程](expressroute-workflows.md)。  

### <a name="step-2-install-the-latest-versions-of-the-azure-service-management-sm-powershell-modules"></a>步驟 2. 安裝最新版的 Azure 服務管理 (SM) PowerShell 模組
請遵循[開始使用 Azure PowerShell Cmdlet](/powershell/azure/overview) 中的指示，來取得如何設定您的電腦以使用 Azure PowerShell 模組的逐步指引。

### <a name="step-3-log-in-to-your-azure-account-and-select-a-subscription"></a>步驟 3. 登入您的 Azure 帳戶並選取訂用帳戶
1. 以提高的權限開啟 PowerShell 主控台並連接到您的帳戶。 使用下列範例來協助您連接：

        Login-AzureRmAccount

2. 檢查帳戶的訂用帳戶。

        Get-AzureRmSubscription

3. 如果您有多個訂用帳戶，請選取您要使用的訂用帳戶。

        Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_id"

4. 請確認選取的訂用帳戶識別碼是否已設為預設值。

        Get-AzureSubscription -default

## <a name="create-and-provision-an-expressroute-circuit"></a>建立和佈建 ExpressRoute 線路
### <a name="step-1-import-the-powershell-modules-for-expressroute"></a>步驟 1. 匯入 ExpressRoute 的 PowerShell 模組
 如果您尚未執行此動作，您必須將 Azure 和 ExpressRoute 模組匯入 PowerShell 工作階段，才能開始使用 ExpressRoute Cmdlet。 您要將模組從其安裝所在的位置匯入您的本機電腦。 根據您用來安裝模組的方法而定，位置可能與下列範例所示的不同。 如有必要，請修改範例。  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="step-2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>步驟 2. 取得支援的提供者、位置和頻寬清單
建立 ExpressRoute 線路之前，您需要有支援的連線提供者、位置和頻寬選項的清單。

PowerShell Cmdlet `Get-AzureDedicatedCircuitServiceProvider` 會傳回此資訊，在稍後的步驟中將會用到：

    Get-AzureDedicatedCircuitServiceProvider

請檢查是否列出您的連線服務提供者。 記下下列資訊，因為您稍後在建立線路時將會用到：

* Name
* PeeringLocations
* BandwidthsOffered

您現在已經準備好建立 ExpressRoute 線路。         

### <a name="step-3-create-an-expressroute-circuit"></a>步驟 3. 建立 ExpressRoute 線路
以下示範如何透過矽谷的 Equinix 建立 200-Mbps ExpressRoute 線路。 如果您使用不同的提供者和不同的設定，請在您提出要求時替換成該資訊。

> [!IMPORTANT]
> ExpressRoute 循環將會從發出服務金鑰時開始收費。 請確定在連線提供者準備好佈建線路之後，再執行這項作業。
> 
> 

下列是新服務金鑰的要求範例：

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

或者，如果您想要使用進階附加元件建立 ExpressRoute 線路，請使用下列範例。 如需進階附加元件的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md) 。

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


回應會包含服務金鑰。 您可以執行下列命令來取得所有參數的詳細描述：

    get-help new-azurededicatedcircuit -detailed

### <a name="step-4-list-all-the-expressroute-circuits"></a>步驟 4. 列出所有 ExpressRoute 線路
您可以執行 `Get-AzureDedicatedCircuit` 命令，以取得您建立的所有 ExpressRoute 線路的清單︰

    Get-AzureDedicatedCircuit

回應將如以下範例所示：

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

您隨時可以使用 `Get-AzureDedicatedCircuit` Cmdlet 擷取此資訊。 執行呼叫時，若未指定任何參數，將會列出所有線路。 [服務金鑰]  欄位會列出您的服務金鑰。

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

您可以執行下列命令來取得所有參數的詳細描述：

    get-help get-azurededicatedcircuit -detailed

### <a name="step-5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>步驟 5。 將服務金鑰傳送給連線提供者以進行佈建
 提供服務提供者端目前的佈建狀態相關資訊。  提供 Microsoft 端的狀態。 如需線路佈建狀態的詳細資訊，請參閱 [工作流程](expressroute-workflows.md#expressroute-circuit-provisioning-states) 文章。

當您建立新的 ExpressRoute 線路時，線路會是下列狀態：

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


當連線提供者正在為您啟用線路時，線路會處於下列狀態：

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

ExpressRoute 線路必須處於下列狀態，才可供您使用。

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="step-6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>步驟 6. 定期檢查線路金鑰的情況和狀態
這樣可讓您知道提供者何時已啟用您的線路。 設定線路之後，[ServiceProviderProvisioningState] 會顯示為 [Provisioned]，如下列範例所示：

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="step-7-create-your-routing-configuration"></a>步驟 7. 建立路由組態
請參閱 [ExpressRoute 線路路由設定 (建立和修改線路對等)](expressroute-howto-routing-classic.md) 一文，取得逐步指示。

> [!IMPORTANT]
> 這些指示只適用於由提供第 2 層連線服務的服務提供者所建立的線路。 如果您使用的服務提供者是提供受管理的第 3 層服務 (通常是 IP VPN，如 MPLS)，您的連線提供者會為您設定和管理路由。
> 
> 

### <a name="step-8-link-a-virtual-network-to-an-expressroute-circuit"></a>步驟 8。 將虛擬網路連結到 ExpressRoute 電路
接下來，將虛擬網路連結到 ExpressRoute 線路。 如需逐步指示，請參閱 [將 ExpressRoute 線路連結至虛擬網路](expressroute-howto-linkvnet-classic.md) 。 如果您需要使用傳統部署模型為 ExpressRoute 建立虛擬網路，請參閱[建立 ExpressRoute 的虛擬網路](expressroute-howto-vnet-portal-classic.md)。

## <a name="getting-the-status-of-an-expressroute-circuit"></a>取得 ExpressRoute 線路的狀態
您隨時可以使用 `Get-AzureCircuit` Cmdlet 擷取此資訊。 執行呼叫時，若未指定任何參數，將會列出所有線路。

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

您可以將服務金鑰當作參數傳遞給呼叫，以取得特定 ExpressRoute 線路的詳細資訊。

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


您可以執行下列範例來取得所有參數的詳細描述：

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>修改 ExpressRoute 線路
您可以修改 ExpressRoute 線路的某些屬性，而不會影響連線。

您可以執行下列工作，而無需中途停機：

* 啟用或停用 ExpressRoute 線路的 ExpressRoute 進階附加元件。
* 只要連接埠有可用的容量，就增加 ExpressRoute 線路的頻寬。 請注意，不支援將線路的頻寬降級。 
* 將計量方案從 [計量付費] 變更為 [無限制]。 請注意，不支援將計量方案從 [無限制] 變更為 [計量付費]。
* 您可以啟用和停用 [允許傳統作業] 。

如需限制的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md) 。

### <a name="to-enable-the-expressroute-premium-add-on"></a>啟用 ExpressRoute 進階附加元件
您可以使用下列 PowerShell Cmdlet，為現有的線路啟用 ExpressRoute Premium 附加元件：

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

您的線路現在將啟用 ExpressRoute Premium 附加功能。 請注意，順利執行命令後，我們會開始向您收取進階附加元件功能的費用。

### <a name="to-disable-the-expressroute-premium-add-on"></a>停用 ExpressRoute 進階附加元件
> [!IMPORTANT]
> 如果您使用的資源超出標準線路所允許的數量，這項作業可能會失敗。
> 
> 

#### <a name="considerations"></a>考量

* 從高階降級為標準之前，您必須確定連結至線路的虛擬網路數目小於 10。 如果您沒有這麼做，則更新要求將會失敗，將會以高階費率向您收費。
* 您必須取消連結其他地理政治區域中的所有虛擬網路。 如果您沒有這麼做，則更新要求將會失敗，將會以高階費率向您收費。
* 就私用對等設定而言，路由表必須少於 4000 個路由。 如果路由表大小超過 4000 個路由，BGP 工作階段將會中斷，而且在通告的首碼數目降到 4000 以下之前不會重新啟用。

#### <a name="disable-the-premium-add-on"></a>停用 Premium 附加元件
您可以使用下列 PowerShell Cmdlet，為現有的線路停用 ExpressRoute Premium 附加元件：

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>更新 ExpressRoute 線路頻寬
請查閱 [ExpressRoute 常見問題集](expressroute-faqs.md) ，以取得提供者支援的頻寬選項。 只要實體連接埠 (您的線路在此建立) 允許，您可以選擇大於現有線路的任何大小。

> [!IMPORTANT]
> 如果現有的連接埠上沒有足夠的容量，您可能必須重新建立 ExpressRoute 線路。 如果該位置已無額外的容量，您無法升級線路。
>
> 降低 ExpressRoute 線路的頻寬時必須中斷運作。 頻寬降級需要取消佈建 ExpressRoute 線路，然後重新佈建新的 ExpressRoute 線路。
> 
> 

#### <a name="resize-a-circuit"></a>調整電路大小

一旦決定需要的大小後，您可以使用下列命令來調整線路大小。

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

我們會在 Microsoft 端調整電路的大小。 您必須連絡連線提供者，將他們那邊的設定更新為符合這項變更。 請注意，從這個階段起，我們將開始計算更新頻寬選項的費用。

如果增加線路頻寬時出現下列錯誤，則表示建立現有線路的實體連接埠所剩的頻寬不足。 您必須刪除此線路，並建立所需大小的新線路。 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>取消佈建和刪除 ExpressRoute 循環

### <a name="considerations"></a>考量

* 您必須取消連結 ExpressRoute 線路的所有虛擬網路，此作業才會成功。 如果此作業失敗，請檢查您是否有任何虛擬網路連結至線路。
* 如果 ExpressRoute 線路服務提供者佈建狀態為 **Provisioning** 或 **Provisioned**，您就必須與服務提供者一起合作，取消佈建他們那邊的線路。 我們將繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。
* 若服務提供者已取消佈建循環 (服務提供者佈建狀態設定為 [NotProvisioned] )，則您可以刪除循環。 這樣將會停止針對循環計費。

#### <a name="delete-a-circuit"></a>刪除電路

您可以執行下列命令來刪除 ExpressRoute 線路：

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>後續步驟
建立好線路後，請務必執行下列作業：

* [建立和修改 ExpressRoute 線路的路由](expressroute-howto-routing-classic.md)
* [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-classic.md)

