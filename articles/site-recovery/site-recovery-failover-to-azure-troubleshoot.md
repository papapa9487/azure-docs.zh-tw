---
title: "針對容錯移轉至 Azure 失敗進行疑難排解 | Microsoft Docs"
description: "本文說明如何針對容錯移轉至 Azure 的常見錯誤進行疑難排解"
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: pratshar
ms.openlocfilehash: 5e1f9a0298c2abd542d7687778716f644a1d0a47
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>針對將虛擬機器容錯移轉至 Azure 時的錯誤進行疑難排解
將虛擬機器容錯移轉至 Azure 時，您可能會收到下列錯誤。 若要進行疑難排解，請使用針對每種錯誤狀況所述的步驟。


## <a name="failover-failed-with-error-id-28031"></a>容錯移轉失敗，錯誤識別碼為 28031

Site Recovery 無法在 Azure 中建立已容錯移轉的虛擬機器。 它可能會因為下列其中一個原因而發生：

* 沒有足夠的配額可用來建立虛擬機器：您可以移至 [訂用帳戶] -> [使用量 + 配額]，檢查可用的配額。 您可以開啟[新的支援要求](http://aka.ms/getazuresupport)來增加配額。
     
* 您嘗試在相同的可用性設定組中容錯移轉不同大小系列的虛擬機器。 確保您為相同可用性設定組中的所有虛擬機器，選擇相同的大小系列。 移至虛擬機器的 [計算] 和 [網路] 來變更大小，然後重試容錯移轉。
  
* 有訂用帳戶原則可避免建立虛擬機器。 將此原則變更為允許建立虛擬機器，然後重試容錯移轉。 

## <a name="failover-failed-with-error-id-28092"></a>容錯移轉失敗，錯誤識別碼為 28092

Site Recovery 無法針對已容錯移轉的虛擬機器建立網路介面。 確定您有足夠的配額可用來在訂用帳戶中建立網路介面。 您可以移至 [訂用帳戶] -> [使用量 + 配額]，檢查可用的配額。 您可以開啟[新的支援要求](http://aka.ms/getazuresupport)來增加配額。 如果您有足夠的配額，這可能是間歇性問題，請重試一次此作業。 如果即使在重試之後，問題仍然存在，則在本文的結尾留言。  

## <a name="failover-failed-with-error-id-70038"></a>容錯移轉失敗，錯誤識別碼為 70038

Site Recovery 無法在 Azure 中建立已容錯移轉的傳統虛擬機器。 其發生原因可能是：

* 其中一個資源 (例如建立虛擬機器所需的虛擬網路) 不存在。 建立如虛擬機器的 [計算] 和 [網路] 設定之下提供的虛擬網路，或將此設定修改為已經存在的虛擬網路，然後重試容錯移轉。 


## <a name="next-steps"></a>後續步驟

如果您需要更多說明，則將您的查詢張貼在 [Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)上或在本文件的結尾留言。 我們有一個能夠協助您的使用中社群。