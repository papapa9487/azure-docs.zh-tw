---
title: "Azure 至 Azure 複寫問題和錯誤的 Azure Site Recovery 疑難排解 | Microsoft Docs"
description: "對於複寫 Azure 虛擬機器進行嚴重損壞修復時發生的錯誤和問題進行疑難排解"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/10/2017
ms.author: sujayt
ms.openlocfilehash: 4e4e8b097fbab3ddce551eb93945d0880f8c457f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure 至 Azure VM 複寫問題的疑難排解

本文說明從一個區域將 Azure 虛擬機器複寫和復原到另一個區域時，關於 Azure Site Recovery 的常見問題，並解說如何進行疑難排解。 如需受支援組態的詳細資訊，請參閱[複寫 Azure VM 的支援矩陣](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 資源配額問題 (錯誤碼 150097)
您的訂用帳戶應該啟用，才能在要做為災害復原區域的目標區域中建立 Azure VM。 此外，您的訂用帳戶應該已啟用足夠的配額，才能建立特定大小的 VM。 根據預設，Site Recovery 會取用相同大小的目標 VM 做為來源 VM。 如果沒有相符大小，系統會自動挑選最接近的大小。 如果沒有支援來源 VM 組態的相符大小，則會出現下列錯誤訊息：

**錯誤碼** | **可能的原因** | **建議**
--- | --- | ---
150097<br></br>**訊息**：虛擬機器 VmName 無法啟用複寫。 | - 可能未啟用您的訂用帳戶 ID 在目標區域位置中建立任何 VM。</br></br>- 可能未啟用您的訂用帳戶 ID 或沒有足夠的配額可在目標區域位置中建立特定 VM 大小。</br></br>- 對於目標區域位置中的訂用帳戶 ID，找不到符合來源 VM NIC 計數 (2) 的適當 VM 大小。| 對於訂用帳戶的目標位置之中的所需 VM 大小，請連絡 [Azure 計費支援](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)啟用 VM。 啟用後，請重試失敗的作業。

### <a name="fix-the-problem"></a>修正問題
您可以連絡 [Azure 計費支援](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)啟用訂用帳戶，在目標位置中建立所需大小的 VM。

如果目標位置有容量限制，請停用複寫，並對於訂用帳戶有足夠配額的不同位置啟用複寫，以建立所需大小的 VM。

## <a name="trusted-root-certificates-error-code-151066"></a>受信任的根憑證 (錯誤碼 151066)

如果 VM 沒有所有最新的受信任根憑證，「啟用複寫」工作可能會失敗。 如果沒有憑證，對於從 VM 的 Site Recovery 服務呼叫進行的驗證和授權會失敗。 失敗的「啟用複寫」Site Recovery 工作的錯誤訊息會出現：

**錯誤碼** | **可能的原因** | **建議**
--- | --- | ---
151066<br></br>**訊息**：Site Recovery 組態失敗。 | 機器上不存在用於授權和驗證的受信任根憑證。 | - 對於執行 Windows 作業系統的 VM，請確定機器上存在受信任根憑證。 如需資訊，請參閱[設定受信任根目錄和不允許的憑證](https://technet.microsoft.com/library/dn265983.aspx)。<br></br>- 對於執行 Linux 作業系統的 VM，請依照 Linux 作業系統版本散發者發行的受信任根憑證適用的指引。

### <a name="fix-the-problem"></a>修正問題
**Windows**

在 VM 上安裝所有最新的 Windows 更新，使機器上存在所有的受信任根憑證。 如果您是在中斷連線的環境，請遵循您組織的標準 Windows 更新程序來取得憑證。 如果 VM 上不存在所需的憑證，對於 Site Recovery 進行的呼叫將而於安全性因素而失敗。

遵循您組織的一般 Windows 更新管理或憑證更新管理程序，取得所有最新的根憑證以及 VM 上更新的憑證撤銷清單。

若要確認問題已解決，請在 VM 中從瀏覽器移至 login.microsoftonline.com。

**Linux**

請遵循 Linux 散發者提供的指引，取得最新的受信任根憑證以及 VM 上最新的憑證撤銷清單。

因為 SuSE Linux 使用符號連結來維護憑證清單，因此請遵循下列步驟：

1.  以根使用者身分登入。

2.  請執行這個命令：

      ``# cd /etc/ssl/certs``

3.  若要查看 Symantec 根 CA 憑證是否存在，請執行下列命令：

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4.  如果找不到檔案，請執行下列命令：

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

      ``# c_rehash``

5.  若要使用 b204d74a.0 建立符號連結 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem，請執行下列命令：

      ``# ln -s  VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

6.  請檢查此命令是否有下列的輸出。 如果沒有，則您必須建立符號連結：

      ``# ls -l | grep Baltimore
      -rw-r--r-- 1 root root   1303 Apr  7  2016 Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 04:47 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 05:01 653b494a.0 -> Baltimore_CyberTrust_Root.pem``

7. 如果符號連結 653b494a.0 不存在，請使用此命令建立符號連結：

      ``# ln -s Baltimore_CyberTrust_Root.pem 653b494a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 或 IP 範圍的輸出連線能力 (錯誤碼 151037 或 151072)

若要使 Site Recovery 複寫正常運作，VM 需要特定 URL 或 IP 範圍的輸出連線能力。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會看到下列其中一個錯誤訊息：

**錯誤碼** | **可能的原因** | **建議**
--- | --- | ---
151037<br></br>**訊息**：無法向 Site Recovery 註冊 Azure 虛擬機器。 | - 您使用 NSG 控制傳出 VM 的存取，而且所需的 IP 範圍並未列在傳出存取的白名單中。</br></br>- 您使用第三方防火牆工具，而且所需的 IP 範圍/URL 並未列在白名單中。</br>| - 如果您使用防火牆 Proxy 控制 VM 上的傳出網路連線能力，請確定必要條件 URL 或資料中心 IP 範圍列在白名單中。 如需資訊，請參閱[防火牆 Proxy 指引](https://aka.ms/a2a-firewall-proxy-guidance)。</br></br>- 如果您使用 NSG 規則控制 VM 上的傳出網路連線能力，請確定必要條件資料中心 IP 範圍列在白名單中。 如需資訊，請參閱[網路安全性群組指引](https://aka.ms/a2a-nsg-guidance)。
151072<br></br>**訊息**：Site Recovery 組態失敗。 | 無法在連線至 Site Recovery 服務端點。 | - 如果您使用防火牆 Proxy 控制 VM 上的傳出網路連線能力，請確定必要條件 URL 或資料中心 IP 範圍列在白名單中。 如需資訊，請參閱[防火牆 Proxy 指引](https://aka.ms/a2a-firewall-proxy-guidance)。</br></br>- 如果您使用 NSG 規則控制 VM 上的傳出網路連線能力，請確定必要條件資料中心 IP 範圍列在白名單中。 如需資訊，請參閱[網路安全性群組指引](https://aka.ms/a2a-nsg-guidance)。

### <a name="fix-the-problem"></a>修正問題
若要將[所需的 URL](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-urls) 或[所需的 IP 範圍](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-ip-ranges)列入白名單中，請依照[網路指引文件](site-recovery-azure-to-azure-networking-guidance.md)中的步驟進行。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>在機器中找不到磁碟 (錯誤碼 150039)

必須先將連接到 VM 的新磁碟初始化。

**錯誤碼** | **可能的原因** | **建議**
--- | --- | ---
150039<br></br>**訊息**：邏輯單元編號 (LUN) 為 (LUNValue) 的 Azure 資料磁碟 (DiskName) (DiskURI) 未對應到從 LUN 值相同的 VM 內回報的對應磁碟。 | - 新的資料磁碟連接到 VM，但是未初始化。</br></br>- VM 內的資料磁碟不當回報 LUN 值，該磁碟已連接到 VM。| 請確定資料磁碟都已初始化，然後再次嘗試操作。</br></br>對於 Windows：[連接並初始化新的磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-portal#option-1-attach-and-initialize-a-new-disk)。</br></br>對於 Linux：[在 Linux 中初始化新的資料磁碟](https://docs.microsoft.com/azure/virtual-machines/linux/classic/attach-disk#initialize-a-new-data-disk-in-linux)。

### <a name="fix-the-problem"></a>修正問題
請確定資料磁碟都已初始化，然後再次嘗試操作：

- 對於 Windows：[連接並初始化新的磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-portal#option-1-attach-and-initialize-a-new-disk)。
- 對於 Linux：[在 Linux 中初始化新的資料磁碟](https://docs.microsoft.com/azure/virtual-machines/linux/classic/attach-disk#initialize-a-new-data-disk-in-linux)。

若問題持續發生，請連絡支援服務。


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>看不見 Azure VM，無法在「啟用複寫」中選取

您可能看不見您的 Azure VM，因此無法在[啟用複寫：步驟 2](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines) 中選取。 此問題可能是由於 Azure VM 上保留過時的 Site Recovery 組態。 在下列情況中，Azure VM 可能保留過時的組態：

- 您使用 Site Recovery 啟用 Azure VM 的複寫，然後刪除 Site Recovery 保存庫，但是並未明確停用 VM 上的複寫。
- 您使用 Site Recovery 啟用 Azure VM 的複寫，然後刪除包含 Site Recovery 保存庫的資源群組，但是並未明確停用 VM 上的複寫。

### <a name="fix-the-problem"></a>修正問題

您可以使用[移除過時 ASR 組態指令碼](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412)，並移除 Azure VM 上的過時 Site Recovery 組態。 移除過時的組態後，您應該會在[啟用複寫：步驟 2](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines) 中看見 VM。


## <a name="next-steps"></a>後續步驟
[複寫 Azure 虛擬機器](site-recovery-replicate-azure-to-azure.md)
