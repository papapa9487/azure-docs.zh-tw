---
title: "Azure 加密概觀 | Microsoft Docs"
description: "了解 Azure 中的各種加密選項"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 752568747ab96bc0a9c7fc5f24ff28c3bce4e09f
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="azure-encryption-overview"></a>Azure 加密概觀

本文提供如何在 Microsoft Azure 中使用加密的概觀。 它涵蓋加密的主要領域，包括待用加密、傳輸中加密，以及 Key Vault 的金鑰管理。 每節都包含詳細資訊的連結。

## <a name="encryption-of-data-at-rest"></a>待用資料加密

待用資料包含位於實體媒體之永續性儲存體中任何數位格式的資訊。 這包括磁性或光學媒體上的檔案、已封存的資料，以及資料備份。 Microsoft Azure 提供各種資料儲存體解決方案來符合不同的需求，包括檔案、磁碟、Blob 和資料表儲存體。 Microsoft 也提供加密來保護 [Azure SQL Database](../sql-database/sql-database-technical-overview.md)、[CosmosDB](../cosmos-db/introduction.md) 和 Azure Data Lake。

待用資料加密適用於 Azure 軟體即服務 (SaaS)、平台即服務 (PaaS) 和基礎結構即服務 (IaaS) 雲端模型中的服務。 本文摘要說明並提供可協助您使用 Azure 加密選項的資源。

如需如何在 Azure 中加密待用資料的詳細討論，請參閱標題為 [Azure 待用資料加密](azure-security-encryption-atrest.md)的文件

## <a name="azure-encryption-models"></a>Azure 加密模型

Azure 支援各種加密模型，包括使用服務管理金鑰的伺服器端加密、在 Azure Key Vault 中使用客戶管理的金鑰，或在受客戶控制硬體上使用客戶管理的金鑰。 用戶端加密可讓您在內部部署或另一個安全位置中管理和儲存金鑰。

### <a name="client-side-encryption"></a>用戶端加密

用戶端加密是在 Azure 外部執行。 用戶端加密包括：

- 由客戶資料中心內執行的應用程式或服務應用程式加密的資料
- Azure 收到時便已加密的資料。

使用用戶端加密，雲端服務提供者無法存取加密金鑰，而且無法將此資料解密。 您會維持對金鑰的完全掌控。

### <a name="server-side-encryption"></a>伺服器端加密

有三種伺服器端加密模型提供不同的金鑰管理特性，可根據您的需求進行選擇。

- **服務管理的金鑰**結合控制能力與便利性，而且額外負荷很低

- **客戶管理的金鑰**可讓您控制金鑰，包括能夠攜帶您自己的金鑰 (BYOK) 或產生新的金鑰。

- **受客戶控制硬體中服務管理的金鑰**可讓您在不受 Microsoft 控制的專屬存放庫中管理金鑰。 這稱為託管您自己的金鑰 (HYOK)。 不過，設定很複雜，而且大多數 Azure 服務並不支援此模型。

### <a name="azure-disk-encryption"></a>Azure 磁碟加密

Windows 和 Linux 虛擬機器可以透過 [Azure 磁碟加密](azure-security-disk-encryption.md)保護，其使用 [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) 技術和 Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 透過完整磁碟區加密來保護作業系統磁碟和資料磁碟。

加密金鑰和密碼會在您的 [Azure Key Vault](../key-vault/key-vault-whatis.md) 訂用帳戶中受到保護。 您可以使用 Azure 備份服務，備份及還原透過 KEK 設定加密的已加密 VM。

### <a name="azure-storage-service-encryption"></a>Azure 儲存體服務加密

Azure 儲存體 (Blob 和檔案) 中的待用資料可以在伺服器端和用戶端案例中進行加密。

[Azure 儲存體服務加密](../storage/storage-service-encryption.md) (SSE) 可以在儲存資料前先加密資料，再於收到時自動將它解密，讓整個程序對使用者而言一目了然。 儲存體服務加密使用 256 位元 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)，這是可用的最強區塊編碼器之一，以透明的方式來處理加密、解密和金鑰管理。

### <a name="client-side-encryption-of-azure-blobs"></a>Azure Blob 的用戶端加密

