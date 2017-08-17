---
title: "Azure 備份︰從 Azure VM 備份復原檔案和資料夾 | Microsoft Docs"
description: "從 Azure 虛擬機器的復原點復原檔案"
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: "項目層級復原；從 Azure VM 備份檔案復原；從 Azure VM 中還原檔案"
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/20/2017
ms.author: pullabhk;markgal
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: ae7c345c11a7db25413d60ad822f16f84ca37362
ms.contentlocale: zh-tw
ms.lasthandoff: 07/21/2017

---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>從 Azure 虛擬機器備份復原檔案

Azure 備份能夠讓您從 Azure VM 備份還原 [Azure VM 和磁碟](./backup-azure-arm-restore-vms.md)。 現在本文說明如何從 Azure VM 備份復原檔案和資料夾等項目。

> [!Note]
> 這項功能可使用 Resource Manager 模式供 Azure VM 部署使用，而且受保護復原服務保存庫。
> 不支援從加密 VM 備份的檔案復原。
>

## <a name="mount-the-volume-and-copy-files"></a>掛接磁碟區和複製檔案

1. 登入 [Azure 入口網站](http://portal.Azure.com)。 尋找相關的復原服務保存庫和必要的備份項目。

2. 在 [備份項目] 刀鋒視窗中，按一下 [檔案修復]。

    ![開啟復原服務保存庫備份項目](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    [檔案復原] 刀鋒視窗隨即開啟。

    ![[檔案復原] 刀鋒視窗](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. 從 [選取復原點] 下拉式選單中，選取包含您所需檔案的復原點。 根據預設，已選取最近的復原點。

4. 按一下 [下載可執行檔] \(若為 Windows Azure VM) 或 [下載指令碼] \(若為 Linux Azure VM) 下載您要用來從復原點複製檔案的軟體。

  可執行檔/指令碼會在本機電腦和指定復原點之間建立連線。

5. 您需要有密碼才能執行下載的指令碼/可執行檔。 您可以使用所產生密碼旁邊的 [複製] 按鈕，從入口網站複製密碼。

    ![產生的密碼](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

6. 在您要復原檔案的電腦上，執行可執行檔/指令碼。 您必須以系統管理員認證來執行它。 如果您在具有限制存取的電腦上執行指令碼，請確定可存取︰

    - download.microsoft.com
    - 用於 Azure VM 備份的 Azure 端點
    - 輸出連接埠 3260

   若為 Linux，指令碼需要 'open-iscsi' 和 'lshw' 元件來連接到復原點。 如果這些不存在其所執行的電腦上，它會要求安裝相關元件的權限，並在同意下加以安裝。
   
   出現提示時，輸入從入口網站複製的密碼。 輸入有效的密碼後，指令碼會連接到復原點。
      
    ![[檔案復原] 刀鋒視窗](./media/backup-azure-restore-files-from-vm/executable-output.png)
    
   
   您可以在任何具有與備份 VM 相的 (或相容) 作業系統的電腦上執行指令碼。 請參閱[相容作業系統資料表](backup-azure-restore-files-from-vm.md#compatible-os)以查看相容的作業系統。 如果受保護的 Azure 虛擬機器使用 Windows 儲存空間 (若為 Windows Azure VM) 或 LVM/RAID 陣列 (若為 Linux VM)，則您無法在相同的虛擬機器上執行可執行檔/指令碼。 相反地，在任何其他具有相容作業系統上的電腦執行它。

### <a name="compatible-os"></a>相容的作業系統

#### <a name="for-windows"></a>若為 Windows

下表顯示伺服器和電腦作業系統之間的相容性。 當復原檔案時，您無法還原不相容作業系統之間的檔案。

|伺服器作業系統 | 相容的用戶端作業系統  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

#### <a name="for-linux"></a>若為 Linux

在 Linux 中，基本要求是指令碼在其中執行之電腦的作業系統應該支援備份 Linux VM 中出現之檔案的檔案系統。 當選取電腦以執行指令碼時，請確定它有相容的作業系統和下表中所述的版本。

|Linux 作業系統 | 版本  |
| --------------- | ---- |
| Ubuntu | 12.04 和更新版本 |
| CentOS | 6.5 和更新版本  |
| RHEL | 6.7 和更新版本 |
| Debian | 7 和更新版本 |
| Oracle Linux | 6.4 和更新版本 |

指令碼也需要 Python 和 Bash 元件來執行，並安全地連接到復原點。

|元件 | 版本  |
| --------------- | ---- |
| Bash | 4 和更新版本 |
| Python | 2.6.6 和更新版本  |


### <a name="identifying-volumes"></a>識別磁碟區

#### <a name="for-windows"></a>若為 Windows

當您執行可執行檔時，作業系統會掛接新磁碟區，並指派磁碟機代號。 您可以使用 Windows 檔案總管或檔案總管來瀏覽這些磁碟機。 指派給磁碟區的磁碟機代號可能與原始虛擬機器為不同的代號，不過，系統會保留磁碟區名稱。 例如，如果原始虛擬機器上的磁碟區是 “資料磁碟機 (E:\)”，則該磁碟區可以在本機電腦上附加做為 “資料磁碟機 ('任何可用的磁碟機代號':\)。 瀏覽指令碼輸出中所提及的所有磁碟區，直到找出您的檔案/資料夾。  
       
   ![[檔案復原] 刀鋒視窗](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>若為 Linux

在 Linux 中，復原點的磁碟區會掛接到執行指令碼的資料夾。 連結的磁碟、磁碟區和對應的掛接路徑會相應顯示。 具有根層級存取權的使用者都看得到這些掛接路徑。 在指令碼輸出中瀏覽所述的磁碟區。

  ![Linux [檔案復原] 刀鋒視窗](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>關閉連線

在識別檔案並將它們複製到本機儲存體位置之後，移除 (或卸載) 其他磁碟機。 若要卸載磁碟機，在 Azure 入口網站的 [檔案復原] 刀鋒視窗中，按一下 [取消掛接磁碟]。

![取消掛接磁碟](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

一旦卸載磁碟後，您會收到一則訊息，告知您已順利完成。 連線可能需要幾分鐘重新整理，以讓您可以移除磁碟。

在 Linux 中，復原點的連線嚴重損毀之後，作業系統並不會自動移除對應的掛接路徑。 這些做為「字串」磁碟區存在且它們會顯示，但當您存取/寫入檔案時會擲回錯誤。 可以手動移除它們。 指令碼執行時，會從任何先前的復原點識別任何這類現有的磁碟區，並在同意下將它們清除。

## <a name="special-configurations"></a>特殊的組態

### <a name="dynamic-disks"></a>動態磁碟

如果備份的 Azure VM 具有跨越多個磁碟 (跨距與等量磁碟區) 及/或跨越動態磁碟上多個容錯磁碟區 (鏡像與 RAID 5 磁碟區) 的磁碟區，您將無法在相同的 VM 上執行可執行的指令碼。 相反地，在任何其他具有相容作業系統上的電腦執行可執行的指令碼。

### <a name="windows-storage-spaces"></a>Windows 儲存空間

Windows 儲存空間是 Windows 儲存體中的一種技術，可讓您虛擬化儲存體。 您可以利用 Windows 儲存空間將工業標準磁碟群組為儲存體集區，然後從這些儲存體集區中的可用空間建立虛擬磁碟，稱為儲存空間。

如果備份的 Azure VM 使用 Windows 儲存空間，則您無法在相同的虛擬機器上執行可執行的指令碼。 相反地，在任何其他具有相容作業系統上的電腦執行可執行的指令碼。

### <a name="lvmraid-arrays"></a>LVM/RAID 陣列

在 Linux 中，邏輯磁碟區管理員 (LVM) 和/或軟體 RAID 陣列會用來管理多個磁碟的邏輯磁碟區。 如果備份的 Linux VM 使用 LVM 和/或 RAID 陣列，您無法在相同的 VM 上執行指令碼。 改為在任何其他具有相容作業系統，且其可支援備份 VM 之檔案系統的電腦上執行指令碼。

指令碼輸出會顯示 LVM 和/或 RAID 陣列磁碟和具有如下所示的磁碟分割類型之磁碟區

   ![Linux LVM 輸出刀鋒視窗](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
下列命令必須由使用者執行使這些磁碟分割上線。 

**若為 LVM 磁碟分割**

```
$ pvs <volume name as shown above in the script output> 
```
這會列出實體磁碟區下的磁碟區群組名稱。

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```
這會列出所有邏輯磁碟區、名稱和其磁碟區群組中的路徑。

```
$ mount <LV path> </mountpath>
```
若要掛接邏輯磁碟區至您所選擇的路徑。


**若為 RAID 陣列**

```
$ mdadm –detail –scan
```
這會顯示有關所有 RAID 磁碟的詳細資料。 相關的 RAID 磁碟將顯示為 `/dev/mdm/<RAID array name in the backed up VM>`

如果 RAID 磁碟具有實體磁碟區，請使用掛接命令。
```
$ mount [RAID Disk Path] [/mounthpath]
```

如果此 RAID 磁碟有另一個在其中配置的 LVM，則針對具有磁碟區名稱為 RAID 磁碟名稱的 LVM 磁碟分割，遵循如上面所述的相同程序

## <a name="troubleshooting"></a>疑難排解

如果您從虛擬機器復原檔案時遇到問題，請檢查下表中的其他資訊。

| 錯誤訊息 / 案例 | 可能的原因 | 建議的動作 |
| ------------------------ | -------------- | ------------------ |
| Exe 輸出︰連線到目標的例外狀況 |指令碼無法存取復原點 | 檢查電腦是否符合上述的存取需求|  
|   Exe 輸出︰目標已透過 ISCSI 工作階段登入。 | 指令碼已在相同電腦上執行，並已附加磁碟機 | 復原點磁碟區已連接。 它們可能未使用原始 VM 的相同磁碟機代號裝載。 在檔案總管中的檔案瀏覽所有可用的磁碟區 |
| Exe 輸出︰這個指令碼無效，因為磁碟已透過入口網站/超過 12 小時限制卸載。請從入口網站下載新的指令碼。 |  已從入口網站卸載磁碟或超過 12 小時的限制 |    此特定 exe 目前無效，無法執行。 如果您想要存取該復原點時間的檔案，請瀏覽新 exe 的入口網站|
| 在執行 exe 的電腦上︰按一下 [卸載] 按鈕之後不會卸載新的磁碟區 |    電腦上的 ISCSI 啟動器沒有回應/重新整理其與目標之連接及維護快取 |    按下 [卸載] 按鈕後等候幾分鐘。 如果仍無法卸載新磁碟區，請瀏覽的所有磁碟區。 這會強制啟動器重新整理連接，且會卸載磁碟區，並出現磁碟無法使用的錯誤訊息|
| Exe 輸出︰指令碼成功執行，但指令碼輸出上不會顯示「附加的新磁碟區」 | 這是暫時性的錯誤   | 磁碟區可能已經附加。 開啟檔案總管以瀏覽。 如果您每次都使用同一部電腦執行指令碼，請考慮重新啟動電腦，清單應該會顯示在後續的 exe 執行。 |
| Linux 特定︰無法檢視所需的磁碟區 | 執行指令碼之電腦的作業系統可能無法辨識備份 VM 的基礎檔案系統 | 檢查復原點是否損毀一致還是檔案一致。 如果檔案一致，在作業系統可辨識備份 VM 的檔案系統之其他電腦上執行指令碼 |
| Windows 特定︰無法檢視所需的磁碟區 | 已連接磁碟，但未設定磁碟區 | 從 [磁碟管理] 畫面上，找出與復原點相關的其他磁碟。 如果有任何這些磁碟處於離線狀態，請透過在磁碟上按一下滑鼠右鍵並按一下 [上線]，來嘗試使狀態成為上線|

