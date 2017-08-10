---
title: "關於 Azure IaaS VM 磁碟的常見問題集 (FAQ) | Microsoft Docs"
description: "關於 Azure IaaS VM 磁碟和進階磁碟 (受控和非受控) 的常見問題集"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 9e5eed35334f1b95441b8181c8e90645be78b389
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>關於 Azure IaaS VM 磁碟及受控和非受控進階磁碟的常見問題集

此文章將回答有關 Azure 受控磁碟和 Azure 進階儲存體的一些常見問題。

## <a name="managed-disks"></a>受控磁碟

**何謂 Azure 受控磁碟？**

受控磁碟是可以為您管理儲存體帳戶而簡化 Azure IaaS VM 磁碟管理的一項功能。 如需詳細資訊，請參閱[受控磁碟概觀](storage-managed-disks-overview.md)。

**如果我從現有的 VHD (大小為 80 GB) 建立標準受控磁碟，需要多少費用？**

從 80 GB VHD 建立的標準受控磁碟會被視為下一個可用的標準磁碟大小 (S10 磁碟)。 將根據 S10 磁碟定價向您收費。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage)。

**標準受控磁碟有任何交易成本嗎？**

是。 我們會根據每一筆交易向您收費。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage)。

**對於標準受控磁碟，收費是依據磁碟上的資料實際大小，還是磁碟的佈建容量？**

收費是依據磁碟的佈建容量。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage)。

**進階受控磁碟和非受控磁碟的價格有何不同？**

進階受控磁碟與進階非受控磁碟的價格相同。

**我是否可以變更受控磁碟的儲存體帳戶類型 (標準或進階)？**

是。 您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 來變更受控磁碟的儲存體帳戶類型。

**是否有方法可以將受控磁碟複製或匯出至私人儲存體帳戶？**

是。 您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 來匯出受控磁碟。

**我是否可以使用 Azure 儲存體帳戶中的 VHD 檔案，透過不同的訂用帳戶來建立受控磁碟？**

否。

**我是否可以使用 Azure 儲存體帳戶中的 VHD 檔案在不同的區域中建立受控磁碟？**

否。

**客戶使用受控磁碟時是否有任何規模限制？**

受控磁碟沒有儲存體帳戶方面的限制。 不過，依預設，每一訂用帳戶的受控磁碟數目限制是 2,000 個。 您可以連絡支援人員來增加此數目。

**我是否可以建立受控磁碟的增量快照集？**

否。 目前的快照集功能會建立受控磁碟的完整複本。 不過，我們已規劃在未來支援增量快照集。

**可用性設定組中的 VM 是否可以由受控和非受控磁碟混合組成？**

否。 可用性設定組中的 VM 必須全部使用受控磁碟，或全部使用非受控磁碟。 當您建立可用性設定組時，可以選擇想要使用的磁碟類型。

**受控磁碟是否為 Azure 入口網站中的預設選項？**

目前不是，但未來會變成預設值。

**我是否可以建立空的受控磁碟？**

是。 您可以建立空的磁碟。 受控磁碟可在 VM 外獨立建立，例如，不連結至 VM。

**使用受控磁碟的可用性設定組支援的容錯網域計數為何？**

根據使用受控磁碟的可用性設定組所在區域，支援的容錯網域計數為 2 或 3。

**如何設定診斷的標準儲存體帳戶？**

您需要為 VM 診斷設定私人儲存體帳戶。 在未來，我們規劃也將診斷切換至受控磁碟。

**受控磁碟適用何種角色型存取控制支援？**

受控磁碟支援三個主要預設角色︰

* 擁有者：可以管理所有項目，包括存取
* 參與者：可以管理存取以外的所有項目
* 讀取者：可以檢視所有項目，但是無法進行變更

**是否有方法可以將受控磁碟複製或匯出至私人儲存體帳戶？**

您可以取得受控磁碟的唯讀共用存取簽章 URI，並使用它將內容複製到私人儲存體帳戶或內部部署儲存體。

