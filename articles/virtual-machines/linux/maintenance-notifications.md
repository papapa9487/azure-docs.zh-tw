---
title: "處理 Azure 中 Linux VM 的維修通知 | Microsoft Docs"
description: "檢視 Azure 中執行的 Linux 虛擬機器的維修通知，並開始自助維修。"
services: virtual-machines-linux
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: zivr
ms.openlocfilehash: 2fc96898c9b7ebefc7648b5819975d9015e76c0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>處理 Linux 虛擬機器預定進行的維修作業通知

為提升虛擬機器之主機基礎結構的可靠性、效能和安全性，Azure 會定期執行更新。 更新會變更，例如裝載環境的修補或將硬體升級與解除委任。 這些更新大多數都會在不影響託管虛擬機器的情況下執行。 不過，更新在某些情況下確實會造成影響：

- 如果維護不需要重新開機，Azure 會在主機更新時使用就地移轉來暫停 VM。

- 如果維護需要重新開機，您會在規劃維護時收到通知。 在這些情況下，您會獲得一段時間，供您在適合的時間自行開始維修。


預定進行的維修作業若需要重新開機，會排定在不同波段。 每一波段有不同的範圍 (區域)。

- 波段開始時會傳送通知給客戶。 根據預設，通知會傳送給訂用帳戶擁有者和共同擁有者。 您可以對通知新增更多收件者和傳訊選項，例如電子郵件、SMS 和 Webhook。 
- 在通知之後很快即會開始自助期間。 在此期間，您可以找到此波段中包含的虛擬機器，並使用主動式重新部署開始維修。 
- 在自助期間之後，排定的維修期間隨即開始。 此時，Azure 會排定並將必要的維修套用於您的虛擬機器。  

有兩個期間的目標是要讓您在知道 Azure 何時將會自動開始維修的同時，有足夠時間開始維修，並將虛擬機器重新啟動。

您可以使用 Azure CLI、PowerShell、REST API 和 Azure 入口網站來查詢您的 VM 的維修期間，並開始自助維修。

 > [!NOTE]
 > 如果您嘗試開始維修並失敗時，Azure 會將您的 VM 標示為**已略過**，並且它不會在排定的維修期間重新啟動。 相反地，稍後會與您連絡，以取得新的排程。 

## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>使用 CLI 尋找排定維修的 VM

您可以使用 [azure vm get-instance-view](/cli/azure/vm?view=azure-cli-latest#az_vm_get_instance_view) 來查看預定進行的維修作業資訊。
 
只在有預定進行的維修作業時，才會傳回維修資訊。 如果沒有會影響 VM 的排定維修，則命令不會傳回任何維修資訊。 

```azure-cli
az vm get-instance-view  - g rgName  -n vmName 
```

下列是 MaintenanceRedeployStatus 下傳回的值： 

| 值 | 說明   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 指出您目前是否可以在 VM 上開始維修 ||
| PreMaintenanceWindowStartTime         | 維修自助期間的開始，此時您可以在 VM 上起始維修 ||
| PreMaintenanceWindowEndTime           | 維修自助期間的結束，此時您可以在 VM 上起始維修 ||
| MaintenanceWindowStartTime            | 排定維修期間的開始，此時您可以在 VM 上起始維修 ||
| MaintenanceWindowEndTime              | 排定維修期間的結束，此時您可以在 VM 上起始維修 ||
| LastOperationResultCode               | 前次嘗試在 VM 上起始維修的結果 ||


## <a name="start-maintenance-on-your-vm-using-cli"></a>使用 CLI 在 VM 上開始維修

如果 `IsCustomerInitiatedMaintenanceAllowed` 設為 true，則下列呼叫會在 VM 上起始維修。

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]


## <a name="next-steps"></a>後續步驟

了解如何使用[排定的事件](scheduled-events.md)從 VM 內註冊取得維修事件。