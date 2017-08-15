---
title: "在 Azure 應用程式閘道中自訂 Web 應用程式防火牆規則 - PowerShell | Microsoft Docs"
description: "此頁面提供如何透過 PowerShell，在應用程式閘道中自訂 Web 應用程式防火牆規則的相關資訊。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 7ed79af5654939c86ca9449be2a59aa0be1136dc
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-powershell"></a>透過 PowerShell 自訂 Web 應用程式防火牆規則

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

應用程式閘道 Web 應用程式防火牆提供 Web 應用程式的保護。 這些保護是由 OWASP CRS 規則集所提供。 某些規則可能會導致誤判，並封鎖真正的流量。  基於此原因，應用程式閘道提供功能，在已啟用 Web 應用程式防火牆的應用程式閘道上自訂規則群組和規則。 如需特定規則群組和規則的詳細資訊，請瀏覽 [Web 應用程式防火牆 CRS 規則群組與規則](application-gateway-crs-rulegroups-rules.md)

## <a name="view-rule-groups-and-rules"></a>檢視規則群組與規則

以下範例顯示如何檢視可在啟用 WAF 的應用程式閘道上設定的規則和規則群組。

### <a name="view-rule-groups"></a>檢視規則群組

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

以下是上述範例的截斷回應。

```
OWASP (Ver. 3.0):

    REQUEST-910-IP-REPUTATION:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            910011     Rule 910011
            910012     Rule 910012
            ...        ...

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            ...        ...

OWASP (Ver. 2.2.9):

    crs_20_protocol_violations:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            960911     Invalid HTTP Request Line
            981227     Apache Error: Invalid URI in Request.
            960000     Attempted multipart/form-data bypass
            ...        ...
```

## <a name="disable-rules"></a>停用規則

下列範例會停用應用程式閘道上的規則 `910018` 和 `910017`。

```azurecli
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>後續步驟

一旦您設定停用的規則之後，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md#diagnostic-logging)來了解如何檢視您的 WAF 記錄。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
