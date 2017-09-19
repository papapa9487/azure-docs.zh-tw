title: Azure 使用加密保護個人待用資料 | Microsoft Docs description: 本文是協助您使用 Azure 來保護個人資料服務之系列的一部分: security documentationcenter: na author: Barclayn manager: MBaldwin editor: TomSh

ms.assetid: ms.service: security ms.devlang: na ms.topic: article ms.tgt_pltfrm: na ms.workload: na ms.date: 08/22/2017 ms.author: barclayn ms.custom: 

---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>Azure 加密技術：使用加密保護個人待用資料

本文可協助您了解及使用 Azure 加密技術來保護待用資料。

待用資料加密是保護敏感性或個人資料，以及符合合規性和資料隱私權需求之最佳做法不可或缺的一部分。
靜態加密旨在防止攻擊者存取未加密的資料，方法是確保資料在磁碟上時就已加密。

## <a name="scenario"></a>案例 

總部設於美國的大型郵輪公司將擴展其營運，以提供地中海、波羅的海和不列顛群島的行程。 為了支援這些工作，它收購了以義大利、德國、丹麥和英國為據點的數個較小型郵輪公司。

此公司使用 Microsoft Azure 在雲端儲存公司資料。 這可能包含類似如下的客戶及/或員工資訊：

- 地址
- 電話號碼
- 稅務識別編號
- 信用卡資訊

此公司必須保護客戶和員工資料的隱私權，同時讓需要的部門 (例如薪資部門和訂位部門) 可以存取資料。

此郵輪公司也會維護獎勵和忠誠度方案會員的大型資料庫，其中包含的個人資訊可追蹤與目前和過去客戶的關係。

### <a name="problem-statement"></a>問題陳述

此公司必須保護客戶和員工個人資料的隱私權，同時讓需要的部門 (例如薪資部門和訂位部門) 可以存取資料。 此個人資料儲存在公司控制的資料中心外部，因此不受公司的實體控制。

### <a name="company-goal"></a>公司目標

作為多層深度防禦安全性策略的一部分，公司目標是確保包含個人資料的所有資料來源都經過加密，包括位於雲端儲存體的資料來源。 如有未經授權的人員取得個人資料的存取權，必須採用讓資料變得無法讀取的格式。 對於使用者和系統管理員而言，套用加密應該很容易或很透明。

## <a name="solutions"></a>解決方案

Azure 服務提供多種工具和技術，可協助您透過加密來保護個人待用資料。

### <a name="azure-key-vault"></a>Azure 金鑰保存庫

[Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) 為用來加密 Azure 服務中待用資料的金鑰提供安全存放裝置，是建議的金鑰儲存和管理解決方案。 加密金鑰管理對保護儲存的資料很重要。

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>如何使用 Azure Key Vault 來保護加密個人資料的金鑰？

若要使用 Azure Key Vault，您需要 Azure 訂用帳戶。 您也需要安裝 Azure Powershell。 其步驟包括使用 PowerShell Cmdlet 來執行下列動作：

1. 連線到您的訂閱

2. 建立金鑰保存庫

3. 將金鑰或密碼加入至金鑰保存庫

4. 利用 Azure Active Directory 註冊將會使用金鑰保存庫的應用程式

5. 授權應用程式使用金鑰或密碼

若要建立金鑰保存庫，請使用 New-AzureRmKeyVault PowerShell Cmdlet。 您將會指派保存庫名稱、資源群組名稱和地理位置。 當您透過其他 Cmdlet 管理金鑰時，您將會使用保存庫名稱。 透過 REST API 使用保存庫的應用程式將會使用保存庫 URI。

Azure Key Vault 可為您提供受軟體保護的金鑰；或者，您也可以匯入 .PFX 檔案中的現有金鑰。 您也可以在保存庫中儲存密碼。

您也可以在本機 HSM 中產生金鑰，且在金鑰無需離開 HSM 界限的情況下，即可將它傳輸到 Key Vault 服務中的 HSM。

如需使用 Azure Key Vault 的詳細指示，請遵循[開始使用 Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-get-started) 中的步驟進行。

如需搭配 Azure Key Vault 使用的 PowerShell Cmdlet 清單，請參閱 [AzureRM.KeyVault](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0)。

### <a name="azure-disk-encryption-for-windows"></a>Windows 適用的 Azure 磁碟加密

[Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)會保護 Azure 虛擬機器上的個人待用資料，並與 Azure Key Vault 整合。 Azure 磁碟加密使用 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) (在 Windows 中) 及 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) (在 Linux 中) 來加密 OS 和資料磁碟。 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 以及 Windows 8 和 Windows 10 用戶端支援 Azure 磁碟加密。

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>如何使用 Azure 磁碟加密來保護個人資料？

若要使用 Azure 磁碟加密，您需要 Azure 訂用帳戶。 若要啟用 Windows 和 Linux VM 適用的 Azure 磁碟加密，請執行下列動作：

1. 使用 Azure 磁碟加密 Resource Manager 範本、PowerShell 或命令列介面 (CLI) 啟用磁碟加密，並指定加密設定。 

