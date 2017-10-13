---
title: "在傳統入口網站中自動調整雲端服務 | Microsoft Docs"
description: "(傳統) 了解如何使用傳統入口網站，在 Azure 中設定雲端服務 Web 角色或背景工作角色的自動調整規則。"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: eb167d70-4eba-42a4-b157-d8d0688abf4b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 90d55bbac6e113d6add848ace67cf0749e26342b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-classic-portal"></a>如何在傳統入口網站中設定雲端服務的自動調整
> [!div class="op_single_selector"]
> * [Azure 入口網站](cloud-services-how-to-scale-portal.md)
> * [Azure 傳統入口網站](cloud-services-how-to-scale.md)

在 Azure 傳統入口網站的 [調整] 頁面上，您可以設定您 web 角色或背景工作角色的自動調整規模設定。 或者，您可以設定手動調整，而非規則型自動縮放比例。

> [!NOTE]
> 本文著重於雲端服務 web 和背景工作角色。 當您直接建立虛擬機器 (傳統) 時，它會裝載於雲端服務中。 其中有些資訊適用於這些類型的虛擬機器。 調整虛擬機器的可用性設定組只是根據您設定的調整規則將其關閉或開啟。 如需虛擬機器和可用性設定組的詳細資訊，請參閱 [管理虛擬機器的可用性](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

在設定應用程式的調整之前，您應該先考量下列資訊：

* 調整受到核心使用量影響。

    較大型的角色執行個體會使用較多核心。 您只能在訂用帳戶的核心限制內調整應用程式。 例如，假設您的訂用帳戶有 20 個核心的限制。 如果您使用兩個中型大小的雲端服務來執行應用程式 (總計 4 個核心)，則您最多只能在訂用帳戶中相應增加所剩餘的 16 個核心的其他雲端服務部署。 如需關於大小的詳細資訊，請參閱[雲端服務的大小](cloud-services-sizes-specs.md)。

* 您必須先建立佇列並且將它與角色建立關聯，然後才能根據訊息臨界值調整應用程式。 如需詳細資訊，請參閱 [如何使用佇列儲存體服務](../storage/queues/storage-dotnet-how-to-use-queues.md)(英文)。

* 您可以調整與您雲端服務連結的資源。 如需關於連結資源的詳細資訊，請參閱 [作法：將資源連結到雲端服務](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service)。

* 若要對應用程式啟用高可用性，您應該確定應用程式是以兩個以上的角色執行個體來部署。 如需詳細資訊，請參閱 [服務等級協定](https://azure.microsoft.com/support/legal/sla/)。

## <a name="schedule-scaling"></a>Schedule scaling
根據預設，所有角色皆未遵循特定的排程。 因此，任何變更的設定皆可套用到所有時間和全年中的所有天數。 您可以根據意願將手動或自動調整設定為下列其中一個模式︰

* 工作日
* 週末
* 週間夜
* 週間早上
* 特定日期
* 特定日期範圍

這個排程設定可以在 [雲端服務] [Azure 傳統入口網站](https://manage.windowsazure.com/) 頁面上的  
[雲端服務] > \[您的雲端服務\] > [調整] > \[生產或預備\] 頁面。

對您想要變更的每個角色按一下 [設定排程時間]  按鈕。

![以排程為基礎的雲端服務自動調整][scale_schedules]

## <a name="manual-scale"></a>手動調整
在 [調整]  頁面上，您可以手動增加或減少雲端服務中執行的執行個體數目。 這項設定會針對您已建立的每個排程進行，或者在您尚未建立排程時隨時進行。

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中，按一下 [雲端服務]，然後按一下雲端服務的名稱以開啟儀表板。
   
   > [!TIP]
   > 如果您沒有看到您的雲端服務，您可能需要從 [生產] 變更為 [預備]，反之亦然。

2. 按一下 [調整] 。
3. 選取您想要變更其調整選項的排程。 如果您尚未定義排程，則預設為 [無排程時間]。
4. 尋找 [依度量調整規模] 區段，然後選取 [無]。 這項設定是所有角色的預設。
5. 雲端服務中的每個角色都有個滑桿可變更要使用的執行個體數目。
   
    ![手動調整雲端服務角色][manual_scale]
   
    如果您需要更多執行個體，您可能需要變更 [雲端服務虛擬機器大小](cloud-services-sizes-specs.md)。
6. 按一下 [儲存] 。  
   系統隨即根據您做的選取來新增或移除角色執行個體。

> [!TIP]
> 每當您看到 ![][tip_icon]，請將滑鼠移向它，您就可以取得特定設定執行作業的相關說明。

## <a name="automatic-scale---cpu"></a>自動調整 - CPU
此模式可以調整平均 CPU 使用量百分比高於或低於指定的臨界值。 在這種情況會建立或刪除角色執行個體。

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中，按一下 [雲端服務]，然後按一下雲端服務的名稱以開啟儀表板。
   
   > [!TIP]
   > 如果您沒有看到您的雲端服務，您可能需要從 [生產] 變更為 [預備]，反之亦然。

2. 按一下 [調整] 。
3. 選取您想要變更其調整選項的排程。 如果您尚未定義排程，則預設為 [無排程時間]。
4. 尋找 [依度量調整規模] 區段，然後選取 [CPU]。
5. 現在您可以設定角色執行個體的最小和最大範圍、目標 CPU 使用量 (以觸發相應增加)，以及向上和向下調整的執行個體數目。

![根據 cpu 負載調整雲端服務角色][cpu_scale]

> [!TIP]
> 每當您看到 ![][tip_icon]，請將滑鼠移向它，您就可以取得特定設定執行作業的相關說明。

## <a name="automatic-scale---queue"></a>自動調整 - 佇列
此模式會自動調整佇列中的訊息數目高於或低於指定的臨界值。 在這種情況會建立或刪除角色執行個體。

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中，按一下 [雲端服務]，然後按一下雲端服務的名稱以開啟儀表板。
   
   > [!TIP]
   > 如果您沒有看到您的雲端服務，您可能需要從 [生產] 變更為 [預備]，反之亦然。

2. 按一下 [調整] 。
3. 尋找 [依度量調整規模] 區段，然後選取 [佇列]。
4. 現在您可以設定角色執行個體的最小和最大範圍、每個執行個體要處理的佇列和佇列訊息數目，以及相應增加和減少的執行個體數目。

![根據訊息佇列調整雲端服務角色][queue_scale]

> [!TIP]
> 每當您看到 ![][tip_icon]，請將滑鼠移向它，您就可以取得特定設定執行作業的相關說明。

## <a name="scale-linked-resources"></a>調整連結的資源
通常，當您調整角色時，連應用程式所使用的資料庫也一起調整會比較好。 如果您將資料庫連結至雲端服務，您可以按一下適當的連結來存取該資源的調整設定。

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中，按一下 [雲端服務]，然後按一下雲端服務的名稱以開啟儀表板。
   
   > [!TIP]
   > 如果您沒有看到您的雲端服務，您可能需要從 [生產] 變更為 [預備]，反之亦然。

2. 按一下 [調整] 。
3. 尋找 [連結的資源] 區段，然後按一下 [管理這個資料庫的調整規模]。
   
   > [!NOTE]
   > 如果您沒有看到 [連結的資源] 區段，您可能沒有任何連結的資源。

![][linked_resource]

[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
