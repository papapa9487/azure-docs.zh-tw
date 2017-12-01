---
title: "將 Azure DNS 用於私人網域 | Microsoft Docs"
description: "Microsoft Azure 上的私人 DNS 主機服務概觀。"
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: 
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 95cf8ab2bd34e698e12452e062687219bad49eb6
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2017
---
# <a name="using-azure-dns-for-private-domains"></a>將 Azure DNS 用於私人網域
「網域名稱系統」(DNS) 會負責將服務名稱轉譯 (或解析) 為其 IP 位址。 Azure DNS 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構來提供名稱解析。  除了網際網路對向 DNS 網域之外，Azure DNS 現在也支援私人 DNS 網域作為預覽版功能。  
 
Azure DNS 提供一個可靠、安全的 DNS 服務，讓您不必新增自訂 DNS 解決方案，就能管理及解析虛擬網路中的網域名稱。 私人 DNS 區域可讓您使用自己的自訂網域名稱，而不是現今可用的 Azure 提供名稱。  使用自訂網域名稱可協助您量身打造虛擬網路架構，來充分滿足您的組織需求。 它可以為虛擬網路內的 VM 及虛擬網路之間提供名稱解析。 此外，您還可以利用水平分割檢視來設定區域名稱，使私人與公用 DNS 區域能夠共用相同名稱。

![DNS 概觀](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="benefits"></a>優點

* **不再需要自訂的 DNS 解決方案。** 以前，許多客戶建立自訂 DNS 解決方案來管理其虛擬網路中的 DNS 區域。  現在，只要使用 Azure 的原生基礎結構就能執行 DNS 區域管理，擺脫了建立和管理自訂 DNS 解決方案的負擔。

* **使用所有常見的 DNS 記錄類型。**  Azure DNS 支援 A、AAAA、CNAME、MX、NS、PTR、SOA、SRV 及 TXT 記錄。

* **自動主機名稱記錄管理。** 除了裝載您的自訂 DNS 記錄之外，Azure 還會自動維護所指定虛擬網路中 VM 的主機名稱記錄。  這讓您無須建立自訂 DNS 解決方案或修改應用程式，即可將您使用的網域名稱最佳化。

* **虛擬網路之間的主機名稱解析。** 不同於 Azure 提供的主機名稱，私人 DNS 區域可以在虛擬網路之間共用。  此功能簡化了跨網路及服務探索案例，例如虛擬網路對等互連。

* **熟悉的工具和使用者體驗。** 為了縮短學習曲線，這個新供應項目使用已經妥善建立的 Azure DNS 工具 (PowerShell、Resource Manager 範本、REST API)。

* **水平分割 DNS 支援。** Azure DNS 可讓您建立具有相同名稱但從虛擬網路內與從公用網際網路會解析成不同答案的區域。  典型的水平分割 DNS 案例是提供一個專用的服務版本以供在您的虛擬網路內使用。


## <a name="pricing"></a>價格

私人 DNS 區域在受管理的預覽版期間為免費。 在正式運作期間，此功能將與現有的 Azure DNS 供應項目類似，採用以使用量作為基礎計價的模型。 


## <a name="next-steps"></a>後續步驟

了解如何在 Azure DNS 中[建立私人 DNS 區域](./private-dns-getstarted-powershell.md)。

如需深入了解 DNS 區域和記錄，請瀏覽：[DNS 區域和記錄的概觀](dns-zones-records.md)。

深入了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md)。

