---
title: "Azure Data Factory 中的安全性考量 | Microsoft Docs"
description: "說明 Azure Data Factory 中資料移動服務用來保護您資料的基本安全性基礎結構。"
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2017
ms.author: abnarain
ms.openlocfilehash: 0514710606b74db7c13f04e961347d215d2d283d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory - 資料移動的安全性考量
本文說明 Azure Data Factory 中資料移動服務用來保護您資料的基本安全性基礎結構。 Azure Data Factory 管理資源建置在 Azure 安全性基礎結構上，並使用 Azure 提供的所有可能安全性措施。

在 Data Factory 方案中，您可以建立一或多個資料 [管線](concepts-pipelines-activities.md)。 管線是一起執行某個工作的活動所組成的邏輯群組。 這些管線位於建立 Data Factory 的區域中。 

雖然 Data Factory 只有在**美國西部**和**美國東部 2**區域才有提供 (版本 2 預覽)，但[全球數個區域](concepts-integration-runtime.md#azure-ir)都有提供資料移動服務。 如果資料移動服務尚未部署到該區域，Data Factory 服務可確保資料不會離開某個地區/區域，除非您明確指示服務使用替代區域。 

Azure Data Factory 本身除了用於雲端資料存放區的已連結服務認證 (會使用憑證加密) 之外，並不會儲存任何資料。 它可讓您建立資料導向工作流程，藉由使用其他區域或內部部署環境中的[計算服務](compute-linked-services.md)，協調[所支援資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)之間的資料移動和資料處理。 它也能讓您使用 SDK 和 Azure 監視器來監視和管理工作流程。

使用 Azure Data Factory 進行的資料移動已通過下列各項規範的「認證」：
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

如果您對 Azure 法規遵循以及 Azure 如何保護其專屬基礎結構感興趣，請瀏覽 [Microsoft 信任中心](https://www.microsoft.com/TrustCenter/default.aspx)。 

在本文中，我們會檢閱下列兩個資料移動案例中的安全性考量︰ 

- **雲端案例** - 在此案例中，可透過網際網路公開存取您的來源和目的地。 這些包括受管理的雲端儲存體服務 (例如「Azure 儲存體」、「Azure SQL 資料倉儲」、Azure SQL Database、Azure Data Lake Store、Amazon S3、Amazon Redshift)、SaaS 服務 (例如 Salesforce)，以及 Web 通訊協定 (例如 FTP 和 OData)。 如需完整的支援資料來源清單，請參閱[這裡](copy-activity-overview.md#supported-data-stores-and-formats)。
- **混合式案例**- 在此案例中，您的來源或目的地是在防火牆之後或在內部部署的公司網路內，或是資料存放區位於私人網路/虛擬網路中 (最常見的是來源) 而不可公開存取。 裝載在虛擬機器上的資料庫伺服器也屬於此案例的涵蓋範圍。

## <a name="cloud-scenarios"></a>雲端案例
###<a name="securing-data-store-credentials"></a>保護資料存放區認證
- 在 Azure Data Factory 受管理存放區中儲存加密的認證。

   Azure Data Factory 可透過使用「受 Microsoft 管理的憑證」來「加密」資料存放區認證，為這些認證提供保護。 這些憑證每隔「兩年」會輪替一次 (包括憑證更新和憑證移轉)。 這些已加密的認證會安全地儲存在「受 Azure Data Factory 管理服務管理的 Azure 儲存體」中。 如需有關「Azure 儲存體」安全性的詳細資訊，請參閱 [Azure 儲存體安全性概觀](../security/security-storage-overview.md)。
- 在 Azure Key Vault 中儲存認證 

   您現在可以選擇在 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 中儲存資料存放區的認證，然後讓 Azure Data Factory 在活動執行期間擷取它。 如需詳細資訊，請參閱 [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)。

   > [!NOTE]
   > 目前，只有[動態連接器](connector-dynamics-crm-office-365.md)支援這項功能。 

### <a name="data-encryption-in-transit"></a>傳輸中資料加密
如果雲端資料存放區支援 HTTPS 或 TLS，則 Data Factory 中資料移動服務與雲端資料存放區之間的所有資料傳輸，都會透過安全通道 HTTPS 或 TLS。

> [!NOTE]
> 所有連到 **Azure SQL Database** 和「Azure SQL 資料倉儲」的連線在資料透過傳輸進出資料庫時，一律都需要加密 (SSL/TLS)。 使用 JSON 編輯器來編寫管線時，在 [連接字串] 中新增 **encryption** 屬性，並將它設定為 **true**。 針對「Azure 儲存體」，您可以在連接字串中使用 **HTTPS**。

### <a name="data-encryption-at-rest"></a>待用資料加密
有些資料存放區支援待用資料加密。 建議您為這些資料存放區啟用資料加密機制。 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL 資料倉儲
「Azure SQL 資料倉儲」中的「透明資料加密」(TDE) 可以對待用資料執行即時加密和解密，協助防止惡意活動的威脅。 用戶端並不會察覺到這個過程。 如需詳細資訊，請參閱[保護 SQL 資料倉儲中的資料庫](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)。

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database 也支援透明資料加密 (TDE)，TDE 可在不需變更應用程式的情況下，對資料執行即時加密和解密，協助防止惡意活動的威脅。 用戶端並不會察覺到這個過程。 若要深入了解，請參閱 [Azure SQL Database 的透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)。 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store 也針對儲存在帳戶中的資料提供加密功能。 啟用加密功能時，Data Lake Store 會在保存資料之前先自動加密資料，並在擷取資料之前先解密資料，因此存取該資料的用戶端並不會察覺這個過程。 如需詳細資訊，請參閱 [Azure Data Lake Store 安全性](../data-lake-store/data-lake-store-security-overview.md)。 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob 儲存體和 Azure 資料表儲存體
「Azure Blob 儲存體」和「Azure 資料表儲存體」支援「儲存體服務加密」(SSE)，此功能會在將資料保存到儲存體之前先自動加密資料，並在擷取資料之前先解密資料。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)。

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 同時支援用戶端和伺服器的待用資料加密。 如需詳細資訊，請參閱[使用加密來保護資料 (英文)](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)。 目前，Data Factory 並不支援虛擬私人雲端 (VPC) 內的 Amazon S3。

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift 支援叢集待用資料加密。 如需詳細資訊，請參閱 [Amazon Redshift 資料庫加密 (英文)](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)。 目前，Data Factory 並不支援 VPC 內的 Amazon Redshift。 

#### <a name="salesforce"></a>Salesforce
Salesforce 支援「Shield 平台加密」，可加密所有檔案、附件、自訂欄位。 如需詳細資訊，請參閱[了解 Web 伺服器 OAuth 驗證流程 (英文)](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)。  

## <a name="hybrid-scenarios-using-self-hosted-integration-runtime"></a>混合式情節 (使用自我裝載整合執行階段)
混合式情節需要您在內部部署網路中或是虛擬網路 (Azure) 或虛擬私人雲端 (Amazon) 內安裝自我裝載整合執行階段。 自我裝載整合執行階段必須能夠存取本機資料存放區。 如需自我裝載整合執行階段的詳細資訊，請參閱[自我裝載整合執行階段](create-self-hosted-integration-runtime.md)。 

![自我裝載整合執行階段通道](media/data-movement-security-considerations/data-management-gateway-channels.png)

**命令通道**可允許 Data Factory 中的資料移動服務與自我裝載整合執行階段之間進行通訊。 此通訊包含活動的相關資訊。 資料通道會用來在內部部署資料存放區與雲端資料存放區之間傳輸資料。    

### <a name="on-premises-data-store-credentials"></a>內部部署資料存放區認證
您的內部部署資料存放區的認證一律會經過加密並儲存。 它可以儲存在自我裝載整合執行階段電腦本機，或儲存於 Azure Data Factory 受管理存放裝置 (如同雲端存放區認證)。 

1. 您可以選擇**在本機儲存認證**。 如果您想要加密，並將認證儲存在自我裝載整合執行階段本機，請依照[在自我裝載整合執行階段上加密認證](encrypt-credentials-self-hosted-integration-runtime.md)的步驟。 所有連接器皆支援此選項。 自我裝載整合執行階段會使用 Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) 加密機密資料/認證資訊。 

   使用 **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** Cmdlet 來加密連結服務認證/加密連結服務中的機密詳細資料。 然後，您可以使用傳回的 JSON (與 **connectionString** 中的 **EncryptedCredential** 元素)，搭配 **Set-AzureRmDataFactoryV2LinkedSevrice** Cmdlet 來建立連結服務。  

2. 如果未使用以上步驟所述的 **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** Cmdlet，而是改為直接搭配使用 **Set-AzureRmDataFactoryV2LinkedSevrice** Cmdlet 與 JSON 中的連接字串/認證，則連結服務將**加密並儲存在 Azure Data Factory 受管理儲存體**。 機密資訊仍由憑證加密，這些憑證由 Microsoft 管理。



#### <a name="ports-used-during-encrypting-linked-service-on-self-hosted-integration-runtime"></a>在自我裝載整合執行階段上加密連結服務時所使用的連接埠
根據預設，PowerShell 會在含自我裝載整合執行階段的電腦上，使用連接埠 **8050** 進行安全通訊。 您可以視需要變更此連接埠。  

![閘道的 HTTPS 連接埠](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>傳輸中加密
與 Azure 服務進行通訊時，所有資料傳輸都會透過安全通道 **HTTPS** 和 **TLS over TCP**，以防止攔截式攻擊。

您也可以使用 [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) 或 [ExpressRoute](../expressroute/expressroute-introduction.md) 來進一步保護內部部署網路與 Azure 之間的通訊通道。

虛擬網路是您網路在雲端的邏輯呈現方式。 您可以透過設定 IPSec VPN (站台對站台) 或 ExpressRoute (私用對等)，將內部部署網路連接到 Azure 虛擬網路 (VNet)     

下表根據混合式資料移動的不同來源和目的地位置組合，摘要說明網路和自我裝載整合執行階段組態的建議事項。

| 來源      | 目的地                              | 網路組態                    | 整合執行階段設定                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| 內部部署 | 部署在虛擬網路中的虛擬機器和雲端服務 | IPSec VPN (點對站或站台對站台) | 自我裝載整合執行階段可以安裝在內部部署環境或 VNet 中的 Azure 虛擬機器 (VM) 上 |
| 內部部署 | 部署在虛擬網路中的虛擬機器和雲端服務 | ExpressRoute (私用對等)           | 自我裝載整合執行階段可以安裝在內部部署環境或 VNet 中的 Azure VM 上 |
| 內部部署 | 具有公用端點的 Azure 型服務 | ExpressRoute (公用對等)            | 自我裝載整合執行階段必須安裝在內部部署中 |

下列各圖說明在使用自我裝載整合執行階段的情況下，利用 ExpressRoute 和 IPSec VPN (搭配「虛擬網路」)，在內部部署資料庫與 Azure 服務之間移動資料：

**ExpressRoute：**

![使用 ExpressRoute 搭配閘道](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN：**

![IPSec VPN 搭配閘道](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-self-hosted-integration-runtime"></a>防火牆組態及將自我裝載整合執行階段的 IP 位址加入白名單

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>內部部署/私人網路的防火牆需求  
在企業中，「公司防火牆」會在組織的中央路由器上執行。 而 **Windows 防火牆**則是在安裝自我裝載整合執行階段的本機電腦上以精靈的形式執行。 

下表提供「公司防火牆」的「輸出連接埠」和網域需求。

| 網域名稱                  | 輸出連接埠 | 說明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443、80        | 必須提供此資訊，自我裝載整合執行階段才能連線到 Data Factory 中的資料移動服務 |
| `*.core.windows.net`          | 443            | 當您使用[分段複製](copy-activity-performance.md#staged-copy)功能時，可供自我裝載整合執行階段用來連線到「Azure 儲存體帳戶」。 |
| `*.frontend.clouddatahub.net` | 443            | 必須提供此資訊，自我裝載整合執行階段才能連線到 Azure Data Factory 服務。 |
| `*.database.windows.net`      | 1433           | (選擇性) 當您的目的地是 Azure SQL Database/「Azure SQL 資料倉儲」時，需要提供此資訊。 若要在不開啟連接埠 1433 的情況下，將資料複製到 Azure SQL Database/「Azure SQL 資料倉儲」，請使用分段複製功能。 |
| `*.azuredatalakestore.net`    | 443            | (選擇性) 當您的目的地是 Azure Data Lake Store 時，需要提供此資訊。 |

> [!NOTE] 
> 您可能需要依個別資料來源所需，在公司防火牆層級管理連接埠/將網域加入白名單。 此表格僅使用 Azure SQL Database、「Azure SQL 資料倉儲」、Azure Data Lake Store 作為範例。   

下表提供 **Windows 防火牆**的**輸入連接埠**需求。

| 輸入連接埠 | 說明                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | [在自我裝載整合執行階段上加密認證](encrypt-credentials-self-hosted-integration-runtime.md)說明的 PowerShell 加密 Cmdlet/認證管理員應用程式所需，以便為自我裝載整合執行階段的內部部署資料存放區安全地設定認證。 |

![閘道連接埠需求](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurationswhitelisting-in-data-store"></a>資料存放區中的 IP 組態/白名單設定
有些雲端資料存放區也會要求必須將存取它們的電腦之 IP 位址加入白名單。 請確定在防火牆中已將自我裝載整合執行階段電腦的 IP 位址正確地加入白名單並進行設定。

下列雲端資料存放區會要求必須將自我裝載整合執行階段電腦的 IP 位址加入白名單。 在這些資料存放區中，有些可能預設不會要求將 IP 位址加入白名單。 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL 資料倉儲](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md#create-a-server-level-firewall-rule-in-the-azure-portal)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>常見問題集

**問：**是否可以跨不同的 Data Factory 共用自我裝載整合執行階段？
**答：**我們尚未支援這項功能。 我們正積極處理這個問題。

**問：**自我裝載整合執行階段需要什麼連接埠才能運作？
**答：**自我裝載整合執行階段會建立 HTTP 型連線來開啟網際網路。 必須開啟**輸出連接埠 443 和 80**，自我裝載整合執行階段才能建立此連線。 針對「認證管理員」應用程式，請只在電腦層級 (而非公司防火牆層級) 開啟**輸入連接埠 8050**。 如果使用 Azure SQL Database 或「Azure SQL 資料倉儲」作為來源/目的地，則也需要開啟 **1433** 連接埠。 如需詳細資訊，請參閱[防火牆組態及將 IP 位址加入白名單](#firewall-configurations-and-whitelisting-ip-address-of gateway)一節。 


## <a name="next-steps"></a>後續步驟
如需有關複製活動效能的資訊，請參閱[複製活動的效能及微調指南](copy-activity-performance.md)。

 
