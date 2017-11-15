---
title: "在 Azure 中使用巢狀虛擬化來疑難排解問題 Azure VM | Microsoft Docs"
description: "如何在 Azure 中使用巢狀虛擬化來疑難排解問題 Azure VM"
services: virtual-machines-windows
documentationcenter: 
author: glimoli
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/06/2017
ms.author: genli
ms.openlocfilehash: 35f52af5fbf0c945a766f5e5431c885d91df546a
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>在 Azure 中使用巢狀虛擬化來疑難排解問題 Azure VM

本文將說明如何在 Microsoft Azure 中建立巢狀虛擬化環境，以便您可以在 Hyper-V 主機 (復原 VM) 上掛接有問題 VM 的磁碟，以進行疑難排解。

## <a name="prerequisite"></a>必要條件

若要掛接有問題的 VM，「復原 VM」必須符合下列必要條件：

-   「復原 VM」必須與問題 VM 處於相同的位置。

-   「復原 VM」必須與問題 VM 處於相同的資元群組。

-   「復原 VM」必須使用與問題 VM 相同類型的儲存體帳戶 (標準或進階)。

## <a name="step-1-create-a-recovery-vm-and-install-hyper-v-role"></a>步驟 1：建立復原 VM 並安裝 Hyper-V 角色

1.  建立新的「復原 VM」：

    -  作業系統：Windows Server 2016 資料中心

    -  大小：支援巢狀虛擬化的任何 V3 系列 (至少具有兩個核心)。 如需詳細資訊，請參閱[新 Dv3 和 Ev3 VM 大小簡介](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)。

    -  與問題 VM 相同的位置、儲存體帳戶，以及資源群組。

    -  選取與問題 VM 相同的儲存體類型 (標準或進階)。

2.  建立「復原 VM」之後，會建立「復原 VM」的遠端桌面。

3.  在伺服器管理員中，選取 [管理] > [新增角色及功能]。

4.  在 [安裝類型] 區段中，選取 [角色型或功能型安裝]。

5.  在 [選取目的地伺服器] 區段中，確定已選取「復原 VM」。

6.  選取 [Hyper-V 角色] > [新增功能]。

7.  在 [功能] 區段上選取 [下一步]。

8.  如果提供虛擬交換器，請選取它。 否則，請選取 [下一步]。

9.  在 [移轉] 區段中，選取 [下一步]

10. 在 [預設存放區] 區段中，選取 [下一步]。

11. 視需要勾選方塊，以自動重新啟動伺服器。

12. 選取 [安裝]。

13. 允許伺服器安裝 Hyper-V 角色。 這需要幾分鐘，而且伺服器會自動重新開機。

## <a name="step-2-create-the-problem-vm-on-the-recovery-vms-hyper-v-server"></a>步驟 2：在「復原 VM」的 Hyper-V 伺服器上建立問題 VM

1.  在問題 VM 中記錄磁碟名稱，然後刪除問題 VM。 請確定您保留所有連接的磁碟。 

2.  連接問題 VM 的作業系統磁碟作為「復原 VM」的資料磁碟。

    1.  刪除問題 VM 之後，請移至「復原 VM」。

    2.  選取 [磁碟]，然後選取 [新增資料磁碟]。

    3.  選取問題 VM 的磁碟，然後選取 [儲存]。

3.  成功連接磁碟之後，會建立「復原 VM」的遠端桌面。

4.  開啟「磁碟管理」(diskmgmt.msc)。 請確定問題 VM 的磁碟已設定為 [離線]。

5.  開啟 [Hyper-V 管理員]：在 [伺服器管理員] 中，選取 [Hyper-V 角色]。 以滑鼠右鍵按一下伺服器，然後選取 [Hyper-V 管理員]。

6.  在 [Hyper-V 管理員] 中，以滑鼠右鍵按一下「復原 VM」，然後選取 [新增] > [虛擬機器] > [下一步]。

7.  輸入 VM 的名稱，然後選取 [下一步]。

8.  選取 [第 1 代]。

9.  將啟動記憶體設定為 1024 MB 以上。

10. 如果適用，請選取已建立的 Hyper-V 網路交換器。 否則，請移至下一頁。

11. 選取 [稍後連接虛擬硬碟]。

    ![「稍後連接虛擬硬碟」選項的圖片](./media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. 建立 VM 後，選取 [完成]。

13. 以滑鼠右鍵按一下您建立的 VM，然後選取 [設定]。

14. 選取 [IDE 控制器 0]，選取 [硬碟]，然後按一下 [新增]。

    ![新增硬碟的圖片](./media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. 在 [實體硬碟] 中，選取您連接至 Azure VM 之問題 VM 的磁碟。 如果看不到任何列出的磁碟，請使用磁碟管理檢查磁碟是否設定為離線。

    ![掛接磁碟的圖片](./media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. 選取 [套用]，然後選取 [確定]。

18. 按兩下 VM，並加以啟動。

19. 現在，您可以將 VM 作為內部部署 VM 進行運作。 可以按照任何您所需要的疑難排解步驟進行。

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>步驟 3：在 Azure 中重新建立您的 Azure VM

1.  讓 VM 恢復上線之後，在 Hyper-V 管理員中關閉 VM。

2.  移至 [Azure 入口網站](https://portal.azure.com)，並選取 [復原 VM] > [磁碟]，複製磁碟的名稱。 您將會在後續步驟中使用此名稱。 將固定式磁碟從復原 VM 中斷連結。

3.  移至 [所有資源]，搜尋磁碟名稱，然後選取磁碟。

     ![關於搜尋磁碟的映像](./media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. 按一下 [建立 VM]。

     ![關於從磁碟建立 VM 的映像](./media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

您也可以使用 Azure PowerShell，從磁碟建立 VM。 如需詳細資訊，請參閱[使用 PowerShell 從現有磁碟建立新的 VM](create-vm-specialized.md#create-the-new-vm)。 

## <a name="next-steps"></a>後續步驟

如果連接至 VM 時發生問題，請參閱[針對 Azure VM 的 RDP 連接進行疑難排解](troubleshoot-rdp-connection.md)。 如果存取 VM 上執行的應用程式時發生問題，請參閱[針對 Windows VM 上的應用程式連線問題進行疑難排解](troubleshoot-app-connection.md)。
