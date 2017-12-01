---
title: "管理 Azure 保留執行個體 | Microsoft Docs"
description: "了解如何變更訂用帳戶範圍，以及針對 Azure 保留執行個體管理存取權。"
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2017
ms.author: vikdesai
ms.openlocfilehash: e23eea52ff5d27beacf938a1ef153172e24f1aee
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="manage-reserved-virtual-machine-instances"></a>管理 Azure 保留執行個體

購買 Azure 保留執行個體之後，建議您將保留套用至不同的訂用帳戶 (不同於購買期間指定的帳戶)。 或者，如果對應的虛擬機器正在多個訂用帳戶中執行，建議您將保留範圍變更為已共用。 若要最大化保留的折扣，請確定您購買的執行個體數目符合您正在執行的虛擬機器屬性與數目。 若要深入了解保留執行個體，請參閱[預付 Azure 虛擬機器以節省成本](https://go.microsoft.com/fwlink/?linkid=862121)。

## <a name="change-the-scope-for-a-reservation"></a>變更保留範圍
 您的保留折扣適用於符合保留且執行於保留範圍內的虛擬機器。 保留的範圍可以是單一訂用帳戶或您的計費內容中的所有訂用帳戶。 如果您將範圍設定為單一訂用帳戶，此保留會對應至所選訂用帳戶中執行的虛擬機器。 如果您將範圍設定為共用，Azure 會將保留對應至計費內容中所有訂用帳戶上執行的虛擬機器。 計費內容取決於購買此保留所用的訂用帳戶。 若要進一步了解，請參閱[具有保留執行個體的虛擬機器預付](https://go.microsoft.com/fwlink/?linkid=861721)。

更新保留範圍： 
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [更多服務] > [保留]。
3. 選取保留。
4. 選取 [設定] > [組態]。
5. 變更範圍。 如果您從共用變更為單一範圍，您只能選取擁有者是您的訂用帳戶。 只能選取與保留屬於相同計費內容的訂用帳戶。 計費內容取決於您購買保留時選取的訂用帳戶。 該範圍僅適用於預付型方案優惠的 MS-AZR-0003P 訂用帳戶與企業優惠的 MS-AZR-0017P 訂用帳戶。 對於企業合約，開發/測試訂用帳戶沒有取得保留折扣的資格。

## <a name="split-a-single-reservation-into-two-reservations"></a>將單一保留分割成兩個保留
 購買多個執行個體之後，建議您將一個保留中的執行個體指派至不同的訂用帳戶。 依預設，所有執行個體 (購買時所指定的數量) 都具有一個範圍 - 單一訂用帳戶或共用。 例如，您購買了 10 部標準 D2 虛擬機器，並指定範圍為訂用帳戶 A。您現在要將 7 個保留執行個體的範圍變更為訂用帳戶 A，而剩下 3 個變更為訂用帳戶 B。分割保留可讓您分散執行個體，以進行更細微的管理。 您可以選擇共用範圍來簡化訂用帳戶的配置。 但是，基於成本管理或預算考量，您可以配置一些數量給特定的訂用帳戶。

 您可以透過 PowerShell、CLI 或 API 來將保留分成兩個保留。

### <a name="split-a-reservation-by-using-powershell"></a>使用 PowerShell 分割保留
1. 執行下列命令以取得保留訂單識別碼：

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```
2. 取得保留的詳細資料：

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. 將保留分割成兩個，並分散執行個體：

    ```powershell
    # Split the reservation. The sum of the Reserved VM instances, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. 您可以執行下列命令來更新範圍：

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>新增或變更可以管理保留的使用者
您可以透過將人員新增到保留上的角色，來委派保留的管理。 依預設，購買保留的人和帳戶管理員就是保留上的「擁有者」角色。 

您可以從取得保留折扣的訂用帳戶中，獨立管理保留的存取。 當您將管理保留的權限給其他人時，並不會給他們管理訂用帳戶的權限。 而當您將保留範圍內的訂用帳戶管理權限給其他人時，並不會給他們管理保留的權限。
 
委派保留的存取管理： 
1.  登入 [Azure 入口網站](https://portal.azure.com)。
2.  選取 [更多服務] > [保留] 來列出您具有存取權的保留。
3.  選取您想要將存取權委派給其他使用者的保留。
4.  在功能表中選取 [存取控制 (IAM)]。
5.  選取 [新增] > [角色] > [擁有者] (如果您想要給予有限的存取權，可選取不同角色)。 
6. 輸入要新增為擁有者之使用者的電子郵件地址。 
7. 選取使用者，然後選取 [儲存]。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果您仍有其他問題，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
