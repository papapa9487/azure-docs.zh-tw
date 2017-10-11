---
title: "關於 Microsoft Azure Linux VM 的磁碟和 VHD | Microsoft Docs"
description: "了解 Azure 中 Linux 虛擬機器的磁碟和 VHD 的基本知識。"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 7be8dd52-98f7-4187-9b78-55197915bc9b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: robinsh
ms.openlocfilehash: be5f09af275142590ec6ade02562e914d5726e08
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="about-disks-and-vhds-for-azure-linux-vms"></a>關於 Azure Linux VM 的磁碟和 VHD
就像任何其他電腦，Azure 中的虛擬機器會使用磁碟做為儲存作業系統、應用程式和資料的位置。 所有 Azure 虛擬機器都至少有兩個磁碟 – Linux 作業系統磁碟和暫存磁碟。 作業系統磁碟是由映像建立，且作業系統磁碟與該映像，實際上都是儲存在 Azure 儲存體帳戶的虛擬硬碟 (VHD)。 虛擬機器也可以有一或多個資料磁碟，而這些磁碟也會儲存成 VHD。 

在本文中，我們將討論磁碟的不同用法，接著討論您可以建立和使用的不同磁碟類型。 本文也適用於 [Windows 虛擬機器](../windows/about-disks-and-vhds.md)。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>VM 使用的磁碟

讓我們看看 VM 如何使用磁碟。

## <a name="operating-system-disk"></a>作業系統磁碟
每個虛擬機器都有一個連接的作業系統磁碟。 它會註冊為 SATA 磁碟機，並預設標示為 /dev/sda。 此磁碟具有 2048 GB 的最大容量。 

## <a name="temporary-disk"></a>暫存磁碟
每個 VM 都包含一個暫存磁碟。 暫存磁碟為應用程式和處理程序提供短期的儲存空間，且僅供用來儲存分頁檔之類的資料。 暫存磁碟上的資料可能會在[維護事件](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime)期間或當您[重新佈署 VM](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 時遺失。 在 VM 的標準重新開機期間，暫存磁碟上的資料會保留。

在 Linux 虛擬機器上，這個磁碟通常是 **/dev/sdb**，並且會由「Azure Linux 代理程式」將它格式化並裝載至 **/mnt/**。 暫存磁碟的大小會依據虛擬機器的大小而改變。 如需詳細資訊，請參閱 [Linux 虛擬機器的大小](../windows/sizes.md)。

如需有關 Azure 如何使用暫存磁碟的詳細資訊，請參閱 [Understanding the temporary drive on Microsoft Azure Virtual Machines (了解 Microsoft Azure 虛擬機器上的暫存磁碟機)](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>資料磁碟
資料磁碟是連接至虛擬機器的 VHD，用來儲存應用程式資料或其他您需要保留的資料。 資料磁碟註冊為 SCSI 磁碟機，並以您選擇的字母標示。 每個資料磁碟具有 4095 GB 的最大容量。 虛擬機器的大小會決定您可以連接之磁碟的數量，以及您可以用來裝載磁碟的儲存體類型。

> [!NOTE]
> 如需有關虛擬機器容量的詳細資訊，請參閱 [Linux 虛擬機器的大小](../windows/sizes.md)。
> 

當您從映像建立虛擬機器時，Azure 會建立作業系統磁碟。 如果您使用包含資料磁碟映像時，Azure 建立虛擬機器時也會建立資料磁碟。 若沒有，則您可以建立虛擬機器後再新增資料磁碟。

您可以隨時將資料磁碟「連接」  到虛擬機器來將該磁碟新增到虛擬機器中。 您可以使用您已上傳或複製到儲存體帳戶的 VHD，或是 Azure 為您建立的 VHD。 附加資料磁碟時，會透過在 VHD 上加上「租約」，來將 VHD 檔案與 VM 關聯，如此一來，當它仍處於附加狀態時，就無法將它從儲存體中刪除。

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>疑難排解
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>後續步驟
* [連接磁碟](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 來為您的 VM 新增額外的儲存空間。
* [設定軟體 RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 以提供備援。
* [擷取 Linux 虛擬機器](./classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)以便快速部署額外的 VM。

