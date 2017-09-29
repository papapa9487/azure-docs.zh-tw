---
title: "如何重設 Azure Windows VM 的網路介面| Microsoft Docs"
description: "示範如何重設 Azure Windows VM 的網路介面"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/26/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: b8eecb9d546d5922844ede3744d80b951334b5d3
ms.contentlocale: zh-tw
ms.lasthandoff: 09/27/2017

---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>如何重設 Azure Windows VM 的網路介面 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

停用預設的網路介面 (NIC) 或手動設定 NIC 的靜態 IP 之後，便無法連線到 Microsoft Azure Windows 虛擬機器 (VM)。 本文說明如何重設 Azure Windows VM 的網路介面，進而解決遠端連線問題。

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>重設網路介面

### <a name="for-classic-vms"></a>適用於傳統 VM

若要重設網路介面，請按照以下步驟操作：

1.  移至 [Azure 入口網站]( https://ms.portal.azure.com)。
2.  選取 [虛擬機器 (傳統)]。
3.  選取受影響的虛擬機器。
4.  選取 [IP 位址]。
5.  如果 [私人 IP 指派] 不是 [靜態]，請將它變更為 [靜態]。
6.  將 [IP 位址] 變更為子網路中可用的其他 IP 位址。
7.  選取 [儲存]。
8.  虛擬機器會重新啟動，以便對系統初始化新的 NIC。
9.  嘗試使用 RDP 連線到您的電腦。 如果成功，您可以自行決定是否將私人 IP 位址變更回原始位址， 或是維持現有設定。 

### <a name="for-vms-deployed-in-resource-group-model"></a>適用於部署在資源群組模型中的 VM

1.  移至 [Azure 入口網站]( https://ms.portal.azure.com)。
2.  選取受影響的虛擬機器。
3.  選取 [網路介面]。
4.  選取和您的電腦相關聯的網路介面
5.  選取 [IP 組態]。
6.  選取 IP。 
7.  如果 [私人 IP 指派] 不是 [靜態]，請將它變更為 [靜態]。
8.  將 [IP 位址] 變更為子網路中可用的其他 IP 位址。
9. 虛擬機器會重新啟動，以便對系統初始化新的 NIC。
10. 嘗試使用 RDP 連線到您的電腦。 如果成功，您可以自行決定是否將私人 IP 位址變更回原始位址， 或是維持現有設定。 

## <a name="delete-the-unavailable-nics"></a>刪除無法使用的 NIC
可以使用遠端桌面連線到機器之後，您必須刪除舊的 NIC 以避免潛在的問題：

1.  開啟 [裝置管理員]。
2.  選取 [檢視] > [顯示隱藏的裝置]。
3.  選取 [網路介面卡]。 
4.  檢查名稱為「Microsoft Hyper-V 網路介面卡」的介面卡。
5.  無法使用的介面卡會以灰色來顯示。以滑鼠右鍵按一下介面卡，然後選取 [解除安裝]。

    ![NIC 的影像](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > 僅解除安裝無法使用且名稱為「Microsoft Hyper-V 網路介面卡」的介面卡。 如果您解除安裝任何其他隱藏的介面卡，可能會導致其他的問題。
    >
    >

6.  現在應該已經從系統清除所有無法使用的介面卡。
