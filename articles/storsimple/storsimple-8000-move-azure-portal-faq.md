---
title: "從傳統入口網站移動到 Azure 入口網站常見問題集 | Microsoft Docs"
description: "提供將 StorSimple 裝置從傳統入口網站移動到 Azure 入口網站相關常見問題的解答。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 06e8d99aa2ad4eb11e594a729c6dab39d5cd1eb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>將 StorSimple 裝置管理員服務從傳統入口網站移動到 Azure 入口網站：常見問題集 (FAQ)

## <a name="overview"></a>概觀

以下是當您將在 Azure 傳統入口網站中執行的 StorSimple 裝置管理員服務移動到 Azure 入口網站時，可能會有的問題和解答。

問題和解答可分為下列幾個類別：

* 移動 StorSimple 裝置管理員服務
* 移動儲存體帳戶
* 使用以 Azure Resource Manager 為基礎的 Cmdlet
* 使用 StorSimple 資料管理員服務
* 其他資訊

## <a name="moving-storsimple-device-manager-service"></a>移動 StorSimple 裝置管理員服務

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>移動到 Azure 入口網站後，我還可以在傳統入口網站中建立 StorSimple Manager 服務嗎？

否。 將 StorSimple Manager 服務移轉到 Azure 入口網站後，您就無法在傳統入口網站中建立新服務。 此外，您也不能透過傳統入口網站管理裝置。 如需詳細資訊，請前往[將您的服務移動到 Azure 入口網站](storsimple-8000-manage-service.md#move-a-service-to-azure-portal)。

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>在傳統入口網站中有多個執行中的 StorSimple Manager。 我可以選擇將哪一個移動到 Azure 入口網站嗎？

否。 您不能選擇將哪一個 StorSimple Manager 移動到 Azure 入口網站。 您訂用帳戶下的所有 StorSimple Manager 都需要移動。


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>我已將開始將服務移轉到新版 Azure 入口網站了。 我應該刪除傳統入口網站的 StorSimple Manager 嗎？ 

否。 請勿嘗試刪除任何從傳統入口網站移動的服務。 請等候移轉完成，待所有 StorSimple Manager 都移動到新版入口網站後，您將不需要刪除傳統入口網站中的任何服務。 傳統入口網站最終將遭到取代。

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>我可以重新命名 Azure 入口網站中的 StorSimple 裝置管理員服務嗎？

否。 在 Azure 入口網站中建立服務之後，您就無法變更服務名稱。 這也適用於其他實體，例如裝置、磁碟區、磁碟區容器和備份原則。

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>我可以利用 Azure Resource Manager 部署模型建立 8010/8020 StorSimple 雲端設備嗎？

是。 您可以利用 Azure Resource Manager 部署模型建立 8010/8020 StorSimple 雲端設備。 這些雲端設備的基礎 VM 也會採用 Resource Manager 部署模型。

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>當我們將訂用帳戶移轉到 Azure 入口網站時，StorSimple 雲端設備的基礎 VM 也會移轉到 Azure Resource Management 部署模型嗎？

StorSimple 服務的移動與 StorSimple 雲端設備的 VM 管理無關。 您現在甚至可以完全管理傳統入口網站和 Azure 入口網站兩者中的 StorSimple 雲端設備 VM。

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>我可以從 Azure 入口網站管理現有的傳統 8010/8020 StorSimple 雲端設備嗎？

是。 您可以從 Azure 入口網站管理與現有 8010/8020 雲端設備相關的 VM。

如果您已建立執行 Update 3.0 或更新版本的 StorSimple 雲端設備模型 8010/8020，將服務移動到新 Azure 入口網站將不會對它們造成任何影響。 您應該可以完全雲端設備而不會遭遇任何問題。 

如果您的雲端設備在傳統入口網站中執行 Update 3.0 之前的版本，您只能使用有限的功能。 如需詳細資訊，請移至[執行 Update 3 先前版本之裝置不支援的作業](storsimple-8000-manage-service.md#move-a-service-to-azure-portal)。

您不能更新雲端設備。 請使用最新版本的軟體建立新雲端設備，然後將現有的磁碟區容器容錯移轉到建立的新雲端設備。 如需詳細資訊，請移至[容錯移轉到雲端設備](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>我的 StorSimple 8000 系列裝置執行 Update 2.0。 我已經將服務移轉到新 Azure 入口網站。 我的裝置連線成功，但是我似乎不能完全管理裝置。 我該如何解決這個行為？

新版 Azure 入口網站僅支援執行 Update 3.0 和更新版本的 StorSimple 裝置。 如果您的裝置執行 Update 2.0，這部裝置只能使用有限的功能。 如需詳細資訊，請移至[執行 Update 3 先前版本之裝置不支援的作業](storsimple-8000-manage-service.md#move-a-service-to-azure-portal)。

若要完全管理裝置，請在裝置上安裝最新版本的更新。 如需詳細資訊，請移至[安裝 Update 5](storsimple-8000-install-update-5.md)。

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>我剛剛將我的 StorSimple Manager 服務移動到 Azure 入口網站。 我看到一些有關裝置的警示。 這個行為與裝置移動有關嗎？

否。 移動本身應該不會引發錯誤或警示。 請遵循警示建議解決警示。

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>我使用 StorSimple 5000/7000 系列裝置。 Azure 入口網站也支援這些裝置嗎？

否。 Azure 入口網站不支援 StorSimple 5000/7000 系列裝置。

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>我計劃將資料從 StorSimple 5000/7000 系列裝置移轉到 StorSimple 8000 系列裝置。 將服務移動到 Azure 入口網站會對我的資料移轉造成什麼影響？ 

您可以將資料從 StorSimple 5000/7000 系列裝置移轉到在 Azure 入口網站中執行的 StorSimple 8000 系列裝置。 若要將資料從 5000/7000 系列移轉到 8000 系列，您必須[向 Microsoft 支援服務登錄支援票證](storsimple-8000-contact-microsoft-support.md)。


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>移動訂用帳戶、儲存體帳戶、資源群組

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>我可以在 Azure 入口網站中將 StorSimple 裝置管理員從某個訂用帳戶移動到另一個訂用帳戶嗎？

否。 我們不支援將 StorSimple 裝置管理員服務從某個訂用帳戶移動到另一個訂用帳戶。 您可以執行包括下列步驟的手動程序：

* 移轉 StorSimple 裝置的資料。
* 執行將裝置重設成出廠預設值，這會刪除裝置上的任何本機資料。
* 為裝置向 StorSimple 裝置管理員服務註冊新的訂用帳戶。
* 將資料移轉回裝置。

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>我需要將儲存體帳戶移轉到 Azure Resource Manager 部署模型嗎？

否。 您可以從 Azure 入口網站完全管理傳統儲存體帳戶。 當您將 StorSimple 服務移動到 Azure 入口網站時，儲存體帳戶將會如往常一樣繼續運作。

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>將服務移轉到 Azure 入口網站時，儲存體帳戶會發生什麼事？

服務的移動與儲存體帳戶的管理無關。 您可以在 Azure 入口網站中完全管理傳統和 Azure Resource Manager 儲存體帳戶。 一旦將服務移動到 Azure 入口網站後，您的裝置應該能繼續與這個儲存體帳戶搭配運作，不會影響您的資料。

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>我可以將 StorSimple 裝置管理員從某一個資源群組移轉到另一個資源群組嗎？

否。 您無法將以某個資源群組建立的 StorSimple 裝置管理員移動到另一個資源群組。

## <a name="using-azure-resource-manager-based-cmdlets"></a>使用以 Azure Resource Manager 為基礎的 Cmdlet

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>我已經移動到新版 Azure 入口網站。 現在，我以 Azure 傳統部署模型為基礎的 PowerShell Cmdlet 都失效了嗎？ 我需要做什麼事嗎？

Azure 入口網站不支援現有的 Azure 傳統部署模型 PowerShell Cmdlet。 請更新指令碼，以透過 Azure Resource Manager 管理您的裝置。 如需更新指令碼的詳細資訊，請移至[新版 Azure 入口網站的範例](https://github.com/anoobbacker/storsimpledevicemgmttools)。

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>我剛剛移動到 Azure 入口網站，所以需要變換服務資料加密金鑰。 這個選項在 Azure 入口網站的哪裡？

變換服務資料加密金鑰的選項不在 Azure 入口網站中。 如需在 Azure 入口網站中執行這項變換作業的詳細資訊，請移至[變更服務資料加密金鑰](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)。

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>我在 StorSimple 裝置上使用適用於 StorSimple 的 Windows PowerShell Cmdlet 來執行擷取支援套件之類的作業。 移動到新版 Azure 入口網站會對這些 Cmdlet 造成影響嗎？

否。 在將服務移動到新版 Azure 入口網站的前提下，與內部部署 StorSimple 裝置相關之適用於 StorSimple 的 Windows PowerShell Cmdlet 應該不會受到影響 (裝置本身不會受到移動影響)。 您可以繼續使用這些 Cmdlet 來建立支援套裝而不會遭遇任何問題，即使在 Azure 入口網站中也是如此。 只有 Azure 傳統部署模型 PowerShell Cmdlet 會受到移動影響。

## <a name="moving-storsimple-data-manager-service"></a>移動 StorSimple 資料管理員服務

### <a name="i-am-using-storsimple-data-manager-service-how-should-i-proceed-with-this-move"></a>我使用 StorSimple 資料管理員服務。 我該如何進行移動？

如果您使用 StorSimple 資料管理員服務，必須先將 StorSimple 資料管理員移動到 Azure 入口網站。 移動完成後，請在 Azure 入口網站建立新的 StorSimple 資料管理員。 在移動之前建立 StorSimple 資料管理員將無法運作。

如需移轉 StorSimple 裝置管理員服務的詳細資訊，請移至[將您的服務移動到 Azure 入口網站](storsimple-8000-manage-service.md#move-a-service-to-azure-portal)。 如需建立 StorSimple 資料管理員的詳細資訊，請移至[建立 StorSimple 資料管理員服務](storsimple-data-manager-ui.md)。

## <a name="miscellaneous"></a>其他資訊

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>我為我的 8000 系列裝置執行 StorSimple Snapshot Manager。 移動到 Azure 入口網站會對 StorSimple Snapshot Manager 造成什麼影響嗎？

否。 將服務移動到 Azure 入口網站不會對 StorSimple Snapshot Manager 造成任何影響。 您的裝置和 StorSimple Snapshot Manager 將會如往常一樣繼續運作。

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>我可以為我的 StorSimple 裝置、磁碟區容器或磁碟區重新命名嗎？

否。 您不能在 Azure 入口網站中為裝置、磁碟區、磁碟區容器或備份原則重新命名。

## <a name="next-steps"></a>後續步驟

逐一了解將 [StorSimple 裝置管理員服務移動到 Azure 入口網站](storsimple-8000-manage-service.md#move-a-service-to-azure-portal)的每個步驟。



