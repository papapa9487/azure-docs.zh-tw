---
title: "如何將 Azure API 管理與內部虛擬網路搭配使用 | Microsoft Docs"
description: "了解如何在內部虛擬網路中安裝和設定 Azure API 管理。"
services: api-management
documentationcenter: 
author: vladvino
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: a4c2bda1226ca05c775d011fba7bc59d4dab8998
ms.contentlocale: zh-tw
ms.lasthandoff: 08/31/2017

---
# <a name="using-azure-api-management-service-with-internal-virtual-network"></a>搭配使用 Azure API 管理服務與內部虛擬網路
搭配 Azure 虛擬網路 (VNET)，API 管理可以管理無法在網際網路上存取的 API。 有多個 VPN 技術可用來進行連線，可以在 VNET 內部署兩種主要模式的 API 管理︰
* 外部
* 內部

## <a name="overview"> </a>概觀
當 API 管理部署為「內部虛擬網路」模式時，只有在您可以控制存取的虛擬網路內部才看得到所有服務端點 (閘道器、開發人員入口網站、發行者入口網站、直接管理、Git)。 不會在公用 DNS 伺服器上註冊任何服務端點。

使用內部模式的 API 管理，您可以實現下列案例
* 協力廠商可在您的私人資料中心外使用站對站或 ExpressRoute VPN 連線存取資料中心時，可以安全地將 API 裝載在您的私人資料中心。
* 透過通用閘道器公開您的雲端型 API 和內部部署 API，藉此實現混合式雲端案例。
* 使用單一閘道器端點，管理裝載在多個地理位置的 API。 

## <a name="enable-vpn"> </a>在內部虛擬網路中建立 API 管理
內部虛擬網路中的 API 管理服務是裝載在內部負載平衡器 (ILB) 後。 ILB 的 IP 位址落在 [RFC1918](http://www.faqs.org/rfcs/rfc1918.html) 範圍內。  

### <a name="enable-vnet-connection-using-azure-portal"></a>使用 Azure 入口網站啟用 VNET 連線
首先，依照[建立 API 管理服務][Create API Management service]的步驟建立 API 管理服務。 然後，將 API 管理設定為在虛擬網路內部署。

![在內部虛擬網路中設定 APIM 的功能表][api-management-using-internal-vnet-menu]

部署成功後，您應該會在儀表板上看到您的服務的內部虛擬 IP 位址。

![已設定內部 VNET 的 API 管理儀表板][api-management-internal-vnet-dashboard]

### <a name="enable-vnet-connection-using-powershell-cmdlets"></a>使用 PowerShell Cmdlet 啟用 VNET 連線
您也可以使用 PowerShell 來mdlet 來啟用 VNET 連線。

* **在 VNET 內建立 API 管理服務**：使用 [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) Cmdlet 在 VNET 內建立 Azure API 管理服務，並將其設定為使用內部 VNET 類型。

* **在 VNET 內部署現有 API 管理服務**：使用 [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) Cmdlet 移動虛擬網路內的現有 Azure API 管理服務，並將其設定為使用內部 VNET 類型。

## <a name="apim-dns-configuration"></a> DNS 設定
使用外部虛擬網路模式的 API 管理時，DNS 是由 Azure 管理。 若是內部虛擬網路模式，您必須管理您自己的 DNS。

> [!NOTE]
> API 管理服務不會接聽 IP 位址傳來的要求。 它只會回應對主機名稱 (在其服務端點上設定) 提出的要求；服務端點包括閘道器、開發人員入口網站、發行者入口網站、直接管理端點、Git。

### <a name="access-on-default-host-names"></a>預設主機名稱上的存取︰
當您在公用 Azure 雲端 (假設名為「contoso」) 中建立 API 管理服務，依預設會設定下列服務端點。

>   閘道 / 代理程式 - contoso.azure-api.net

> 發行者入口網站和開發人員入口網站 - contoso.portal.azure-api.net

> 直接管理端點 - contoso.management.azure-api.net

>   Git - contoso.scm.azure-api.net

若要存取這些 API 管理服務端點，您可在連線到「已部署 API 管理的虛擬網路」的子網路中建立虛擬機器。 假設服務的內部虛擬 IP 位址是 10.0.0.5，您可以進行主機檔案對應 (%SystemDrive%\drivers\etc\hosts)，如下所示︰

> 10.0.0.5    contoso.azure-api.net

> 10.0.0.5    contoso.portal.azure-api.net

> 10.0.0.5    contoso.management.azure-api.net

> 10.0.0.5    contoso.scm.azure-api.net

然後您就可以從您建立的虛擬機器存取所有服務端點。 如果在虛擬網路中使用自訂 DNS 伺服器，您也可以建立 A DNS 記錄，並從虛擬網路中的任何地方存取這些端點。 

### <a name="access-on-custom-domain-names"></a>自訂網域名稱上的存取：
如果您不想要存取具有預設主機名稱的 API 管理服務，可以為您的所有服務端點設定自訂網域名稱，類似這樣：

![設定 API 管理的自訂網域][api-management-custom-domain-name]

然後您可以在您的 DNS 伺服器中建立 A 記錄，用來存取這些只能從您的虛擬網路存取的端點。

## <a name="related-content"> </a>相關內容
* [在 VNET 中設定 APIM 常見的網路組態問題][Common Network Configuration Issues]
* [虛擬網路常見問題集](../virtual-network/virtual-networks-faq.md)
* [在 DNS 中建立 A 記錄](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: api-management-get-started.md#create-service-instance
[Common Network Configuration Issues]: api-management-using-with-vnet.md#network-configuration-issues