Azure Blob 的用戶端加密可以透過不同的方式來執行。

您可以使用適用於 .NET NuGet 套件的 Azure 儲存體用戶端程式庫，將用戶端應用程式中的資料加密，再將它上傳至 Azure 儲存體。

若要深入了解並下載適用於 .NET NuGet 套件的 Azure 儲存體用戶端程式庫，請參閱標題為 [Windows Azure 儲存體 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage) 的文件

當您使用 Azure Key Vault 的用戶端加密時，您的資料會使用 Azure 儲存體用戶端 SDK 所產生的一次性對稱內容加密金鑰 (CEK) 進行加密。 使用金鑰加密金鑰 (KEK) 加密的 CEK 可以是對稱金鑰或非對稱金鑰組。 您可以在本機進行管理，或將它儲存在 Azure Key Vault 中。 然後，將加密的資料上傳至 Azure 儲存體服務。

若要深入了解 Azure Key Vault 的用戶端加密，並開始使用做法指示，請參閱標題為[教學課程：在 Microsoft Azure 儲存體中使用 Azure Key Vault 加密和解密 Blob](../storage/storage-encrypt-decrypt-blobs-key-vault.md) 的文件

最後，您也可以使用適用於 Java 的 Azure 儲存體用戶端程式庫，在上傳資料至 Azure 儲存體前先執行用戶端加密，然後於下載資料至用戶端時將它解密。 此程式庫也支援與 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 整合，以進行儲存體帳戶金鑰管理。

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>使用 Azure SQL Database 加密待用資料

[Azure SQL Database](../sql-database/sql-database-technical-overview.md) 是 Microsoft Azure 中的一般目的關聯式資料庫服務，可支援關聯式資料、JSON、空間和 XML 等結構。 Azure SQL 透過透明資料加密 (TDE) 功能支援伺服器端加密，並透過 Always Encrypted 功能支援用戶端加密。

#### <a name="transparent-data-encryption"></a>透明資料加密

您可以使用 [TDE 透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde)透過資料庫加密金鑰 (DEK) 來即時加密 [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016)、[Azure SQL Database](../sql-database/sql-database-technical-overview.md) 和 [Azure SQL 資料倉儲](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)資料檔案，該金鑰會儲存在資料庫開機記錄中，以在復原期間使用。

TDE 使用 AES 和 3DES 加密演算法來保護資料和記錄檔。 資料庫檔案的加密是在頁面層級執行；已加密資料庫中的頁面在寫入磁碟之前會進行加密，然後於讀入記憶體時進行解密。 現在預設會在新建立的 Azure SQL Database 上啟用 TDE。

#### <a name="always-encrypted"></a>一律加密

Azure SQL 中的 [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) 功能可讓您加密用戶端應用程式中的資料，然後儲存至 Azure SQL Database，並可讓您將內部部署資料庫管理作業委派給協力廠商，將擁有及可以檢視資料的人員與管理但不能存取資料的人員分開。

#### <a name="cellcolumn-level-encryption"></a>資料格/資料行層級加密

Azure SQL Database 可讓您使用 Transact-SQL 將對稱式加密套用至資料行。 這稱為[資料格層級加密或資料行層級加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) (CLE)，因為您可以使用它透過不同的加密金鑰加密特定資料行或更特定的資料格。 這提供您比 TDE 更細微的加密功能，來加密頁面中的資料。

CLE 有內建函式，可透過對稱或非對稱金鑰、憑證的公開金鑰，或使用 3DES 的複雜密碼來加密資料。

### <a name="cosmos-db-database-encryption"></a>Cosmos DB 資料庫加密

[Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) 是 Microsoft 之全域散發的多模型資料庫。 預設會加密儲存在 Cosmos DB 之非揮發性儲存體 (固態硬碟) 中的資料；沒有開啟或關閉的控制項。 待用加密是使用數種安全性技術來實作的，這些技術包括安全金鑰儲存體系統、加密的網路，以及密碼編譯 API。 加密金鑰是由 Microsoft 管理，並根據 Microsoft 內部方針來輪替。

