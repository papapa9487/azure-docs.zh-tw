---
title: "在 Azure 應用程式閘道中自訂 Web 應用程式防火牆規則 - 入口網站 | Microsoft Docs"
description: "此頁面提供如何透過入口網站，在應用程式閘道中自訂 Web 應用程式防火牆規則的相關資訊。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c3e93614f012eecff0e88f5f2ad13db199406f4a
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-the-portal"></a>透過入口網站自訂 Web 應用程式防火牆規則

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

應用程式閘道 Web 應用程式防火牆提供 Web 應用程式的保護。 這些保護是由 OWASP CRS 規則集所提供。 某些規則可能會導致誤判，並封鎖真正的流量。  基於此原因，應用程式閘道提供功能，在已啟用 Web 應用程式防火牆的應用程式閘道上自訂規則群組和規則。 如需特定規則群組和規則的詳細資訊，請瀏覽 [Web 應用程式防火牆 CRS 規則群組與規則](application-gateway-crs-rulegroups-rules.md)

>[!NOTE]
> 如果您的應用程式閘道並未使用 WAF 層，即會顯示選項，讓您能夠將應用程式閘道升級至 WAF 層，如下圖所示：

![啟用 WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>檢視規則群組與規則

瀏覽至應用程式閘道，然後選取 [Web 應用程式防火牆]。  按一下 [進階規則設定]。  這個檢視會在透過所選擇規則集提供之所有規則群組的頁面上顯示一個表格。

![設定已停用的規則][1]

## <a name="search-for-rules-to-disable"></a>搜尋要停用的規則

[Web 應用程式防火牆設定] 刀鋒視窗提供依文字搜尋篩選規則的功能。 結果只會顯示包含所搜尋文字的規則群組與規則。

![搜尋規則][2]

## <a name="disable-rule-groups-and-rules"></a>停用規則群組與規則

停用規則時，您可以停用整個規則群組，或停用一或多個規則群組下的特定規則。  取消核取您想要停用的規則之後，按一下 [儲存]。  此步驟會將變更儲存到應用程式閘道。

![儲存變更][3]

## <a name="next-steps"></a>後續步驟

一旦您設定停用的規則之後，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md#diagnostic-logging)來了解如何檢視您的 WAF 記錄。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

