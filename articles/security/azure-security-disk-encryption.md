---
title: "Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密 | Microsoft Docs"
description: "本文提供 Windows 和 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密概觀。"
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: kakhan
ms.openlocfilehash: 15ed35ab3a082db3376890992be3a29b6e042a2f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密
Microsoft Azure 強烈承諾確保您的資料隱私權、資料主權，並透過一系列進階技術來加密、控制和管理加密金鑰、控制和稽核資料存取，讓您控制您的 Azure 託管資料。 這會提供 Azure 客戶靈活度，可選擇最符合其商務需求的解決方案。 本文中，我們將為您介紹新的技術解決方案「Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密」，以協助保護及保障您的資料，以便符合組織的安全性和符合性的承諾。 本文提供有關如何使用 Azure 磁碟加密功能的詳細指引，包括支援的案例和使用者體驗。

> [!NOTE]
> 某些建議可能會增加資料、網路或計算資源的使用量，導致額外的授權或訂用帳戶成本。

## <a name="overview"></a>概觀
Azure 磁碟加密是協助您加密 Windows 和 Linux IaaS 虛擬機器磁碟的新功能。 Azure 磁碟加密利用 Windows 的業界標準 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，為 OS 和資料磁碟提供磁碟區加密。 此解決方案與 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) 整合，協助您控制及管理金鑰保存庫訂用帳戶中的磁碟加密金鑰與密碼。 此解決方案也可確保虛擬機器磁碟上的所有待用資料都會在您的 Azure 儲存體中加密。

Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密現已在所有 Azure 公用區域和 AzureGov 區域**正式推出**，可供用於標準 VM 和具有高階儲存體的 VM。

### <a name="encryption-scenarios"></a>加密案例
Azure 磁碟加密解決方案支援下列客戶案例：

* 對透過加密的 VHD 和加密金鑰建立的新 IaaS VM 啟用加密
* 對透過受支援 Azure 資源庫映像建立的新 IaaS VM 啟用加密
* 在 Azure 中執行的現有 IaaS VM 上啟用加密
* 在 Windows IaaS VM 上停用加密
* 在 Linux IaaS VM 的資料磁碟機上停用加密
* 允許加密受管理磁碟 VM
* 更新現有已加密進階與非進階儲存體 VM 的加密設定
* 備份與還原加密的 VM

在 Microsoft Azure 中啟用時，解決方案會對 IaaS VM 支援下列案例：