### <a name="at-rest-encryption-in-azure-data-lake"></a>Azure Data Lake 的待用加密

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) 是企業級的存放庫，可於單一位置收集每種資料類型，再正式定義任何需求或結構描述。 Azure Data Lake Store 支援且「預設會開啟」透明待用資料加密，您可以在建立帳戶期間進行設定。 根據預設，Data Lake Store 會為您管理金鑰，但您可以選擇自行管理這些金鑰。

您有三種金鑰類型可用於加密和解密資料：主要加密金鑰 (MEK)、資料加密金鑰 (DEK) 和區塊加密金鑰 (BEK)。 MEK 可用來加密 DEK，並將它儲存在永續性媒體中，而 BEK 衍生自 DEK 和資料區塊。 如果您要管理自己的金鑰，您可以輪替 MEK。

## <a name="encryption-of-data-in-transit"></a>傳輸中資料加密

Azure 提供許多機制，可在將資料從一個位置移至另一個位置時，確保資料的隱私權。

### <a name="tlsssl-encryption-in-azure"></a>Azure 的 TLS/SSL 加密

在雲端服務與客戶之間移動資料時，Microsoft 使用[傳輸層安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) 通訊協定來保護資料。 Microsoft 的資料中心會交涉連線到 Azure 服務之用戶端系統的 TLS 連線。 TLS 提供增強式驗證、訊息隱私權、完整性 (可偵測訊息竄改、攔截和偽造)、互通性、演算法彈性，並方便部署和使用。

[完整轉寄密碼](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) 透過唯一金鑰來保護客戶的用戶端系統與 Microsoft 的雲端服務之間的連線。 這些連線也會使用 RSA 型 2048 位元加密金鑰長度。 這種組合讓其他人很難攔截和存取傳輸中的資料。

### <a name="azure-storage-transactions"></a>Azure 儲存體交易

當您透過 Azure 入口網站與 Azure 儲存體互動時，所有交易都會透過 HTTPS 進行。 您也可以透過 HTTPS 使用儲存體 REST API 來與 Azure 儲存體互動。 透過啟用儲存體帳戶所需的安全傳輸，您可於呼叫 REST API 來存取儲存體帳戶中的物件時強制使用 HTTPS。

共用存取簽章 ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)) 可用來委派 Azure 儲存體物件的存取權，包含一個選項，可指定在使用共用存取簽章時只能使用 HTTPS 通訊協定。 如此可確保任何使用 SAS 權杖送出連結的人都將使用正確的通訊協定。

用來存取 Azure 檔案共用的 [SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption) 支援加密，而且可在 Windows Server 2012 R2、Windows 8、Windows 8.1 和 Windows 10 中使用，允許跨區域存取，甚至是電腦上的存取。

用戶端加密會將資料加密再傳送至 Azure 儲存體，以在透過網路傳送時處於加密狀態。

### <a name="smb-encryption-over-azure-virtual-networks"></a>透過 Azure 虛擬網路的 SMB 加密 

執行 Windows Server 2012 和更新版本之 Azure VM 中的 [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) 藉由加密透過 Azure 虛擬網路傳輸的資料，讓您能夠安全地傳輸資料，以防止竄改和竊聽攻擊。 系統管理員可以針對整個伺服器啟用 SMB 加密，或僅針對特定共用來啟用。

根據預設，一旦開啟共用或伺服器的 SMB 加密，只有 SMB 3 用戶端才能存取加密的共用。

## <a name="in-transit-encryption-in-azure-virtual-machines"></a>Azure 虛擬機器的傳輸中加密

在執行 Windows 的 Azure VM 之間來回傳輸的資料可根據連線本質透過數種方式加密。

### <a name="rdp-sessions"></a>RDP 工作階段

您可以從 Windows 用戶端電腦或已安裝 RDP 用戶端的 Mac，使用[遠端桌面通訊協定](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) (RDP) 連線到 Azure VM 並進行登入。 RDP 工作階段中透過網路傳輸的資料可由 TLS 保護。

您也可以使用遠端桌面連線到 Azure 中的 Linux VM。

### <a name="secure-access-to-linux-vms-with-ssh"></a>使用 SSH 對 Linux VM 進行安全存取