2. 授與 Azure 平台的存取權，以從金鑰保存庫讀取加密資料。

3. 提供 Azure Active Directory (AAD) 應用程式識別碼，以將加密金鑰資料寫入至金鑰保存庫。

Azure 將會更新 VM 和金鑰保存庫設定，並設定加密的 VM。

當您設定金鑰保存庫以支援 Azure 磁碟加密時，您可以新增金鑰加密金鑰 (KEK)，以提高安全性並支援備份加密的虛擬機器。

![](media/protect-personal-data-at-rest/create-key.png)

如需特定部署案例和使用者體驗的詳細指示，請參閱 [Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)。

### <a name="azure-storage-service-encryption"></a>Azure 儲存體服務加密

[Azure Storage Service Encryption (SSE) for Data at Rest](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption) (待用資料的 Azure 儲存體服務加密 (SSE)) 會協助您保護資料安全，以符合組織安全性和合規性承諾。 Azure 儲存體會先自動使用 256 位元 AES 加密來加密資料，再保存到儲存體，以及在擷取之前將它解密。 這個服務適用於 Azure Blob 和檔案。

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>如何使用儲存體服務加密來保護個人資料？

若要啟用儲存體服務加密，請執行下列動作：

1. 登入 Azure 入口網站。

2. 選取儲存體帳戶。

3. 在 [設定] 的 [Blob 服務] 區段下，選取 [加密]。

4. 在 [檔案服務] 區段下，選取 [加密]。

按一下 [加密] 設定之後，您可以啟用或停用「儲存體服務加密」。

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

新的資料將會加密。 此儲存體帳戶中現有檔案的資料將保持未加密狀態。

啟用加密之後，使用下列其中一個方法將資料複製到儲存體帳戶：

1. 使用 [AzCopy 命令列公用程式](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy)複製 Blob 或檔案。

2. [使用 SMB 掛接檔案共用](https://docs.microsoft.com/en-us/azure/storage/storage-file-how-to-use-files-windows)，因此您可以使用 Robocopy 等公用程式來複製檔案。

3. 使用 [.NET 等儲存體用戶端程式庫](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-how-to-use-blobs)，在 Blob 儲存體或儲存體帳戶之間來回複製 Blob 或檔案資料。

4.  使用[儲存體總管](https://docs.microsoft.com/en-us/azure/storage/storage-explorers)將 Blob 上傳至儲存體帳戶，並且啟用加密。

### <a name="transparent-data-encryption"></a>透明資料加密

透明資料加密 (TDE) 是 SQL Azure 功能，可讓您在資料庫和伺服器層級加密資料。 現在預設會在所有新建立的資料庫上啟用 TDE。 TDE 會執行資料和記錄檔的即時 I/O 加密和解密。

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>如何使用 TDE 來保護個人資料？

您可以透過 Azure 入口網站、使用 REST API 或使用 PowerShell 來設定 TDE。 若要使用 Azure 入口網站在現有的資料庫上啟用 TDE，請執行下列動作：

1. 前往 Azure 入口網站 (網址為 <https://portal.azure.com>)，並使用您的 Azure 系統管理員或參與者帳戶登入。

2. 在左橫幅上，按一下 [瀏覽]，然後按一下 SQL 資料庫。

3. 在左窗格中選取 SQL 資料庫時，按一下您的使用者資料庫。

4. 在資料庫刀鋒視窗中，按一下 [所有設定]。

5. 在 [設定] 刀鋒視窗中，按一下 [透明資料加密] 部分以開啟 [透明資料加密] 刀鋒視窗。

6. 在資料加密刀鋒視窗中，將 [資料加密] 按鈕移至 [開啟]，然後按一下 [儲存] \(在頁面頂端) 以套用設定。 加密狀態將會粗略估算透明資料加密的進度。

![啟用資料加密](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

如需如何啟用 TDE 的指示，以及將受 TDE 保護的資料庫解密的資訊及其他資訊，請參閱 [Azure SQL Database 的透明資料加密](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)一文。

## <a name="summary"></a>摘要

此公司可以完成加密儲存在 Azure 雲端中個人資料的目標。 他們可以透過使用 Azure 磁碟加密保護整個磁碟區來達成此目的。 這可能包含保存個人識別資訊和其他敏感性資料的作業系統檔案和資料檔案。 Azure 儲存體服務加密可用來保護儲存在 Blob 和檔案中的個人資料。 對於儲存在 Azure SQL Database 中的資料，透明資料加密會提供保護，以防止未經授權公開個人資訊。

此公司可以使用 Azure Key Vault，來保護用來加密 Azure 中資料的金鑰。 這會簡化金鑰管理程序，並可讓公司控管存取和加密個人資料的金鑰。

## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密疑難排解指南](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-tsg)

- [加密 Azure 虛擬機器](https://docs.microsoft.com/en-us/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Azure Data Lake Store 中的資料加密](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-encryption)

- [Azure Cosmos DB 資料庫待用加密](https://docs.microsoft.com/en-us/azure/cosmos-db/database-encryption-at-rest)
