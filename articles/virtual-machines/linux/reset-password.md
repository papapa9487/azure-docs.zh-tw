---
title: "如何在 Azure VM 上重設本機 Linux 密碼 | Microsoft Docs"
description: "介紹在 Azure VM 上重設本機 Linux 密碼的步驟"
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: delhan
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: bd48128a078821b7a4baa02d5d7ceecc6de99608
ms.contentlocale: zh-tw
ms.lasthandoff: 08/11/2017

---

# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>如何在 Azure VM 上重設本機 Linux 密碼

本文介紹數種方法來重設本機 Linux 虛擬機器 (VM) 的密碼。 如果使用者帳戶已過期，或您只要建立新帳戶，您可以使用下列方法來建立新的本機管理帳戶並重新獲得 VM 的存取權。

## <a name="symptoms"></a>徵兆

您無法登入 VM，並收到一則訊息，指出您所使用的密碼不正確。 此外，您無法使用 VMAgent 來重設您在 Azure 入口網站上的密碼。 

## <a name="manual-password-reset-procedure"></a>手動密碼重設程序

1.  刪除 VM，並保留已連接的磁碟。

2.  將 OS 磁碟機作為資料磁碟連接到相同位置中另一部暫時的 VM。

3.  在暫時的 VM 上，執行下列 SSH 命令以成為進階使用者。


    ~~~~
    sudo su
    ~~~~

4.  執行 **fdisk -l** 或查看系統記錄，以尋找最新連接的磁碟。 找出要掛接的磁碟機名稱。 然後在暫時的 VM 上，尋找相關記錄檔。

    ~~~~
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ~~~~

    以下是 grep 命令的範例輸出：

    ~~~~
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ~~~~

5.  建立名為 **tempmount** 的掛接點。

    ~~~~
    mkdir /tempmount
    ~~~~

6.  在掛接點上掛接 OS 磁碟。 您通常需要掛接 sdc1 或 sdc2。 這將取決於已中斷電腦磁碟之 /etc 目錄中裝載的磁碟分割。

    ~~~~
    mount /dev/sdc1 /tempmount
    ~~~~

7.  執行備份，然後再進行任何變更：

    ~~~~
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ~~~~

8.  重設您所需的使用者密碼：

    ~~~~
    passwd <<USER>> 
    ~~~~

9.  將修改過的檔案移至已中斷電腦磁碟上的正確位置。

    ~~~~
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    
10. Go back to the root and unmount the disk.

    ~~~~
    cd / umount /tempmount
    ~~~~

11. Detach the disk from the management portal.

12. Recreate the VM.

## Next steps

* [Troubleshoot Azure VM by attaching OS disk to another Azure VM](http://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: How to delete and re-deploy a VM from VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
