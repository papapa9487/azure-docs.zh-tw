---
title: "Azure 受管理的應用程式 PublicIpAddressCombo UI 元素 | Microsoft Docs"
description: "描述 Azure 受管理應用程式的 Microsoft.Network.PublicIpAddressCombo UI 元素"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 94f1f64a57784254912cb6cf568fafc15c8e74ec
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI 元素
選取新的或現有公用 IP 位址的控制項群組。 您可以在[建立 Azure 受管理應用程式](publish-service-catalog-app.md)時使用此元素。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- 如果使用者的公用 IP 位址選擇 [無]，網域名稱標籤文字方塊就會加以隱藏。
- 如果使用者選擇現有的公用 IP 位址，網域名稱標籤文字方塊就會加以停用。 其值為所選取 IP 位址的網域名稱標籤。
- 網域名稱尾碼 (例如，westus.cloudapp.azure.com) 會根據選取的位置自動更新。

## <a name="schema"></a>結構描述
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "foobar"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>備註
- 如果將 `constraints.required.domainNameLabel` 設為 **true**，使用者在建立新的公用 IP 位址時，就必須提供網域名稱標籤。 無法選取沒有標籤的現有公用 IP 位址。
- 如果將 `options.hideNone` 設為 **true**，公用 IP 位址的 [無] 選項就會加以隱藏。 預設值為 **false**。
- 如果將 `options.hideDomainNameLabel` 設為 **true**，網域名稱標籤的文字方塊就會加以隱藏。 預設值為 **false**。
- 如果 `options.hideExisting` 為 true，使用者就無法選擇現有的公用 IP 位址。 預設值為 **false**。

## <a name="sample-output"></a>範例輸出
如果使用者未選取公用 IP 位址，則預期會產生下列輸出︰
```json
{
  "newOrExistingOrNone": "none"
}
```

如果使用者選取新的或現有 IP 位址，則預期會產生下列輸出︰
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "foobar",
  "newOrExistingOrNone": "new"
}
```
- 當 `options.hideNone` 指定為 **true** 時，`newOrExistingOrNone` 只會有**新的**或**現有的**值。
- 當 `options.hideDomainNameLabel` 指定為 **true** 時，`domainNameLabel` 為未宣告。

## <a name="next-steps"></a>後續步驟
* 如需受管理應用程式的簡介，請參閱 [Azure 受管理的應用程式概觀](overview.md)。
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
