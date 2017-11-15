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
ms.date: 10/26/2017
ms.author: zivr
ms.openlocfilehash: b31955e19883f9fe2e7ed6cf7f5076eaf52577c0
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2017
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

## <a name="classic-deployments"></a>傳統部署

如果您仍有使用傳統部署模型部署的舊版 VM，可以使用 CLI 1.0 查詢 VM 並起始維護。

請鍵入下列項目，以確認您使用傳統 VM 的模式正確：

```
azure config mode asm
```

若要取得名為 *myVM* 的 VM 維護狀態，請鍵入：

```
azure vm show myVM 
``` 

若要開始維護 *myService* 服務和 *myDeployment* 部署中名為 *myVM* 的傳統 VM，請鍵入：

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```


## <a name="faq"></a>常見問題集


**問：為什麼需要立即重新啟動我的虛擬機器？**

**答：**雖然對 Azure 平台的大部分更新與升級不會影響虛擬機器的可用性，但是有時候我們無法避免重新啟動裝載於 Azure 的虛擬機器。 我們已經累積幾項變更，需要我們重新啟動伺服器，這會導致虛擬機器重新開機。

**問：如果我遵循建議針對高可用性使用可用性設定組，是否安全？**

**答：**部署在可用性設定組或虛擬機器擴展集的虛擬機器，具有更新網域 (UD) 的概念。 執行維護時，Azure 會接受 UD 條件約束，並且不會從不同的 UD (在相同的可用性設定組內) 重新啟動虛擬機器。  Azure 也會等候至少 30 分鐘，再移至下一個虛擬機器群組。 

如需高可用性的詳細資訊，請參閱在 Azure 中管理 Windows 虛擬機器的可用性或在 Azure 中管理 Linux 虛擬機器的可用性。

**問：我在其他區域設定災害復原。是否安全？**

**答：**每個 Azure 區域都會與相同地理位置 (例如美國、歐洲或亞洲) 內的另一個區域配對。 計劃性 Azure 更新會以逐個區域的方式發行至配對的區域，以將停機時間縮到最短並將應用程式中斷風險降到最低。 計劃維護期間，Azure 可能會排程類似期間讓使用者開始維護，不過排程的維護期間在配對的區域之間有所不同。  

如需有關 Azure 區域的詳細資訊，請參閱 Azure 中虛擬機器的區域和可用性。  您可以在這裡看到完整的區域配對清單。

**問：我如何取得規劃維護的通知？**

**答：**規劃的維護是從對一或多個 Azure 區域設定排程開始。 之後，電子郵件通知會傳送至訂用帳戶擁有者 (每個訂用帳戶一封電子郵件)。 可以使用「活動記錄警示」來設定此通知的其他通道和收件者。 如果您將虛擬機器部署到已排程規劃的維護之區域，您不會收到通知，但是需要檢查 VM 的維護狀態。

**問：我在入口網站、Powershell 或 CLI 中都看不到任何關於規劃的維護之指示，發生什麼情況？**

**答：**與規劃的維護相關之資訊僅在規劃的維護期間，適用於受到該維護影響的 VM。 也就是說，如果您看不到資料，可能是維護已完成 (或尚未啟動)，或者您的虛擬機器是裝載在更新的伺服器上。

**問：我是否應該在我的虛擬機器上啟動維護？**

**答：**一般而言，部署在雲端服務、可用性設定組或虛擬機器擴展集中的工作負載，會復原規劃的維護。  在規劃維護期間，在任何指定時間只有單一更新網域受影響。 請注意，受影響的更新網域順序不一定會循序發生。

您可能想要在下列情況下自行啟動維護：
- 您的應用程式是在單一虛擬機器上執行，且您必須在下班時間套用所有維護
- 您必須在 SLA 當中協調維護時間
- 即使 VM 是在可用性設定組內，您在每個 VM 重新啟動之間仍然需要超過 30 分鐘的時間。
- 您想要關閉整個應用程式 (多層、多個更新網域)，以便更快完成維護。

**問：是否有方法可以確切知道我的虛擬機器何時會受到影響？**

**答：**設定排程時，我們會定義數天的時間期間。 不過，此期間內伺服器 (和 VM) 的確切順序則未知。 想要知道其 VM 確切時間的客戶，可以使用排程的事件並且從虛擬機器內查詢，之後會在 VM 重新啟動前 10 分鐘收到通知。
  
**問：重新啟動我的虛擬機器需要多久時間？**

**答：**根據您的 VM 大小，重新啟動最長可能需要幾分鐘的時間。 請注意，如果您使用雲端服務、擴展集或可用性設定組，在每個 VM 群組 (UD) 之間有 30 分鐘。 

**問：在雲端服務、擴展集和 Service Fabric 中會有什麼體驗？**

**答：**這些平台會受到規劃的維護影響，使用這些平台的客戶是安全的，因為只有單一升級網域 (UD) 中的 VM 會在任何指定時間受到影響。  

**問：我收到一封關於硬體解除委任的電子郵件，這與規劃的維護相同嗎？**

**答：**硬體解除委任是規劃的維護事件，我們尚未讓這個使用案例上架到新的體驗。  我們設想到，如果客戶收到關於兩個不同規劃的維護之兩封類似的電子郵件，他們會感到混淆。

**問：我在我的 VM 上看不到任何維護資訊。哪裡發生錯誤？**

**答：**您在 VM 上看不到任何維護資訊有以下幾個原因：
1.  您使用標示為 Microsoft 內部的訂用帳戶。
2.  您的 VM 未排程進行維護。 可能是維護已經結束、取消或修改，所以您的 VM 不再受到它的影響。
3.  您尚未將 [維護] 資料行新增至您的 VM 清單檢視。 雖然我們已將此資料行新增至預設檢視，但是設定為查看非預設資料行的客戶必須將 [維護] 資料行手動新增至其 VM 清單檢視。

**問：我的 VM 第二次排程進行維護。原因為何？**

**答：**有數個使用案例，您會在您已完成維護重新部署之後，看到 VM 排程進行維護：
1.  我們已取消維護，並且使用不同的裝載重新啟動它。 可能是我們偵測到發生錯誤的裝載，只是需要部署額外承載。
2.  因為硬體故障，您的 VM 已「服務修復」到另一個節點
3.  您已選取停止 (解除配置)，然後重新啟動 VM
4.  您已為 VM 開啟 [自動關機]


## <a name="next-steps"></a>後續步驟

了解如何使用[排定的事件](scheduled-events.md)從 VM 內註冊取得維修事件。