* 與 Azure 金鑰保存庫整合
* 標準層 VM：[A、D、DS、G、GS、F 等系列 IaaS VM](https://azure.microsoft.com/pricing/details/virtual-machines/)
* 對透過受支援 Azure 資源庫映像的 Windows 和 Linux IaaS VM 以及受控磁碟 VM 啟用加密
* 在 Windows IaaS VM 與受管理磁碟 VM 的作業系統與資料磁碟機上停用加密
* 在 Linux IaaS VM 與受管理磁碟 VM 的資料磁碟機上停用加密
* 在執行 Windows Client OS 的 IaaS VM 上啟用加密
* 在具有掛接路徑的磁碟區上啟用加密
* 使用 mdadm 在設定了磁碟串接 (RAID) 的 Linux VM 上啟用加密
* 使用資料磁碟適用的 LVM 在 Linux VM 上啟用加密
* 在適用於作業系統和資料磁碟的 Linux LVM 7.3 上啟用加密 
* 在設定了儲存空間的 Windows VM 上啟用加密
* 更新現有已加密進階與非進階儲存體 VM 的加密設定
* 針對非 KEK 與 KEK 案例 (KEK，金鑰加密金鑰) 備份與還原已加密的 VM
* 支援所有 Azure 公用區域與 AzureGov 區域

此解決方案不支援下列案例、功能和技術：

* 基本層 IaaS VM
* 在 Linux IaaS VM 的 OS 磁碟機上停用加密
* 如果已加密 Linux IaaS VM 的 OS 磁碟機，請將資料磁碟機上的加密停用
* 使用傳統 VM 建立方法所建立的 IaaS VM
* 「不」支援透過 Windows 和 Linux IaaS VM 客戶自訂映像上啟用加密。
* 與您的內部部署金鑰管理服務整合
* Azure 檔案 (共用檔案系統)、網路檔案系統 (NFS)、動態磁碟區和以軟體型 RAID 系統所設定的 Windows VM

### <a name="encryption-features"></a>加密功能
為 Azure IaaS VM 啟用並部署 Azure 磁碟加密時，會啟用下列功能，視提供的組態而定：

* 加密 OS 磁碟區以保護儲存體中的待用開機磁碟區
* 加密資料磁碟區以保護儲存體中的待用資料磁碟區
* 在 Windows IaaS VM 的 OS 和資料磁碟機上停用加密
* 在 Linux IaaS VM 的資料磁碟機上停用加密 (僅在 OS 磁碟機「並未」加密時)
* 保護金鑰保存庫訂用帳戶中的加密金鑰和密碼
* 報告已加密 IaaS VM 的加密狀態
* 從 IaaS 虛擬機器移除磁碟加密組態設定
* 使用 Azure 備份服務來備份和還原已加密的 VM

Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密解決方案包含：

* Windows 的磁碟加密擴充功能。
* Linux 的磁碟加密擴充功能。
* 磁碟加密 PowerShell Cmdlet。
* 磁碟加密 Azure 命令列介面 (CLI) Cmdlet。
* 磁碟加密 Azure Resource Manager 範本。

執行 Windows 或 Linux OS 的 IaaS VM 支援 Azure 磁碟加密解決方案。 如需支援的作業系統相關詳細資訊，請參閱「必要條件」一節。

> [!NOTE]
> 使用 Azure 磁碟加密來加密 VM 磁碟完全免費。

### <a name="value-proposition"></a>價值主張
當您套用 Azure 磁碟加密管理解決方案時，可滿足下列商務需求：

* 待用 IaaS VM 會受到保護，因您使用了業界標準的加密技術解決組織安全性和合規性的需求。
* IaaS VM 會在客戶控制的金鑰和原則下開機，且您可以在金鑰保存庫中稽核其使用狀況。

### <a name="encryption-workflow"></a>加密工作流程
若要啟用 Windows 和 Linux VM 的磁碟加密，請執行下列作業︰

1. 從先前的加密案例中選擇一個加密案例。
2. 選擇透過 Azure 磁碟加密 Resource Manager 範本、PowerShell Cmdlet 或 CLI 命令啟用磁碟加密，並指定加密組態。

   * 針對客戶加密的 VHD 案例，請將加密的 VHD 上傳至儲存體帳戶並將加密金鑰資料上傳至金鑰保存庫。 接著，提供加密組態資訊以在新的 IaaS VM 上啟用加密。
   * 針對透過 Marketplace 所建立的新 VM 和已在 Azure 中執行的現有 VM，提供加密組態以在 IaaS VM 上啟用加密。

3. 授與存取至 Azure 平台，以從您的金鑰保存庫讀取加密金鑰資料 (Windows 系統的 BitLocker 加密金鑰和 Linux 的複雜密碼)，藉以在 IaaS VM 上啟用加密。

4. 提供 Azure Active Directory (Azure AD) 應用程式身分識別，以將加密金鑰資料寫入至金鑰保存庫。 如此可針對步驟 2 中所述的案例在 IaaS VM 上啟用加密。

5. Azure 會使用加密和金鑰保存庫組態更新 VM 服務模型，並建立加密的 VM。

 ![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>解密工作流程
若要停用 IaaS VM 的磁碟加密，請完成下列高階步驟：

1. 選擇透過 Azure 磁碟加密 Resource Manager 範本或 PowerShell Cmdlet，在 Azure 中的執行中 IaaS VM 上停用加密 (解密)，並指定解密組態。

 此步驟會在執行中 Windows IaaS VM 上停用 OS 或資料磁碟區或兩者的加密。 但是如前一節所述，並不支援停用 Linux 的 OS 磁碟加密。 只要 OS 磁碟機未加密，就只允許對 Linux VM 上的資料磁碟機執行解密步驟。
2. Azure 會更新 VM 服務模型，且 IaaS VM 會標示為已解密。 VM 的待用內容不會再加密。

> [!NOTE]
> 停用加密作業並不會刪除您的金鑰保存庫和加密金鑰資料 (Windows 系統的 BitLocker 加密金鑰或 Linux 的複雜密碼)。
 > 不支援停用 Linux 適用的作業系統磁碟加密。 只允許對 Linux VM 上的資料磁碟機執行解密步驟。
如果 OS 磁碟機已加密，就不支援將 Linux 的資料磁碟加密停用。

## <a name="prerequisites"></a>必要條件
針對「概觀」一節所提到支援的案例，在 Azure IaaS VM 上啟用 Azure 磁碟加密之前，請參閱下列必要條件：

* 您必須擁有有效的作用中 Azure 訂用帳戶，才能在 Azure 支援的區域中建立資源。
* 下列 Windows Server 版本支援 Azure 磁碟加密：Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016。
* 下列 Windows 用戶端版本支援 Azure 磁碟加密：Windows 8 Client 和 Windows 10 Client。

> [!NOTE]
> 針對 Windows Server 2008 R2，您必須先安裝 .NET Framework 4.5，才能在 Azure 中啟用加密。 您可以透過安裝選用的更新 Windows Server 2008 R2 x64 型系統的 Microsoft .NET Framework 4.5.2 ([KB2901983](https://support.microsoft.com/kb/2901983))，從 Windows Update 安裝它。

* 在下列以 Azure 資源庫為基礎的 Linux 伺服器散發套件和版本上支援 Azure 磁碟加密︰

| Linux 散發套件 | 版本 | 支援加密的磁碟區類型|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | 作業系統和資料磁碟 |
| Ubuntu | 14.04.5-DAILY-LTS | 作業系統和資料磁碟 |
| Ubuntu | 12.10 | 資料磁碟 |
| Ubuntu | 12.04 | 資料磁碟 |
| RHEL | 7.4 | 作業系統和資料磁碟 |
| RHEL | 7.3 | 作業系統和資料磁碟 |
| RHEL | LVM 7.3 | 作業系統和資料磁碟 |
| RHEL | 7.2 | 作業系統和資料磁碟 |
| RHEL | 6.8 | 作業系統和資料磁碟 |
| RHEL | 6.7 | 資料磁碟 |
| CentOS | 7.3 | 作業系統和資料磁碟 |
| CentOS | 7.2n | 作業系統和資料磁碟 |
| CentOS | 6.8 | 作業系統和資料磁碟 |
| CentOS | 7.1 | 資料磁碟 |
| CentOS | 7.0 | 資料磁碟 |
| CentOS | 6.7 | 資料磁碟 |
| CentOS | 6.6 | 資料磁碟 |
| CentOS | 6.5 | 資料磁碟 |
| openSUSE | 13.2 | 資料磁碟 |
| SLES | 12 SP1 | 資料磁碟 |
| SLES | 12-SP1 (高階) | 資料磁碟 |
| SLES | HPC 12 | 資料磁碟 |
| SLES | 11-SP4 (高階) | 資料磁碟 |
| SLES | 11 SP4 | 資料磁碟 |

* Azure 磁碟加密需要您的金鑰保存庫和 VM 位於相同的 Azure 區域和訂用帳戶中。

> [!NOTE]
> 若在不同的區域設定資源，會導致 Azure 磁碟加密功能啟用失敗。

* 若要安裝及設定 Azure 磁碟加密的金鑰保存庫，請參閱本文＜必要條件＞一節中的**安裝及設定 Azure 磁碟加密的金鑰保存庫**。
* 若要針對 Azure 磁碟加密在 Azure Active Directory 中安裝與設定 Azure AD 應用程式 ，請參閱本文＜必要條件＞一節中的**在 Azure Active Directory 中設定 Azure AD 應用程式**。
* 若要針對 Azure AD 應用程式安裝與設定金鑰保存庫存取原則，請參閱本文＜必要條件＞一節中的**設定 Azure AD 應用程式的金鑰保存庫存取原則**
* 若要準備預先加密的 Windows VHD，請參閱＜附錄＞中的**準備預先加密的 Windows VHD** 一節。
* 若要準備預先加密的 Linux VHD，請參閱＜附錄＞中的**準備預先加密的 Linux VHD** 一節。
* Azure 平台需要存取您金鑰保存庫中的加密金鑰或密碼，讓它們可供虛擬機器用來開機和解密虛擬機器作業系統磁碟區。 若要授與權限至 Azure 平台，請設定金鑰保存庫中的 **EnabledForDiskEncryption** 屬性。 如需詳細資訊，請參閱＜附錄＞中的**安裝及設定 Azure 磁碟加密的金鑰保存庫**。
* 您的金鑰保存庫密碼和 KEK URL 必須已設定版本。 Azure 會強制執行設定版本的這項限制。 針對有效的密碼和 KEK URL，請參閱下列範例︰

  * 有效祕密 URL 的範例︰https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  * 有效 KEK URL 的範例：https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

* Azure 磁碟加密不支援將連接埠號碼指定為金鑰保存庫密碼和 KEK URL 的一部分。 如需不支援和支援的金鑰保存庫 URL 範例，請參閱下列各項︰

  * 無法接受的金鑰保存庫 URL https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  * 可接受的金鑰保存庫 URL：https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

* 若要啟用 Azure 磁碟加密功能，IaaS VM 必須符合下列網路端點組態需求：
  * 若要取得用來連線至金鑰保存庫的權杖，IaaS VM 必須能連線至 Azure Active Directory 端點 \[login.microsoftonline.com\]。
  * 若要將加密金鑰寫入至您的金鑰保存庫，IaaS VM 必須能連接至金鑰保存庫端點。
  * IaaS VM 必須能連接至託管 Azure 擴充儲存機制的 Azure 儲存體端點，和託管 VHD 檔案的 Azure 儲存體帳戶。

  > [!NOTE]
  > 如果您的安全性原則會限制從 Azure VM 至網際網路的存取，您可以解析前述的 URI，並設定特定的規則以允許和這些 IP 的輸出連線。
  >
  >設定與存取防火牆後的 Azure 金鑰保存庫 (英文) (https://docs.microsoft.com/en-us/azure/key-vault/key-vault-access-behind-firewall)

* 使用最新版的 Azure PowerShell SDK 版本來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell 版本](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > [Azure PowerShell SDK 1.1.0 版](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016)不支援 Azure 磁碟加密。 如果您收到與使用 Azure PowerShell 1.1.0 相關的錯誤，請參閱[與 Azure PowerShell 1.1.0 相關的 Azure 磁碟加密錯誤](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx)。

* 若要執行任何 Azure CLI 命令並使其與您的 Azure 訂用帳戶建立關聯，您必須先安裝 Azure CLI：
  * 若要安裝 Azure CLI 並使其與您的 Azure 訂用帳戶建立關聯，請參閱[如何安裝和設定 Azure CLI](../cli-install-nodejs.md)。
  * 若要使用適用於 Mac、Linux 和 Windows 的 Azure CLI 搭配 Azure Resource Manager，請參閱 [Resource Manager 模式中的 Azure CLI 命令](../virtual-machines/azure-cli-arm-commands.md)。

* 在加密受控磁碟時，必要的先決條件是先擷取受控磁碟的快照集或先在 Azure 磁碟加密之外備份磁碟再啟用加密。  若未備妥備份，當加密期間發生任何未預期的失敗時，將可能會讓磁碟和 VM 變得無法存取又沒有復原選項。  Set-AzureRmVMDiskEncryptionExtension 目前不會備份受控磁碟，而且除非指定了 -skipVmBackup 參數，否則在針對受控磁碟使用時將會發生錯誤。  除非您已在 Azure 磁碟加密之外建立備份，否則使用此參數並不安全。   若指定了 -skipVmBackup 參數，此 Cmdlet 在執行加密之前將不會先備份受控磁碟。  因此，我們才會認為必要的先決條件就是先備妥受控磁碟 VM 的備份，再啟用 Azure 磁碟加密，以防之後需要進行復原。  
> [!NOTE]
 > 除非您已在 Azure 磁碟加密之外建立快照集或備份，否則請永遠不要使用 -skipVmBackup 參數。 

* Azure 磁碟加密解決方案對 Windows IaaS VM 使用 BitLocker 外部金鑰保護裝置。 對於加入網域的 VM，請勿推送任何會強制使用 TPM 保護裝置的群組原則。 如需關於「在不含相容 TPM 的情形下允許使用 BitLocker」的群組原則相關資訊，請參閱 [BitLocker 群組原則參考文件](https://technet.microsoft.com/library/ee706521)。
* 適用於具有自訂群組原則且已加入網域之虛擬機器上的 Bitlocker 原則必須包含下列設定：`Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` 當 Bitlocker 的自訂群組原則設定不相容時，Azure 磁碟加密將會失敗。 在沒有正確原則設定的電腦上，您可能必須套用新的原則、強制新的原則進行更新 (gpupdate.exe /force)，然後重新啟動。  
* 若要建立 Azure AD 應用程式、建立金鑰保存庫或設定現有的金鑰保存庫並啟用加密，請參閱 [Azure 磁碟加密的必要條件 PowerShell 指令碼](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1)。
* 若要使用 Azure CLI 設定磁碟加密必要條件，請參閱[此 Bash 指令碼](https://github.com/ejarvi/ade-cli-getting-started)。
* 若要使用 Azure 備份服務來備份和還原已加密的 VM，在透過 Azure 磁碟加密啟用加密時，請使用 Azure 磁碟加密金鑰組態來加密您的 VM。 備份服務支援使用非 KEK 或 KEK 組態加密的 VM。 請參閱[如何使用 Azure 備份加密來備份與還原加密的虛擬機器](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption) (英文)。

* 在加密 Linux 作業系統磁碟區時請注意，您目前需要在加密程序結束時重新啟動 VM。 這可以透過入口網站、PowerShell 或 CLI 來完成。   若要追蹤加密進度，請定期輪詢 Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus 所傳回的狀態訊息。  加密完成後，此命令所傳回的狀態訊息將會指出這一點。  例如，「ProgressMessage：已成功加密作業系統磁碟，請重新啟動 VM」。此時即可重新啟動及使用 VM。  

* 適用於 Linux 的 Azure 磁碟加密會要求資料磁碟先在 Linux 中擁有已掛接的檔案系統，然後才會執行加密

* 適用於 Linux 的 Azure 磁碟加密不支援遞迴掛接的資料磁碟。 例如，如果目標系統已在 /foo/bar 掛接一個磁碟，又在 /foo/bar/baz 掛接另一個磁碟，則 /foo/bar/baz 的加密會成功，但 /foo/bar 的加密則會失敗。 

* 只有符合上述必要條件的 Azure 資源庫支援的映像才會支援 Azure 磁碟加密。 由於客戶自訂映像上可能存在自訂的資料分割配置和流程表現方式，因此系統不支援這些映像。 此外，即使以資源庫映像為基礎的 VM 一開始符合必要條件，但如果該 VM 在建立之後做過修改，則也可能會變得不相容。  為此，建議的 Linux VM 加密程序是從全新的資源庫映像來開始、加密 VM，然後再視需要於 VM 中新增自訂軟體或資料。  

* Azure 磁碟加密與本機資料磁碟區 (Windows 的 Bek Volume 和 Linux IaaS VM 的 /mnt/azure_bek_disk)，其會安全地保存加密金鑰。 請勿刪除或編輯此磁碟中的任何內容。 由於 IaaS VM 上的任何加密作業都需要加密金鑰存在，因此請勿卸載該磁碟。 磁碟區內的讀我檔案包含其他詳細資料。

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>在 Azure Active Directory 中設定 Azure AD 應用程式
當您需要在 Azure 中執行中的 VM 上啟用加密時，Azure 磁碟加密會產生並將加密金鑰寫入金鑰保存庫。 在金鑰保存庫中管理加密金鑰需要 Azure AD 驗證。

基於此目的，請建立 Azure AD 應用程式。 您可以在部落格文章 [Azure Key Vault - 逐步解說](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)的「取得應用程式的身分識別」一節中找到註冊應用程式的詳細步驟。 這篇文章也包含一些有關安裝及設定金鑰保存庫的實用範例。 針對驗證目的，您可以使用用戶端密碼式驗證或用戶端憑證式 Azure AD 驗證。

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Azure AD 的用戶端密碼式驗證
下列各節可協助您設定 Azure AD 的用戶端密碼式驗證。

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>使用 Azure PowerShell 來建立 Azure AD 應用程式
使用下列 PowerShell Cmdlet 建立 Azure AD 應用程式︰

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId 是 Azure AD ClientID，而 $aadClientSecret 是用戶端密碼，您稍後應該會用該資訊來啟用 Azure 磁碟加密。 適當地保護 Azure AD 用戶端密碼。

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>從 Azure 入口網站設定 Azure AD 用戶端識別碼和密碼
您也可以使用 Azure 入口網站設定您的 Azure AD 用戶端識別碼和密碼。 若要執行這項工作，請執行下列步驟︰

1. 按一下 [Active Directory] 索引標籤。

 ![Azure 磁碟加密](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2. 按一下 [新增應用程式]，並輸入應用程式名稱。

 ![Azure 磁碟加密](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. 按一下箭號按鈕，然後設定應用程式的屬性。

 ![Azure 磁碟加密](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. 按一下左下角的核取記號來完成。 應用程式組態頁面隨即出現，而 Azure AD 用戶端識別碼會顯示在頁面底部。

 ![Azure 磁碟加密](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. 按一下 [儲存] 按鈕來儲存 Azure AD 用戶端密碼。 請注意在金鑰文字方塊中的 Azure AD 用戶端密碼。 適當地保護該密碼。

 ![Azure 磁碟加密](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


##### <a name="use-an-existing-application"></a>使用現有的應用程式
若要執行下列命令，請取得並使用 [Azure AD PowerShell 模組](https://technet.microsoft.com/library/jj151815.aspx)。

> [!NOTE]
> 下列命令必須從新的 PowerShell 視窗執行。 請勿使用 Azure PowerShell 或 Azure Resource Manager 視窗來執行這些命令。 我們建議使用此方法是因為這些 Cmdlet 在 MSOnline 模組或 Azure AD PowerShell 中。

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Azure AD 的憑證式驗證
> [!NOTE]
> Linux VM 目前不支援 Azure AD 憑證式驗證。

以下各節會說明如何設定 Azure AD 的憑證式驗證。

##### <a name="create-an-azure-ad-application"></a>建立 Azure AD 應用程式
若要建立 Azure AD 應用程式，請執行下列 PowerShell Cmdlet︰

> [!NOTE]
> 使用您的安全密碼來取代下列 `yourpassword` 字串，並保護密碼。

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

完成此步驟之後，請將 PFX 檔案上傳至您的金鑰保存庫，並啟用將該憑證部署到 VM 所需的存取原則。

##### <a name="use-an-existing-azure-ad-application"></a>使用現有的 Azure AD 應用程式
如果您要為現有應用程式設定憑證式驗證，請使用此處所示的 PowerShell Cmdlet。 務必從新的 PowerShell 視窗執行它們。

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

完成此步驟之後，請將 PFX 檔案上傳至您的金鑰保存庫，並啟用將憑證部署到 VM 所需的存取原則。

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>將 PFX 檔案上傳至金鑰保存庫
如需此程序的詳細說明，請參閱[官方 Azure Key Vault 團隊部落格](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)。 不過，對於這項工作您只需要下列 PowerShell Cmdlet。 務必從 Azure PowerShell 主控台執行它們。

> [!NOTE]
> 使用您的安全密碼來取代下列 `yourpassword` 字串，並保護密碼。

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>將金鑰保存庫中的憑證部署至現有 VM
PFX 上傳完成之後，使用下列作業將金鑰保存庫中的憑證部署至現有 VM︰
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>設定 Azure AD 應用程式的金鑰保存庫存取原則
您的 Azure AD 應用程式需要權限，才能存取保存庫中的金鑰或密碼。 使用 [`Set-AzureKeyVaultAccessPolicy`](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) Cmdlet 可授與應用程式權限，使用用戶端識別碼 (登錄應用程式時所產生) 做為 _–ServicePrincipalName_ 參數值。 若要深入了解，請參閱部落格文章 [Azure Key Vault - 逐步解說](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)。 以下是如何透過 PowerShell 執行這項工作的範例：

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> 若要使用 Azure 磁碟加密，您必須對 Azure AD 用戶端應用程式設定下列存取原則：_WrapKey_ 和 _Set_ 權限。

## <a name="terminology"></a>術語
若要了解此技術所使用的一些常用術語，請使用以下術語表：

| 術語 | 定義 |
| --- | --- |
| Azure AD | Azure AD 是 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)。 Azure AD 帳戶是從金鑰保存庫驗證、儲存和擷取密碼的必要條件。 |
| Azure 金鑰保存庫 | Key Vault 是密碼編譯金鑰管理服務，它是基於美國聯邦資訊處理標準 (FIPS) 驗證的硬體安全性模組，可協助您保護密碼編譯金鑰和敏感性密碼。 如需詳細資訊，請參閱 [Key Vault](https://azure.microsoft.com/services/key-vault/) 文件。 |
| ARM | Azure Resource Manager |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) 是一種業界認可的 Windows 磁碟區加密技術，用來在 Windows IaaS VM 上啟用磁碟加密。 |
| BEK | BitLocker 加密金鑰可用來加密作業系統開機磁碟區和資料磁碟區。 BitLocker 金鑰會在金鑰保存庫中以密碼形式保護。 |
| CLI | 請參閱 [Azure 命令列介面](../cli-install-nodejs.md)。 |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 是基於 Linux 的透明磁碟加密子系統，用來在 Linux IaaS VM 上啟用磁碟加密。 |
| KEK | 金鑰加密金鑰是非對稱金鑰 (RSA 2048)，可用來保護或包裝密碼。 您可以提供硬體安全性模組 (HSM) 保護的金鑰或軟體保護的金鑰。 如需詳細資訊，請參閱 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文件。 |
| PS Cmdlet | 請參閱 [Azure PowerShell Cmdlet](/powershell/azure/overview)。 |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>針對 Azure 磁碟加密安裝及設定您的金鑰保存庫
Azure 磁碟加密會協助您保護金鑰保存庫中的磁碟加密金鑰和密碼。 若要設定 Azure 磁碟加密的金鑰保存庫，請完成下列各節中的步驟。

#### <a name="create-a-key-vault"></a>建立金鑰保存庫
若要建立金鑰保存庫，請使用下列選項之一︰

* ["101-Key-Vault-Create" Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Azure PowerShell 金鑰保存庫 Cmdlet](/powershell/module/azurerm.keyvault/#key_vault)
* Azure Resource Manager
* 如何[保護您的金鑰保存庫](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> 如果您已為訂用帳戶設定了金鑰保存庫，請跳至下一節。

![Azure 金鑰保存庫](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>設定金鑰加密金鑰 (選擇性)
如果您想針對 BitLocker 加密金鑰使用 KEK 增加額外的安全性，請將 KEK 新增至您的金鑰保存庫。 使用 [`Add-AzureKeyVaultKey`](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) Cmdlet 在金鑰保存庫中建立金鑰加密金鑰。 您也可以從內部部署金鑰管理 HSM 匯入 KEK。 如需詳細資訊，請參閱 [Key Vault 文件](https://azure.microsoft.com/documentation/services/key-vault/)。

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

您可以移至 Azure Resource Manager 或使用金鑰保存庫介面來新增 KEK。

![Azure 金鑰保存庫](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>設定金鑰保存庫的權限
Azure 平台需要存取您金鑰保存庫中的加密金鑰或密碼，讓該資訊可供 VM 用來開機和解密磁碟區。 若要授與權限給 Azure 平台，使用金鑰保存庫的 PowerShell Cmdlet 設定金鑰保存庫中的 **EnabledForDiskEncryption** 屬性：

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

您也可以造訪 [Azure 資源 Explorer](https://resources.azure.com) 來設定 **EnabledForDiskEncryption** 屬性。

如之前所提及，您必須在金鑰保存庫上設定 **EnabledForDiskEncryption** 屬性。 否則，部署作業將會失敗。

您可以從金鑰保存庫介面設定 Azure AD 應用程式的存取原則，如下所示︰

![Azure 金鑰保存庫](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure 金鑰保存庫](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

在 [進階存取原則] 索引標籤中，請確定您已為 Azure 磁碟加密啟用金鑰保存庫︰

![Azure 金鑰保存庫](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>磁碟加密部署案例和使用者體驗
您可以啟用許多磁碟加密案例，步驟可能會因案例而有所不同。 下列各節涵蓋更詳細的案例。

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>在透過 Marketplace 所建立的新 IaaS VM 上啟用加密
您可以使用 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)，透過 Azure 中的 Marketplace 在新 IaaS Windows VM 上啟用磁碟加密。

1. 在 Azure 快速入門範本上按一下 [部署至 Azure]，在 [參數] 刀鋒視窗中輸入加密組態，然後按一下 [確定]。

2. 選取訂用帳戶、資源群組、資源群組位置、法律條款與合約，然後按一下 [建立] 以在新 IaaS VM 上啟用加密。

> [!NOTE]
> 此範本會建立使用 Windows Server 2012 資源庫映像的新加密 Windows VM。

您可以使用此 [Resource Manager 範本](https://aka.ms/fde-rhel)，在具有 200 GB RAID-0 陣列的新 IaaS RedHat Linux 7.2 VM 上啟用磁碟加密。 在您部署範本之後，請確認 VM 加密狀態，方法是使用[在執行中的 Linux VM 上加密 OS 磁碟機](#encrypting-os-drive-on-a-running-linux-vm)中所述的 `Get-AzureRmVmDiskEncryptionStatus` Cmdlet。 當電腦傳回 _VMRestartPending_ 的狀態，重新啟動 VM。

針對透過 Marketplace 案例使用 Azure AD 用戶端識別碼的新 VM，以下資料表列出其 Resource Manager 範本參數︰

| 參數 | 說明 |
| --- | --- |
| adminUserName | 虛擬機器的系統管理使用者名稱。 |
| adminPassword | 虛擬機器的系統管理使用者密碼。 |
| newStorageAccountName | 要儲存作業系統和資料 VHD 的儲存體帳戶名稱。 |
| vmSize | VM 的大小。 目前僅支援標準的 A、D 和 G 系列。 |
| virtualNetworkName | VM NIC 應該隸屬的 VNet 名稱。 |
| subnetName | VM NIC 應該隸屬的 VNet 中子網路的名稱。 |
| AADClientID | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端識別碼。 |
| AADClientSecret | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端密碼。 |
| keyVaultURL | 應上傳 BitLocker 金鑰的金鑰保存庫 URL。 您可以使用 Cmdlet `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI` 來取得它。 |
| keyEncryptionKeyURL | 用來加密所產生 BitLocker 金鑰的金鑰加密金鑰 URL (選擇性)。 |
| keyVaultResourceGroup | 金鑰保存庫的資源群組。 |
| vmName | 要執行加密作業所在 VM 的名稱。 |

> [!NOTE]
> _KeyEncryptionKeyURL_ 是選擇性參數。 您可以使用自己的 KEK，在金鑰保存庫中進一步保護資料加密金鑰 (複雜密碼)。

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>在透過客戶加密 VHD 和加密金鑰所建立的新 IaaS VM 上啟用加密
在這個案例中，您可以使用 Resource Manager 範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 下列各節將更加詳細地說明 Resource Manager 範本和 CLI 命令。

遵循下列其中一節的指示來準備可用於 Azure 的預先加密映像。 映像建立之後，您可以使用下一節的步驟來建立加密的 Azure VM。

* [準備已預先加密的 Windows VHD](#preparing-a-pre-encrypted-windows-vhd)
* [準備已預先加密的 Linux VHD](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>使用 Resource Manager 範本
您可以使用 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)在加密的 VHD 上啟用磁碟加密。

1. 在 Azure 快速入門範本上按一下 [部署至 Azure]，在 [參數] 刀鋒視窗中輸入加密組態，然後按一下 [確定]。

2. 選取訂用帳戶、資源群組、資源群組位置、法律條款與合約，然後按一下 [建立] 以在新 IaaS VM 上啟用加密。

以下資料表列出加密 VHD 的 Resource Manager 範本參數︰

| 參數 | 說明 |
| --- | --- |
| newStorageAccountName | 要儲存加密的作業系統 VHD 的儲存體帳戶名稱。 此儲存體帳戶應該已建立在與 VM 相同的資源群組和相同的位置中。 |
| osVhdUri | 來自儲存體帳戶作業系統 VHD 的 URI。 |
| osType | 作業系統產品類型 (Windows/Linux)。 |
| virtualNetworkName | VM NIC 應該隸屬的 VNet 名稱。 名稱應該已建立在與 VM 相同的資源群組和相同的位置中。 |
| subnetName | VM NIC 應該隸屬的 VNet 上子網路的名稱。 |
| vmSize | VM 的大小。 目前僅支援標準的 A、D 和 G 系列。 |
| keyVaultResourceID | 在 Azure Resource Manager 中識別金鑰保存庫資源的 ResourceID。 您可以使用 PowerShell Cmdlet `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId` 來取得它。 |
| keyVaultSecretUrl | 在金鑰保存庫中所設定的磁碟加密金鑰的 URL。 |
| keyVaultKekUrl | 用來加密所產生磁碟加密金鑰的金鑰加密金鑰的 URL。 |
| vmName | IaaS VM 的名稱。 |

#### <a name="using-powershell-cmdlets"></a>使用 PowerShell Cmdlet
您可以使用 PowerShell Cmdlet [`Set-AzureRmVMOSDisk`](/powershell/module/azurerm.compute/set-azurermvmosdisk) 在加密的 VHD 上啟用磁碟加密。  

#### <a name="using-cli-commands"></a>使用 CLI 命令
若要使用 CLI 命令啟用此案例的磁碟加密，請執行下列步驟：

1. 設定金鑰保存庫的存取原則：

   * 設定 **EnabledForDiskEncryption** 旗標︰

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * 設定權限給 Azure AD 應用程式，以將密碼寫入至您的金鑰保存庫：

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. 若要在現有或執行中的 VM 上啟用加密，請輸入︰

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. 取得加密狀態：

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. 若要從您加密的 VHD 啟用新 VM 上的加密，請使用以下參數搭配 `azure vm create` 命令：

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>在 Azure 中現有或執行中的 IaaS Windows VM 上啟用加密
在這個案例中，您可以使用 Resource Manager 範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 下列各節將更加詳細地說明如何使用 Resource Manager 範本和 CLI 命令來啟用它。

#### <a name="using-the-resource-manager-template"></a>使用 Resource Manager 範本
您可以使用 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)，在 Azure 中現有或執行中的 IaaS Windows VM 上啟用磁碟加密。

1. 在 Azure 快速入門範本上按一下 [部署至 Azure]，在 [參數] 刀鋒視窗中輸入加密組態，然後按一下 [確定]。

2. 選取訂用帳戶、資源群組、資源群組位置、法律條款與合約，然後按一下 [建立] 以在現有或執行中的 IaaS VM 上啟用加密。

針對使用 Azure AD 用戶端識別碼的現有或執行中 VM，以下資料表列出其 Resource Manager 範本參數︰

| 參數 | 說明 |
| --- | --- |
| AADClientID | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端識別碼。 |
| AADClientSecret | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端密碼。 |
| keyVaultName | 應上傳 BitLocker 金鑰的金鑰保存庫名稱。 您可以使用 Cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname` 來取得它。 |
|  keyEncryptionKeyURL | 用來加密所產生 BitLocker 金鑰的金鑰加密金鑰 URL。 如果您在 UseExistingKek 下拉式清單中選取 [nokek]，此參數是選擇性的。 如果您在 UseExistingKek 下拉式清單中選取 [kek]，您必須輸入 _keyEncryptionKeyURL_ 值。 |
| volumeType | 執行加密作業所在磁碟區的類型。 有效值為 _OS_、_Data_ 和 _All_。 |
| sequenceVersion | BitLocker 作業的順序版本。 每當在相同的 VM 上執行磁碟加密作業時便遞增此版本號碼。 |
| vmName | 要執行加密作業所在 VM 的名稱。 |

> [!NOTE]
> _KeyEncryptionKeyURL_ 是選擇性參數。 您可以使用自己的 KEK，在金鑰保存庫中進一步保護資料加密金鑰 (BitLocker 加密密碼)。

#### <a name="using-powershell-cmdlets"></a>使用 PowerShell Cmdlet
如需使用 PowerShell Cmdlet 以 Azure 磁碟加密來啟用加密的相關資訊，請參閱部落格文章[探索使用 Azure PowerShell 的 Azure 磁碟加密 - 第 1 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx)和[探索使用 Azure PowerShell 的 Azure 磁碟加密 - 第 2 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)。

#### <a name="using-cli-commands"></a>使用 CLI 命令
若要在 Azure 中使用 CLI 命令來啟用現有或執行中 IaaS Windows VM 上的加密，請執行下列步驟：

1. 若要設定金鑰保存庫的存取原則：
   * 設定 **EnabledForDiskEncryption** 旗標︰

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * 設定權限給 Azure AD 應用程式，以將密碼寫入至您的金鑰保存庫：

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. 若要在現有或執行中的 VM 上啟用加密︰

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. 若要取得加密狀態：

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. 若要從您加密的 VHD 啟用新 VM 上的加密，請使用以下參數搭配 `azure vm create` 命令：

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>在 Azure 中現有或執行中的 IaaS Linux VM 上啟用加密
您可以使用 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)，在 Azure 中現有或執行中的 IaaS Linux VM 上啟用磁碟加密。

1. 在 Azure 快速入門範本上按一下 [部署至 Azure]，在 [參數] 刀鋒視窗中輸入加密組態，然後按一下 [確定]。

2. 選取訂用帳戶、資源群組、資源群組位置、法律條款與合約，然後按一下 [建立] 以在現有或執行中的 IaaS VM 上啟用加密。

針對使用 Azure AD 用戶端識別碼的現有或執行中 VM，以下資料表列出其 Resource Manager 範本參數︰

| 參數 | 說明 |
| --- | --- |
| AADClientID | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端識別碼。 |
| AADClientSecret | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端密碼。 |
| keyVaultName | 應上傳 BitLocker 金鑰的金鑰保存庫名稱。 您可以使用 Cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname` 來取得它。 |
|  keyEncryptionKeyURL | 用來加密所產生 BitLocker 金鑰的金鑰加密金鑰 URL。 如果您在 UseExistingKek 下拉式清單中選取 [nokek]，此參數是選擇性的。 如果您在 UseExistingKek 下拉式清單中選取 [kek]，您必須輸入 _keyEncryptionKeyURL_ 值。 |
| volumeType | 執行加密作業所在磁碟區的類型。 支援的有效值為 _OS_ 或 _All_ (請參閱支援的 Linux 散發套件及其適用於作業系統與資料磁碟版本，述於先前的必要條件區段)。 |
| sequenceVersion | BitLocker 作業的順序版本。 每當在相同的 VM 上執行磁碟加密作業時便遞增此版本號碼。 |
| vmName | 要執行加密作業所在 VM 的名稱。 |
| passPhrase | 輸入強式複雜密碼做為資料加密金鑰。 |

> [!NOTE]
> _KeyEncryptionKeyURL_ 是選擇性參數。 您可以使用自己的 KEK，在金鑰保存庫中進一步保護資料加密金鑰 (複雜密碼)。

#### <a name="cli-commands"></a>CLI 命令
您可以安裝並使用 [CLI 命令](../cli-install-nodejs.md)在加密的 VHD 上啟用磁碟加密。 若要在 Azure 中使用 CLI 命令來啟用現有或執行中 IaaS Linux VM 上的加密，請執行下列步驟：

1. 設定金鑰保存庫的存取原則：

 * 設定 **EnabledForDiskEncryption** 旗標︰

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * 設定權限給 Azure AD 應用程式，以將密碼寫入至您的金鑰保存庫：

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. 若要在現有或執行中的 VM 上啟用加密︰

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. 取得加密狀態：

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. 若要從您加密的 VHD 啟用新 VM 上的加密，請使用以下參數搭配 `azure vm create` 命令：
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>取得已加密 IaaS VM 的加密狀態
您可以使用 Azure Resource Manager、[PowerShell Cmdlet](/powershell/azure/overview) 或 CLI 命令取得加密狀態。 下列章節將說明如何使用 Azure 入口網站和 CLI 命令來取得加密狀態。

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>使用 Azure Resource Manager 取得已加密 Windows VM 的加密狀態
您可以從 Azure Resource Manager 取得 IaaS VM 的加密狀態，請執行以下步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後按一下左窗格中的 [虛擬機器] 以查看您訂用帳戶中虛擬機器的摘要檢視。 您可以從 [訂用帳戶] 下拉式清單中選取訂用帳戶名稱，以篩選虛擬機器檢視。

2. 在 [虛擬機器] 頁面頂端，按一下 [資料行]。

3. 從 [選擇資料行] 刀鋒視窗選取 [磁碟加密]，並按一下 [更新]。 您應該會看到磁碟加密資料行顯示出每個 VM 的加密狀態為_已啟用_或_未啟用_，如下圖所示：

 ![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>使用磁碟加密 PowerShell Cmdlet 取得已加密 (Windows/Linux) IaaS VM 的加密狀態
您可以透過磁碟加密 PowerShell Cmdlet `Get-AzureRmVMDiskEncryptionStatus` 來取得 IaaS VM 的加密狀態。 若要取得 VM 的加密設定，請輸入以下資訊︰

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

您可以檢查 _Get-AzureRmVMDiskEncryptionStatus_ 的輸出來取得加密金鑰 URL。

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

OSVolumeEncrypted 和 DataVolumesEncrypted 的設定值設定為 _Encrypted_，顯示這兩個磁碟區都是使用 Azure 磁碟加密來加密。 如需使用 PowerShell Cmdlet 以 Azure 磁碟加密來啟用加密的相關資訊，請參閱部落格文章[探索使用 Azure PowerShell 的 Azure 磁碟加密 - 第 1 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx)和[探索使用 Azure PowerShell 的 Azure 磁碟加密 - 第 2 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)。

> [!NOTE]
> 在 Linux VM 上，`Get-AzureRmVMDiskEncryptionStatus` Cmdlet 報告加密狀態需要三至四分鐘。

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>透過磁碟加密 CLI 命令取得 IaaS VM 的加密狀態
您可以使用磁碟加密 CLI 命令 `azure vm show-disk-encryption-status` 取得 IaaS VM 的加密狀態。 若要取得 VM 的加密設定，請進入您的 Azure CLI 工作階段：

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>在執行中 Windows IaaS VM 上停用加密
您可以透過 Azure 磁碟加密 Resource Manager 範本或 PowerShell Cmdlet，在執行中 Windows 或 Linux IaaS VM 上停用加密，並指定解密組態。

##### <a name="windows-vm"></a>Windows VM
停用加密步驟會在執行中 Windows IaaS VM 上停用 OS、資料磁碟區或兩者的加密。 您無法停用 OS 磁碟區並將資料磁碟區保持加密狀態。 執行停用加密步驟時，Azure 傳統部署模型會更新 VM 服務模型，且 Windows IaaS VM 會標示為已解密。 VM 的待用內容不會再加密。 解密並不會刪除您的金鑰保存庫和加密金鑰資料 (Windows 系統的 BitLocker 加密金鑰或 Linux 的複雜密碼)。

##### <a name="linux-vm"></a>Linux VM
停用加密步驟會在執行中 Linux IaaS VM 上停用資料磁碟區的加密。 此步驟僅適用於 OS 磁碟機未加密的情況。

> [!NOTE]
> Linux VM 不允許在 OS 磁碟上停用加密。

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>在現有或執行中 IaaS VM 上停用加密
您可以使用 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)，在執行中 Windows IaaS VM 上停用磁碟加密。

1. 在 Azure 快速入門範本上按一下 [部署至 Azure]，在 [參數] 刀鋒視窗中輸入解密組態，然後按一下 [確定]。

2. 選取訂用帳戶、資源群組、資源群組位置、法律條款與合約，然後按一下 [建立] 以在新 IaaS VM 上啟用加密。

對於 Linux VM，您可以使用[在執行中的 Linux VM 上停用加密](https://aka.ms/decrypt-linuxvm)範本來停用加密。

以下資料表列出可供在執行中 IaaS VM 上停用加密的 Resource Manager 範本參數︰

| 參數 | 說明 |
| --- | --- |
| vmName | 要執行加密作業所在 VM 的名稱。
| volumeType | 執行解密作業所在磁碟區的類型。 有效值為 _OS_、_Data_ 和 _All_。 若未在 _Data_ 磁碟區上停用加密，則無法在執行中 Windows IaaS VM OS/開機磁碟區上停用加密。 也請注意 Linux VM 不允許在 OS 磁碟上停用加密。 |
| sequenceVersion | BitLocker 作業的順序版本。 每當在相同的 VM 上執行磁碟解密作業時便遞增此版本號碼。 |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>在現有或執行中 IaaS VM 上停用加密
若要使用 PowerShell Cmdlet 在現有或執行中 IaaS VM 上停用加密，請參閱 [`Disable-AzureRmVMDiskEncryption`](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption)。 此 Cmdlet 同時支援 Windows 和 Linux VM。 若要停用加密，它會在虛擬機器上安裝擴充功能。 如果未指定 _Name_ 參數，則會建立使用預設名稱 _AzureDiskEncryption for Windows VMs_ 的擴充功能。

在 Linux VM 上會使用 AzureDiskEncryptionForLinux 擴充功能。

> [!NOTE]
> 此 Cmdlet 會重新啟動虛擬機器。

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>在具有 Azure 受管理磁碟且預先加密的 IaaS VM 上啟用加密
使用 Azure 受管理磁碟 ARM 範本，以使用位於以下位置的 ARM 範本從預先加密的 VHD 建立加密的 VM   
[從預先加密的 VHD/儲存體 blob 建立新的已加密受管理磁碟] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>在具有 Azure 受管理磁碟的新 Linux IaaS VM 上啟用加密
使用 Azure 受管理磁碟 ARM 範本，以使用位於以下位置的 ARM 範本建立新的已加密 Linux IaaS VM   
[使用完整磁碟加密部署 RHEL 7.2] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>在具有 Azure 受管理磁碟的新 Windows IaaS VM 上啟用加密
 使用 Azure 受管理磁碟 ARM 範本，以使用位於以下位置的 ARM 範本建立新的已加密 Linux IaaS VM   
 [從資源庫映像建立新的已加密 Windows IaaS 受管理磁碟 VM] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >您必須在啟用 Azure 磁碟加密之前，先在 Azure 磁碟加密之外對以受控磁碟為基礎的 VM 執行個體建立快照集和/或備份。  您可以從入口網站建立受控磁碟的快照集，也可以使用 Azure 備份來建立。  擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。  在建立備份後，您就可以使用 Set-AzureRmVMDiskEncryptionExtension Cmdlet 並指定 -skipVmBackup 參數來加密受控磁碟。  在建立好備份並指定了這個參數之前，對以受控磁碟為基礎的 VM 執行這個命令都將會失敗。    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>現有已加密非高階 VM 的更新加密設定
  針對執行中的 VM 使用現有 Azure 磁碟加密支援的介面 [PS Cmdlet、CLI 或 ARM 範本] 更新加密設定，如 AAD 用戶端識別碼/密碼、金鑰加密金鑰 [KEK]、Windows VM 適用的 BitLocker 加密金鑰，或 Linux VM 適用的複雜密碼等。進階與非進階儲存體 VM 皆支援更新加密設定。

## <a name="appendix"></a>附錄
### <a name="connect-to-your-subscription"></a>連線至您的訂用帳戶
在繼續之前，請先參閱本文中＜必要條件＞一節。 確定已符合所有必要條件之後，請透過下列方式連接至您的訂用帳戶︰

1. 開始 Azure PowerShell 工作階段，並使用下列命令登入您的 Azure 帳戶：

    `Login-AzureRmAccount`

2. 如果您有多個訂用帳戶，並想要指定要使用的其中一個，請輸入下列內容以查看您帳戶的訂用帳戶：

    `Get-AzureRmSubscription`

3. 若要指定要使用的訂用帳戶，請輸入：

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. 若要確認訂用帳戶設定正確無誤，請輸入：

    `Get-AzureRmSubscription`

5. 若要確認已安裝 Azure 磁碟加密 Cmdlet，請輸入：

    `Get-command *diskencryption*`

6. 下列的輸出可以確認 Azure 磁碟加密 PowerShell 的安裝：

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>準備已預先加密的 Windows VHD
下列各節是準備預先加密的 Windows VHD 以在 Azure IaaS 中部署為加密的 VHD 的必要項目。 使用該資訊以在 Azure Site Recovery 或 Azure 上準備並啟動全新的 Windows VM (VHD)。

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>更新群組原則以對作業系統保護允許非 TPM
設定 **BitLocker 磁碟機加密**的 BitLocker 群組原則設定，其位於此路徑**本機電腦原則** > **電腦設定** > **系統管理範本** > **Windows 元件**。 將這個設定變更為**作業系統磁碟機** > **啟動時需要其他驗證** > **在不含相容 TPM 的情形下允許使用 BitLocker**，如下圖所示：

![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>安裝 BitLocker 功能元件
針對 Windows Server 2012 和更新版本，請使用下列命令︰

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

針對 Windows Server 2008 R2，請使用下列命令︰

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>使用 `bdehdcfg` 準備用於 BitLocker 的 OS 磁碟區
若要壓縮作業系統磁碟分割並為 BitLocker 準備電腦，請執行下列命令：

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>使用 BitLocker 保護作業系統磁碟區
使用 [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) 命令，以使用外部的金鑰保護裝置在開機磁碟區上啟用加密。 也將外部金鑰 (.bek 檔案) 放置於外部磁碟機或磁碟區。 下次重新開機後，會在系統/開機磁碟區上啟用加密。

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> 使用不同的資料/資源 VHD 來準備 VM，才能使用 BitLocker 取得外部金鑰。

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>在執行中的 Linux VM 上加密 OS 磁碟機
下列散發套件支援在執行中的 Linux VM 上加密 OS 磁碟機︰

* RHEL 7.2
* CentOS 7.2
* Ubuntu 16.04

##### <a name="prerequisites-for-os-disk-encryption"></a>OS 磁碟加密的必要條件

* 必須從 Azure Resource Manager 的Marketplace 映像建立 VM。
* 具有至少 4 GB RAM 的 Azure VM (建議大小為 7 GB)。
* (適用於 RHEL 和 CentOS) 停用 SELinux。 若要停用 SELinux，請參閱「4.4.2. 停用 SELinux」，其位於 VM 上的 [SELinux 使用者和系統管理員指南](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux)。
* 停用 SELinux 之後，至少重新啟動 VM 一次。

##### <a name="steps"></a>步驟
1. 使用先前指定的其中一個散發套件來建立 VM。

 若為 CentOS 7.2，支援透過特殊映像加密 OS 磁碟。 若要使用此映像，請在建立 VM 時指定 "7.2n" 做為 SKU︰
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. 根據您的需求設定 VM。 如果您要加密所有 (OS + 資料) 磁碟機，必須可從 /etc/fstab 指定和掛接資料磁碟機。

 > [!NOTE]
 > 使用 UUID=... 來指定 /etc/fstab 中的資料磁碟機，而不是指定區塊裝置名稱 (例如 /dev/sdb1)。 加密期間，VM 上的磁碟機順序會變更。 如果 VM 依賴特定的區塊裝置順序，則無法在加密後掛接裝置。

3. 登出 SSH 工作階段。

4. 若要加密 OS，請在[啟用加密](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure)時將 volumeType 指定為 **All** 或 **OS**。

 > [!NOTE]
 > 未以 `systemd` 服務的形式執行的所有使用者空間程序皆應使用 `SIGKILL` 來終止。 重新啟動 VM。 當您在執行中的 VM 上啟用 OS 磁碟加密時，請規劃 VM 停機時間。

5. 使用[下一節](#monitoring-os-encryption-progress)的指示定期監視加密進度。

6. 在 Get-AzureRmVmDiskEncryptionStatus 顯示「VMRestartPending」之後，請登入 VM 或使用入口網站、PowerShell 或 CLI 來重新啟動 VM。
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
重新開機之前，我們建議您儲存 VM 的[開機診斷](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/)。

#### <a name="monitoring-os-encryption-progress"></a>監視 OS 加密進度
有三種方式可監視 OS 加密進度：

* 使用 `Get-AzureRmVmDiskEncryptionStatus` Cmdlet，並查看 ProgressMessage 欄位︰
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 VM 達到「OS disk encryption started」之後，需要在進階儲存體所支援的 VM 上耗費大約 40 至 50 分鐘。

 由於 WALinuxAgent 發生 [388 號問題](https://github.com/Azure/WALinuxAgent/issues/388)，某些散發套件的 `OsVolumeEncrypted` 和 `DataVolumesEncrypted` 會顯示為 `Unknown`。 若使用 WALinuxAgent 2.1.5 和更新版本，就會自動修正此問題。 如果您在輸出中看到 `Unknown`，可以使用 Azure 資源 Explorer 確認磁碟加密狀態。

 移至 [Azure 資源 Explorer](https://resources.azure.com/)，然後在左側選取面板中展開這個階層︰

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 在 InstanceView 中，向下捲動以查看磁碟機的加密狀態。

 ![VM 執行個體檢視](./media/azure-security-disk-encryption/vm-instanceview.png)

* 查看[開機診斷](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/)。 來自 ADE 擴充功能的訊息應該會在前面加上 `[AzureDiskEncryption]`。

* 透過 SSH 登入 VM，並從下列位置取得擴充功能記錄檔：

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 不建議在 OS 加密進行時登入 VM。 其他兩個方法都失敗時，只複製記錄檔。

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>準備已預先加密的 Linux VHD
##### <a name="ubuntu-16"></a>Ubuntu 16
在發佈安裝期間執行下列步驟以設定加密︰

1. 在分割磁碟時選取 [設定加密的磁碟區]。

 ![Ubuntu 16.04 設定](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. 另外建立一個不得加密的開機磁碟機。 加密根磁碟機。

 ![Ubuntu 16.04 設定](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. 提供複雜密碼。 這是您要上傳至金鑰保存庫的複雜密碼。

 ![Ubuntu 16.04 設定](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. 完成分割。

 ![Ubuntu 16.04 設定](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. 啟動 VM 時會要求輸入複雜密碼，請使用步驟 3 中提供的複雜密碼。

 ![Ubuntu 16.04 設定](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. 使用[這些指示](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/)準備要上傳到 Azure 的 VM。 還不要執行最後一個步驟 (解除佈建 VM)。

設定加密來與 Azure 搭配運作，請執行下列步驟：

1. 在 /usr/local/sbin/azure_crypt_key.sh 下，使用以下指令碼的內容建立檔案。 請注意 KeyFileName，因為它是 Azure 使用的複雜密碼檔案名稱。

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. 在 */etc/crypttab* 中變更 crypt 設定。 它看起來應該如下所示：
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. 如果您正在 Windows 中編輯 *azure_crypt_key.sh* 並將它複製到 Linux，執行 `dos2unix /usr/local/sbin/azure_crypt_key.sh`。

4. 在指令碼中新增可執行檔權限︰
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. 以加上程式碼行的方式編輯 /etc/initramfs-tools/modules：
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. 執行 `update-initramfs -u -k all` 來更新 initramfs，以讓 `keyscript` 生效。

7. 現在您可以取消佈建 VM。

 ![Ubuntu 16.04 設定](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. 繼續進行下一個步驟，並[將 VHD 上傳](#upload-encrypted-vhd-to-an-azure-storage-account)到 Azure。

##### <a name="opensuse-132"></a>openSUSE 13.2
若要在發佈安裝期間設定加密，請執行下列步驟︰
1. 分割磁碟時，請選取 [加密磁碟區群組]，然後輸入密碼。 這是您要上傳到金鑰保存庫的密碼。

 ![openSUSE 13.2 設定](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. 使用您的密碼啟動 VM。

 ![openSUSE 13.2 設定](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. 請依照[準備 Azure 的 SLES 或 openSUSE 虛擬機器](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131)中的指示準備 VM 以上傳至 Azure。 還不要執行最後一個步驟 (解除佈建 VM)。

若要設定加密來與 Azure 搭配運作，請執行下列步驟：
1. 編輯 /etc/dracut.conf 並新增下面這一行︰
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. 註解化 /usr/lib/dracut/modules.d/90crypt/module-setup.sh 檔案結尾的這幾行程式碼：
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. 在 /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh 檔案開頭附加下面這一行：
 ```
    DRACUT_SYSTEMD=0
 ```
變更所有出現的：
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
至：
```
    if [ 1 ]; then
```
4. 編輯 /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh 並將它附加至「# Open LUKS device」：

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. 執行 `/usr/sbin/dracut -f -v` 以更新 initrd。

6. 現在您可以取消佈建 VM，並[將 VHD 上傳](#upload-encrypted-vhd-to-an-azure-storage-account)到 Azure。

##### <a name="centos-7"></a>CentOS 7
若要在發佈安裝期間設定加密，請執行下列步驟︰
1. 在分割磁碟時選取 [加密資料]。

 ![CentOS 7 設定](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. 確定已為根分割選取 [加密]。

 ![CentOS 7 設定](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. 提供複雜密碼。 這是您要上傳到金鑰保存庫的複雜密碼。

 ![CentOS 7 設定](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. 啟動 VM 時會要求輸入複雜密碼，請使用步驟 3 中提供的複雜密碼。

 ![CentOS 7 設定](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. 請依照[準備 Azure 的 CentOS 式虛擬機器](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70)中的「CentOS 7.0+」指示準備 VM 以上傳至 Azure。 還不要執行最後一個步驟 (解除佈建 VM)。

6. 現在您可以取消佈建 VM，並[將 VHD 上傳](#upload-encrypted-vhd-to-an-azure-storage-account)到 Azure。

若要設定加密來與 Azure 搭配運作，請執行下列步驟：

1. 編輯 /etc/dracut.conf 並新增下面這一行︰
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. 註解化 /usr/lib/dracut/modules.d/90crypt/module-setup.sh 檔案結尾的這幾行程式碼：
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. 在 /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh 檔案開頭附加下面這一行：
```
    DRACUT_SYSTEMD=0
```
變更所有出現的：
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
to
```
    if [ 1 ]; then
```
4. 編輯 /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh 並將此附加在「# Open LUKS device」之後：
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. 執行「/usr/sbin/dracut -f -v」來更新 initrd。

![CentOS 7 設定](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>上傳加密的 VHD 至 Azure 儲存體帳戶
啟用 BitLocker 加密或 DM-Crypt 加密之後，需要將本機加密的 VHD 上傳至儲存體帳戶。

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>上傳已預先加密 VM 的磁碟加密密碼至金鑰保存庫
先前取得的磁碟加密密碼必須上傳，做為金鑰保存庫中的密碼。 金鑰保存庫必須為 Azure AD 用戶端啟用磁碟加密以及權限。

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>未使用 KEK 加密的磁碟加密密碼
若要在金鑰保存庫中設定密碼，請使用 [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret)。 如果您有 Windows 虛擬機器，bek 檔案會以 base64 字串編碼，然後使用 `Set-AzureKeyVaultSecret` Cmdlet 上傳至您的金鑰保存庫。 如果是 Linux，複雜密碼會以 base64 字串編碼，然後上傳至金鑰保存庫。 此外，請確定在金鑰保存庫中建立密碼時會設定下列標籤。

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

在下一個步驟中使用 `$secretUrl`，以便[在不使用 KEK 的狀況下連接 OS 磁碟](#without-using-a-kek)。

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>使用 KEK 加密的磁碟加密密碼
將密碼上傳至金鑰保存庫之前，您可以選擇性地使用金鑰加密金鑰來加密密碼。 使用包裝 [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) 先加密使用金鑰加密金鑰的密碼。 這個包裝作業的輸出是 base64 URL 編碼的字串，您可以接著使用 [`Set-AzureKeyVaultSecret`](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) Cmdlet 將它上傳做為密碼。

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

在下一個步驟中使用 `$KeyEncryptionKey` 和 `$secretUrl`，以便[使用 KEK 連接 OS 磁碟](#using-a-kek)。

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>連接 OS 磁碟時，指定密碼的 URL
#### <a name="without-using-a-kek"></a>不使用 KEK
當您在連接 OS 磁碟時，需要傳遞 `$secretUrl`。 URL 已在「未使用 KEK 加密的磁碟加密密碼」一節中產生。

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>使用 KEK
連接 OS 磁碟時，傳遞 `$KeyEncryptionKey` 和 `$secretUrl`。 URL 已在「未使用 KEK 加密的磁碟加密密碼」一節中產生。

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>下載此指南
您可以從 [TechNet 資源庫](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)下載此指南。

## <a name="for-more-information"></a>取得詳細資訊
[探索使用 Azure PowerShell 的 Azure 磁碟加密 - 第 1 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[探索使用 Azure PowerShell 的 Azure 磁碟加密 - 第 2 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
