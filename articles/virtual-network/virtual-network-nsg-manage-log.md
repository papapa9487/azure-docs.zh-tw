---
title: "監視 NSG 的作業、事件和計數器 | Microsoft Docs"
description: "了解如何啟用 NSG 的計數器、事件和作業記錄"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2017
ms.author: jdial
ms.openlocfilehash: 552f37dd704de25159bc0f0ad34fdae9ed8b73f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>網路安全性群組 (NSG) 的記錄檔分析

您可以啟用下列 NSG 的診斷記錄類別︰

* **事件記錄檔︰**包含 NSG 規則會套用到以 MAC 位址為基礎的 VM 和執行個體角色的項目。 每隔 60 秒會收集一次這些規則的狀態。
* **規則計數器：**包含套用每個 NSG 規則以拒絕或允許流量之次數的項目。

> [!NOTE]
> 診斷記錄檔僅適用於透過 Azure Resource Manager 部署模型中部署的 NSG。 您無法啟用透過傳統部署模型部署的 NSG 診斷記錄。 若要深入了解這兩個模型，請參閱[了解 Azure 部署模型](../resource-manager-deployment-model.md)一文。

預設會啟用透過任何一個 Azure 部署模型所建立之 NSG 的活動記錄 (先前稱為稽核或操作的記錄檔)。 若要判斷在 NSG 上已完成哪些作業，請在活動記錄中尋找包含下列資源類型的項目： 

- Microsoft.ClassicNetwork/networkSecurityGroups 
- Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/networkSecurityGroups/securityRules 

閱讀 [Azure 活動記錄檔概觀](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)文章以深入了解活動記錄。 

## <a name="enable-diagnostic-logging"></a>啟用診斷記錄

必須啟用每個您想要收集資料的 NSG 診斷記錄。 [Azure 診斷記錄檔概觀](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)文章說明可傳送診斷記錄檔的位置。 如果您沒有現有的 NSG，完成[建立網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)文章中的步驟以建立一個 NSG。 您可以使用下列任何方法來啟用 NSG 診斷記錄功能︰

### <a name="azure-portal"></a>Azure 入口網站

若要使用入口網站來啟用記錄，請登入[入口網站](https://portal.azure.com)。 按一下 [更多服務]，然後輸入網路安全性群組。 選取您想要啟用記錄功能的 NSG。 遵循[在入口網站中啟用診斷記錄檔](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)一文中非計算資源的指示來進行。 選取 **NetworkSecurityGroupEvent**、**NetworkSecurityGroupRuleCounter**，或兩種類別的記錄檔。

### <a name="powershell"></a>PowerShell

若要使用 PowerShell 啟用記錄，請遵循[透過 PowerShell 啟用診斷記錄檔](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)文章中的指示。 請先評估下列資訊之後，再輸入文章中的命令︰

- 您可以視需要取代下列 [文字] 來決定要用於 `-ResourceId` 參數的值，然後輸入命令 `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]`。 命令中的識別碼輸出看起來類似 */subscriptions/[訂用帳戶識別碼]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG 名稱]*。
- 如果您只想要收集記錄檔分類中的資料，請將 `-Categories [category]` 新增至文章中的命令結尾，其中類別為 *NetworkSecurityGroupEvent* 或 *NetworkSecurityGroupRuleCounter*。 如果您不使用 `-Categories` 參數，會同時啟用兩個記錄類別的資料集合。

### <a name="azure-command-line-interface-cli"></a>Azure 命令列介面 (CLI)

若要使用 CLI 啟用記錄，請遵循[透過 CLI 啟用診斷記錄檔](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)文章中的指示。 請先評估下列資訊之後，再輸入文章中的命令︰

- 您可以視需要取代下列 [文字] 來決定要用於 `-ResourceId` 參數的值，然後輸入命令 `azure network nsg show [resource-group-name] [nsg-name]`。 命令中的識別碼輸出看起來類似 */subscriptions/[訂用帳戶識別碼]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG 名稱]*。
- 如果您只想要收集記錄檔分類中的資料，請將 `-Categories [category]` 新增至文章中的命令結尾，其中類別為 *NetworkSecurityGroupEvent* 或 *NetworkSecurityGroupRuleCounter*。 如果您不使用 `-Categories` 參數，會同時啟用兩個記錄類別的資料集合。

## <a name="logged-data"></a>記錄的資料

兩個記錄皆會以 JSON 格式資料寫入。 以下各節中會列出針對每個記錄類型撰寫的特定資料︰

### <a name="event-log"></a>事件記錄檔
這個記錄包含有關哪些 NSG 規則會根據 MAC 位址套用至 VM 和雲端服務角色執行個體的資訊。 每個事件會記錄下列範例資料︰

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>規則計數器記錄檔

此記錄檔包含每個規則套用至資源的相關資訊。 每次套用規則時會記錄下列範例資料︰

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>檢視及分析記錄檔

若要了解如何檢視活動記錄資料，閱讀 [Azure 活動記錄檔概觀](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)文章。 若要了解如何檢視診斷記錄資料，閱讀 [Azure 診斷記錄檔概觀](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)文章。 如果您將診斷資料傳送給 Log Analytics，您可以使用 [Azure 網路安全性群組分析](../log-analytics/log-analytics-azure-networking-analytics.md) (預覽) 管理解決方案來取得深入解析。 
