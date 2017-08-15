---
title: "Azure Cloud Shell (預覽) 限制 | Microsoft Docs"
description: "Azure Cloud Shell 限制的概觀"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 3ac234b27a0675f484018c357a65ab65049ceee0
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---

# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell 限制
Azure Cloud Shell 具有下列已知限制。

## <a name="system-state-and-persistence"></a>系統狀態和持續性
提供 Cloud Shell 工作階段的電腦只是暫時性，在工作階段閒置 20 分鐘後就會回收。 Cloud Shell 需要掛接檔案共用。 因此，您的訂用帳戶必須能夠設定儲存體資源，才可存取 Cloud Shell。 其他考量包括：
* 在掛接的儲存體中，只會保存 `$Home` 目錄或 `clouddrive` 目錄內的修改。
* 只能從您的[已指派區域](persisting-shell-storage.md#mount-a-new-clouddrive)內掛接檔案共用。
* Azure 檔案只支援本機備援儲存體和異地備援儲存體帳戶。

## <a name="user-permissions"></a>使用者權限
權限設定為沒有 sudo 存取權的一般使用者。 將不會保存 `$Home` 目錄之外的任何安裝。
雖然 `git clone` 目錄內的某些命令 (例如 `clouddrive`) 沒有適當的權限，但您的 `$Home` 目錄有適當的權限。

## <a name="browser-support"></a>瀏覽器支援
Cloud Shell 支援最新版的 Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox 及 Apple Safari。 不支援 Safari 私密瀏覽模式。

## <a name="copy-and-paste"></a>複製和貼上
因為 Ctrl+C 和 Ctrl+V 無法在 Windows 電腦上作為複製及貼上的鍵盤快速鍵運作，請改用 Ctrl+Insert 和 Shift+Insert 來複製及貼上。

也提供滑鼠右鍵的複製貼上選項，但滑鼠右鍵功能取決於瀏覽器特定的剪貼簿存取。

## <a name="editing-bashrc"></a>編輯 .bashrc
編輯 .bashrc 時需要注意，因為這樣做可能會導致在 Cloud Shell 發生未預期的錯誤。

## <a name="bashhistory"></a>.bash_history
Bash 命令的歷程記錄可能因為 Cloud Shell 工作階段中斷或並行工作階段而出現不一致的情況。

## <a name="usage-limits"></a>使用限制
Cloud Shell 主要用於互動式的使用案例。 因此，任何長時間執行而沒有互動的工作階段會在不發出警告的情況下結束。

## <a name="network-connectivity"></a>網路連線
Cloud Shell 中的任何延遲受限於本機網際網路連線，Cloud Shell 會遵循任何傳送的指示而繼續嘗試運作。

## <a name="next-steps"></a>後續步驟
[Cloud Shell 快速入門](quickstart.md)

