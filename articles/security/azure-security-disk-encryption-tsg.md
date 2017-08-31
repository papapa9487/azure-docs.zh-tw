---
title: "針對 Azure 磁碟加密進行疑難排解 | Microsoft Docs"
description: "本文提供的疑難排解秘訣適用於 Windows 及 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密。"
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 5f482a92b8fcd71a1b767fcc5741bc57605997ea
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure 磁碟加密疑難排解指南

本指南適用於組織目前採用 Azure 磁碟加密，且需要針對磁碟加密相關問題進行疑難排解指引的資訊技術 (IT) 專業人員、資訊安全性分析師和雲端系統管理員。

## <a name="troubleshooting-linux-os-disk-encryption"></a>針對 Linux OS 磁碟加密進行疑難排解

Linux OS 磁碟加密必須先將 OS 磁碟機取消掛接後，才能透過完整磁碟加密流程加以執行。   如果不行，錯誤訊息為「後續無法取消掛接...」 可能會出現錯誤訊息。

在已從其支援內建資源庫映像修改或變更的目標 VM 環境中嘗試 OS 磁碟加密時，這是最有可能的。  可能會干擾 OS 磁碟機取消掛接擴充功能的受支援映像偏差範例包括：
- 不再符合支援的檔案系統和/或資料分割配置的自訂映像。
- 在加密之前，在 OS 中執行防毒、Docker、SAP、MongoDB 或 Apache Cassandra 等應用程式的自訂映像。  這些應用程式難以終止，而且當它們維持開啟 OS 磁碟機的檔案控制代碼，就無法將磁碟機取消掛接，並且會造成失敗。
- 在鄰近加密步驟之關閉時間執行的自訂指令碼可能會造成干擾並導致此失敗。 當 Resource Manager 範本定義要同時執行的多個擴充功能，或當自訂指令碼擴充功能或其他動作同時對磁碟加密執行時，也可能會發生。   將這些步驟序列化和隔離，就可解決此問題。
- 啟用加密之前尚未停用 SELinux 時，取消掛接步驟就會失敗。  完成加密後，即可重新啟用 SELinux。
- 當 OS 磁碟使用 LVM 配置時 (雖然可以使用有限的 LVM 資料磁碟支援，但 LVM OS 磁碟並沒有)
- 當未符合最小記憶體需求時 (OS 磁碟加密建議為 7 GB)
- 當資料磁碟機已遞迴掛接於 /mnt/ directory 下，或彼此掛接 (例如，/mnt/data1、/mnt/data2、/data3 + /data3/data4 等等)
- 未符合其他 Linux 適用的 Azure 磁碟加密[必要條件](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)時

## <a name="unable-to-encrypt"></a>無法加密

在某些情況下，Linux 磁碟加密似乎會卡在「已啟動的 OS 磁碟加密」且停用 SSH。 在庫存資源庫映像上，此程序可能需要 3-16 個小時才能完成。  如果新增多 TB 大小的資料磁碟，此程序可能需要數天的時間。 Linux OS 磁碟加密順序會暫時取消掛接 OS 磁碟機，且在加密狀態中重新掛接之前，會執行整個 OS 磁碟的逐區塊加密。   不同於 Windows 上的 Azure 磁碟加密，Linux 磁碟加密不允許加密進行時，同時使用 VM。  諸如磁碟大小以及儲存體帳戶是由標準還是進階 (SSD) 儲存體所備份等 VM 的效能特性，可能會大幅影響完成加密所需的時間。

若要檢查狀態，可輪詢 [Get AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) 命令所傳回的 ProgressMessage 欄位。   加密 OS 磁碟機時，VM 會進入服務狀態且會停用 SSH，從而避免中斷進行中的流程。  加密進行中的大部分時間都會報告 EncryptionInProgress，並且稍後幾個小時會使用 VMRestartPending 訊息來提示重新啟動 VM。  例如：


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

一旦系統提示重新啟動 VM 且重新啟動 VM 之後，需要 2-3 分鐘重新開機，並會在目標上執行的最後步驟，狀態訊息就會指出最後已完成加密。   一旦可以使用此訊息後，加密的 OS 磁碟機應該會就緒可供使用，且再次可供 VM 使用。

如果未看到此序列，或如果在此流程期間，開機資訊、進度訊息或其他錯誤指標報告 OS 加密失敗 (例如，如果您看到本指南中所述的「無法取消掛接」錯誤)，建議您將 VM 還原回快照集，或在加密之前立即建立備份。  在下一次嘗試之前，建議您重新評估 VM 的特性，並確定所有必要條件都已滿足。

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>針對防火牆後方的 Azure 磁碟加密進行疑難排解
當連線能力受防火牆、Proxy 需求或網路安全性群組 (NSG) 設定限制時，擴充功能執行所需工作的能力可能會受到中斷。   這會產生狀態訊息 (例如「無法在 VM 上使用擴充功能狀態」)，且無法在預期的情節中完成。  以下各節有一些常見的防火牆問題，您可能需要進行調查。

### <a name="network-security-groups"></a>網路安全性群組
任何套用的網路安全性群組設定仍然必須允許端點，從而符合磁碟加密的記載網路設定[必要條件](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites)。

### <a name="azure-keyvault-behind-firewall"></a>防火牆後方的 Azure Keyvault
VM 必須能夠存取金鑰保存庫。 請參閱由 [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall) 小組所維護的指引，當中說明如何從防火牆後方存取金鑰保存庫。

### <a name="linux-package-management-behind-firewall"></a>防火牆後方的 Linux 套件管理
在執行階段，適用於 Linux 的 Azure 磁碟加密需依賴目標發佈的套件管理系統先行安裝所需的必要條件元件後，才能啟用加密。  如果防火牆設定造成 VM 無法下載及安裝這些元件，就會發生後續的失敗。    設定的步驟可能會依發佈而有所不同。  在 Red Hat 上，在需要 Proxy 時，請務必確保已正確地設定您的訂用帳戶管理員和 Yum。  請參閱[此](https://access.redhat.com/solutions/189533) Red Hat 關於本主題的技術支援文件。  

## <a name="troubleshooting-windows-server-2016-server-core"></a>針對 Windows Server 2016 Server Core 進行疑難排解

在 Windows Server 2016 Server Core 上，預設無法使用 bdehdcfg 元件。 Azure 磁碟加密需要此元件。

若要解決這個問題，請將下列 4 個檔案從 Windows Server 2016 資料中心 VM 複製到 Server Core 映像的 c:\windows\system32 資料夾：

```
bdehdcfg.exe
bdehdcfglib.dll
bdehdcfglib.dll.mui
bdehdcfg.exe.mui
```

然後，執行下列命令：

```
bdehdcfg.exe -target default
```

這會建立 550 MB 的系統磁碟分割，然後在重新開機後，即可使用 Diskpart 來檢查磁碟區，並繼續執行。  

例如：

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="see-also"></a>另請參閱
在本文件中，您已深入了解 Azure 磁碟加密中的一些常見問題，以及如何進行疑難排解。 如需此服務和其功能的相關資訊，請閱讀：

- [在 Azure 資訊安全中心套用磁碟加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [加密 Azure 虛擬機器](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure 資料靜態加密](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