**我是否可以建立受控磁碟的複本？**

客戶可以建立受控磁碟的快照集，然後使用快照集建立另一個受控磁碟。

**是否仍然支援非受控磁碟？**

是。 我們支援受控磁碟和非受控磁碟。 我們建議您使用受控磁碟來處理新的工作負載，並將您目前的工作負載移轉至受控磁碟。


**如果我建立大小為 128 GB 的磁碟，然後將大小增加至 130 GB，我是否必須支付下一個磁碟大小 (512 GB) 的費用？**

是。

**我是否可以建立本地備援儲存體、異地備援儲存體和區域備援儲存體受控磁碟？**

Azure 受控磁碟目前只支援本地備援儲存體受控磁碟。

**我是否可以壓縮受控磁碟或縮減其大小？**

否。 目前不受支援此功能。 

**當特製化 (不是透過使用系統準備工具或一般化所建立) 作業系統磁碟用來佈建 VM 時，我是否可以變更電腦名稱屬性？**

否。 您無法更新電腦名稱屬性。 新的 VM 將從其父 VM 繼承它，並用來建立作業系統磁碟。 

**哪裡可以找到 Azure Resource Manager 範本範例以建立具有受控磁碟的 VM**
* [使用受控磁碟的範本清單](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

## <a name="managed-disks-and-storage-service-encryption"></a>受控磁碟和儲存體服務加密 

**當我建立受控磁碟時，依預設是否啟用 Azure 儲存體服務加密？**

是。

**誰負責管理加密金鑰？**

Microsoft 負責管理加密金鑰。

**我是否可以停用受控磁碟的儲存體服務加密？**

否。

**儲存體服務加密是否僅供特定地區使用？**

否。 它在受控磁碟可以使用的區域中都有提供。 受控磁碟在所有公開區域和德國都有提供。

**如何查看我的受控磁碟是否加密？**

您可以從 Azure 入口網站、Azure CLI 和 PowerShell 找出建立受控磁碟的時間。 如果時間是在 2017 年 6 月 9 日之後，那麼您的磁碟是加密的。 

**如何加密我在 2017 年 6 月 10 日之前建立的現有磁碟？**

自 2017 年 6 月 10 日起，系統會自動將寫入現有受控磁碟的新資料加密。 我們也計劃對現有資料進行加密，加密將以非同步方式在背景處理。 如果您現在必須加密現有資料，請建立一份磁碟複本。 新的磁碟將會加密。

* [使用 Azure CLI 複製受控磁碟](https://docs.microsoft.com/en-us/azure/storage/scripts/storage-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription?toc=%2fcli%2fmodule%2ftoc.json)
* [使用 PowerShell 複製受控磁碟](https://docs.microsoft.com/en-us/azure/storage/scripts/storage-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription?toc=%2fcli%2fmodule%2ftoc.json)

**受管理的快照集和映像是否加密？**

是。 2017 年 6 月 9 日後建立之所有受管理的快照集和映像都將自動加密。 

**我是否可以將具有非受控磁碟 (位於之前已加密的儲存體帳戶上) 的 VM 轉換為受控磁碟？**

是

**從受控磁碟或快照集匯出的 VHD 是否也會加密？**

否。 但是，如果將 VHD 從加密的受控磁碟或快照集匯出到加密儲存體帳戶，則會將它加密。 

## <a name="premium-disks-managed-and-unmanaged"></a>進階磁碟：受控和非受控

**如果 VM 使用的大小系列支援進階儲存體，例如 DSv2，我是否可以同時連結進階和標準資料磁碟？** 

是。

**我是否可以同時將進階和標準資料磁碟連結至不支援進階儲存體的大小系列？例如 D、Dv2、G 或 F 系列？**

否。 只有當 VM 未使用支援進階儲存體的大小系列時，您才能將標準資料磁碟連結至 VM。

**如果我從現有的 VHD (大小為 80 GB) 建立進階資料磁碟，需要多少費用？**

從 80 GB VHD 建立的進階資料磁碟會被視為下一個可用的進階磁碟大小 (P10 磁碟)。 將根據 P10 磁碟定價向您收費。

**使用進階儲存體是否有交易成本？**

依 IOPS 和輸送量的特定限制而佈建的每個磁碟大小，都有固定成本。 其他成本包括輸出頻寬和快照集容量 (如果適用的話)。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage)。

**我可以從磁碟快取取得的 IOPS 和輸送量有何限制？**

DS 系列的快取和本機 SSD 合併限制是每個核心 4,000 IOPS，以及每個核心每秒 33 MB。 GS 系列提供每個核心 5,000 IOPS，以及每個核心每秒 50 MB。

**受控磁碟 VM 是否支援本機 SSD？**

本機 SSD 是受控磁碟 VM 隨附的暫時儲存體。 暫時儲存體不需額外的成本。 建議您不要使用本機 SSD 來儲存應用程式資料，因為它不會保留在 Azure Blob 儲存體中。

**在進階磁碟上使用 TRIM 是否有任何影響？**

在進階或標準磁碟的 Azure 磁碟上使用 TRIM 並無任何不妥。

## <a name="new-disk-sizes-managed-and-unmanaged"></a>新磁碟大小：受控和非受控

**作業系統和資料磁碟支援的最大磁碟大小是多少？**

Azure 針對作業系統磁碟所支援的磁碟分割類型是主開機記錄 (MBR)。 MBR 格式支援的磁碟大小上限為 2 TB。 Azure 針對作業系統磁碟支援的大小上限為 2 TB。 Azure 支援最大 4 TB 的資料磁碟。 

**支援的分頁 Blob 大小上限是多少？**

Azure 支援的分頁 Blob 大小上限是 8 TB (8,191 GB)。 我們不支援將大於 4 TB (4,095 GB) 的分頁 Blob 連結到 VM 作為資料或作業系統磁碟。

**我是否需要使用新版 Azure 工具來建立磁碟、連結磁碟、調整磁碟大小及上傳大於 1 TB 的磁碟？**

您不需要升級現有的 Azure工具來建立磁碟、連結磁碟或調整大於1 TB 的磁碟大小。 若要將 VHD 檔案從內部部署直接上傳到 Azure 作為分頁 Blob 或非受控磁碟，您需要使用最新的工具集：

|Azure 工具      | 支援的版本                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | 版本號碼 4.1.0：2017 年 6 月發行或更新版本|
|Azure CLI v1     | 版本號碼 0.10.13：2017 年 5 月發行或更新版本|
|AzCopy           | 版本號碼 6.1.0：2017 年 6 月發行或更新版本|

即將支援 Azure CLI v2 和 Azure 儲存體總管。 

**針對非受控磁碟或分頁 Blob，是否支援 P4 和 P6 磁碟大小？**

否。 只有受控磁碟才支援 P4 (32 GB) 和 P6 (64 GB) 磁碟大小。 即將支援非受控磁碟和分頁 Blob。

**如果小於 64 GB 的現有進階受控磁碟，是在啟用小型磁碟 (2017 年 6 月 15 日前後) 之前建立，該如何計費？**

根據 P10 定價層，小於 64 GB 的現有小型進階磁碟繼續計費。 

**我要如何將小於 64 GB 的小型進階磁碟的磁碟層，從 P10 切換到 P4 或 P6？**

您可以為小型磁碟建立快照集，然後建立一個磁碟，根據佈建大小自動將定價層切換為 P4 或 P6。 


## <a name="what-if-my-question-isnt-answered-here"></a>如果這裡沒有解答我的問題該怎麼辦？

如果這裡未列出您的問題，請告訴我們，我們將協助您找到答案。 您可以在此文章結尾處將問題張貼在註解中。 若要就此文章與 Azure 儲存體小組和其他社群成員互動，請使用 MSDN [Azure 儲存體論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)。

若要提出功能要求，請將要求和想法提交到 [Azure 儲存體意見反應論壇](https://feedback.azure.com/forums/217298-storage)。