您可以使用 [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) 連線到 Azure 中執行的 Linux VM ，以便進行遠端管理。 SSH 是允許透過不安全的連線進行安全登入的加密連線通訊協定。 它是 Azure 中裝載之 Linux VM 的預設連線通訊協定。 使用 SSH 金鑰進行驗證時，不需要密碼就可以登入。 SSH 使用公開/私密金鑰組 (非對稱式加密) 進行驗證。

## <a name="azure-vpn-encryption"></a>Azure VPN 加密

您可以透過虛擬私人網路連線到 Azure，以建立安全通道來保護透過網路所傳送資料的隱私權。

### <a name="azure-vpn-gateway"></a>Azure VPN 閘道

[Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)可透過公用連線在您的虛擬網路和內部部署位置之間傳送加密流量，或在虛擬網路之間傳送流量。

站對站 VPN 使用 [IPsec](https://en.wikipedia.org/wiki/IPsec) 進行傳輸加密。 Azure VPN 閘道使用一組預設提案。 您可以設定 Azure VPN 閘道搭配特定密碼編譯演算法和金鑰長度使用自訂 IPsec/IKE 原則，而不是 Azure 預設原則集。

### <a name="point-to-site-vpn"></a>點對站 VPN

點對站 VPN 可讓個別用戶端電腦存取 Azure 虛擬網路。 [安全通訊端通道通訊協定](https://technet.microsoft.com/library/2007.06.cableguy.aspx) (SSTP) 會用來建立 VPN 通道，而且可以周遊防火牆 (通道會顯示為 HTTPS 連線)。 您可以使用自己的內部 PKI 根 CA 進行點對站連線。

您可以使用 Azure 入口網站，透過憑證驗證或 PowerShell 來設定虛擬網路的點對站 VPN 連線。

若要深入了解 Azure VNet 的點對站 VPN 連線，請參閱：[使用憑證驗證設定 VNet 的點對站連線：Azure 入口網站](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)和

[使用憑證驗證設定 VNet 的點對站連線：PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpn"></a>站對站 VPN 

站對站 VPN 閘道連線可用來透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道，將內部部署網路連線到 Azure 虛擬網路。 此類型的連線需要位於內部部署的 VPN 裝置，且您已對該裝置指派對外開放的公用 IP 位址。

您可以使用 Azure 入口網站、PowerShell 或 Azure 命令列介面 (CLI)，來設定虛擬網路的站對站 VPN 連線。

如需詳細資訊，請閱讀：

[在 Azure 入口網站中建立站對站連線](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[建立站對站連線](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[使用 CLI 建立具有站對站 VPN 連線的虛擬網路](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-azure-data-lake"></a>Azure Data Lake 的傳輸中加密

傳輸中的資料 (也稱為移動中的資料) 也一律會在 Data Lake Store 中加密。 除了在儲存至持續性媒體之前加密資料，也一律會使用 HTTPS 保護傳輸中的資料。 HTTPS 是 Data Lake Store REST 介面支援的唯一通訊協定。

若要深入了解 Azure Data Lake 的傳輸中資料加密，請參閱標題為 [Azure Data Lake Store 中的資料加密](../data-lake-store/data-lake-store-encryption.md)的文件

## <a name="key-management-with-azure-key-vault"></a>Azure Key Vault 的金鑰管理

若未適當保護及管理金鑰，加密會變成沒有用。 Azure Key Vault 是 Microsoft 建議的解決方案，可用來管理和控制雲端服務所使用之加密金鑰的存取權。 存取金鑰的權限可透過 Azure Active Directory 帳戶指派給服務或使用者。

Azure Key Vault 讓組織不需要設定、修補及維護硬體安全模組 (HSM) 和金鑰管理軟體。 透過 Azure Key Vault，Microsoft 永遠不會看到您的金鑰，而且應用程式無法對其直接存取，讓您維持掌控。 您也可以在 HSM 中匯入或產生金鑰。

## <a name="next-steps"></a>後續步驟

- [Azure 安全性概觀](security-get-started-overview.md)
- [Azure 網路安全性概觀](security-network-overview.md)
- [Azure 資料庫安全性概觀](azure-database-security-overview.md)
- [Azure 虛擬機器安全性概觀](security-virtual-machines-overview.md)
- [待用資料加密](azure-security-encryption-atrest.md)
- [資料安全性與加密的最佳做法](azure-security-data-encryption-best-practices.md)
