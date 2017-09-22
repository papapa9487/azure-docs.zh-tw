---
title: "在 Azure 中使用加密功能來保護傳輸中的個人資料 | Microsoft Docs"
description: "在 Azure 中使用加密功能來保護個人資料"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 8c59fa7e9315922af4fab5f8492194b50459ef8b
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>Azure 加密技術：使用加密功能來保護傳輸中的個人資料

本文可協助您了解及使用 Azure 加密技術來保護傳輸中的資料。 

保護個人資料於網路周遊時的隱私權，是多層式深度防禦安全性策略中不可或缺的一部分。 傳輸中的加密功能是為了讓攔截傳輸的攻擊者無法檢視或使用資料。

## <a name="scenario"></a>案例

總部設於美國的大型郵輪公司將擴展其營運，以提供地中海、亞得里亞海、波羅的海和不列顛群島的行程。 為了支援這些工作，它收購了以義大利、德國、丹麥和英國為據點的數個較小型郵輪公司。 

此公司使用 Microsoft Azure 在雲端儲存公司資料。 其中包含其全球客戶群的名稱、地址、電話號碼和信用卡資訊等個人識別資訊。 它也會包含傳統人力資源資訊，例如地址、電話號碼、稅務識別碼，以及有關各地公司員工的其他資訊。 此郵輪公司也會維護獎勵和忠誠度方案會員的大型資料庫，其中包含的個人資訊可追蹤與目前和過去客戶的關係。

客戶的個人資料是從公司的遠端辦公室以及從世界各地的旅遊代理商輸入於資料庫中。 包含客戶資訊的文件會透過網路傳輸至 Azure 儲存體。

## <a name="problem-statement"></a>問題陳述

當客戶和員工的個人資料往返傳輸至 Azure 服務時，公司必須保護其隱私權。

## <a name="company-goal"></a>公司目標

公司目標在於確保個人資料在離開磁碟時已加密。 如有未經授權的人員攔截離開磁碟的個人資料，則必須採用讓資料變得無法讀取的格式。 對於使用者和系統管理員而言，套用加密應該很容易或完全透明。

## <a name="solutions"></a>解決方案

Azure 服務提供多種工具和技術，協助您保護傳輸中的個人資料。

### <a name="azure-storage"></a>Azure 儲存體

儲存在雲端的資料必須從用戶端 (可實際位於世界任意處) 傳送至 Azure 資料中心。 當使用者擷取資料時，資料會以相反的方向再次傳送。 透過公用網際網路傳輸的資料一律有遭到攻擊者攔截的風險。 請務必使用傳輸層級加密來保護個人資料的隱私權，以保護其在位置之間移動時的安全。

HTTPS 通訊協定會透過網際網路提供安全且加密的通訊通道。 HTTPS 應該用來存取 Azure 儲存體中的物件，以及用於呼叫 REST API。 使用[共用存取簽章](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) (SAS) 時，您可強制使用 HTTPS 通訊協定，以委派 Azure 儲存體物件的存取權。 SAS 類型有兩種：服務 SAS 和帳戶 SAS。

#### <a name="how-do-i-construct-a-service-sas"></a>如何建構服務 SAS？

服務 SAS 只會將存取權限委派給一種儲存體服務資源 (Blob、佇列、資料表或檔案服務)。 若要建構服務 SAS，請執行下列作業：

1. 指定已簽署的版本欄位

2. 指定已簽署的資源 (Blob 和僅限檔案服務)

3. 指定查詢參數來覆寫回應標頭 (Blob 服務和僅限檔案服務)

4. 指定資料表名稱 (僅限資料表服務)

5. 指定存取原則

6. 指定簽章有效性間隔

8. 指定權限

9. 指定 IP 位址或 IP 範圍

10. 指定 HTTP 通訊協定

11. 指定資料表存取範圍

12. 指定已簽署的識別碼

13. 指定簽章

如需詳細指示，請參閱[建構服務 SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN)。

#### <a name="how-do-i-construct-an-account-sas"></a>如何建構帳戶 SAS？

帳戶 SAS 會將存取權委派給一或多個儲存體服務中的資源。 您也可以將 Blob 容器、資料表、佇列和檔案共用的讀取、寫入和刪除作業的存取權限，委派給本無權限的服務 SAS。 帳戶 SAS 與服務 SAS 的建構方式相似。 如需詳細指示，請參閱[建構服務 SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN)。

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>呼叫 REST API 時如何強制使用 HTTPS？

若要在呼叫 REST API 來存取儲存體帳戶中的物件時強制使用 HTTPS，您可以對儲存體帳戶啟用「需要安全傳輸」。 

1. 在 Azure 入口網站中，選取 [建立儲存體帳戶]，或針對現有的儲存體帳戶，依序選取 [設定] 和 [組態]。

2. 在 [需要安全傳輸] 下，選取 [已啟用]。

![建立儲存體帳戶](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

如需詳細指示，包括如何以程式設計方式啟用「需要安全傳輸」，請參閱[需要安全傳輸](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer)。

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>如何在 Azure 檔案儲存體中加密資料？

若要使用 [Azure 檔案儲存體](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal)加密傳輸中的資料，您可以使用 SMB 3.x 搭配 Windows 8、8.1 和 10 以及搭配 Windows Server 2012 R2 和 Windows Server 2016。 當您在啟用 [需要安全傳輸] 的情況下使用 Azure 檔案服務時，任何未加密的連線都會失敗。 這包括使用 SMB 2.1、無加密的 SMB 3.0 和某些 Linux SMB 用戶端類型的情況。

#### <a name="azure-client-side-encryption"></a>Azure 用戶端加密

當個人資料在用戶端應用程式與儲存體之間傳輸時，另一個用於保護該資料的選項是[用戶端加密](https://docs.microsoft.com/azure/storage/storage-client-side-encryption)。 資料會在傳輸至 Azure 儲存體之前進行加密，而當您從 Azure 儲存體擷取資料後，資料會在用戶端上接收之後進行解密。

### <a name="azure-site-to-site-vpn"></a>Azure 站對站 VPN

保護在公司網路或使用者與 Azure 虛擬網路之間傳輸的個人資料的有效方式，就是使用[站對站](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)或[點對站](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)虛擬私人網路 (VPN)。 VPN 連線會透過網際網路建立安全的加密通道。

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>如何建立站對站 VPN 連線？

站對站 VPN 可將公司網路上的多個使用者連線至 Azure。 若要在 Azure 入口網站中建立站對站連線，請執行下列作業：

1. 建立虛擬網路。

2. 指定 DNS 伺服器。

3. 建立閘道子網路。

4. 建立 VPN 閘道。 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. 建立區域網路閘道。

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. 設定 VPN 裝置。

7. 建立 VPN 連線。

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. 驗證 VPN 連線。

如需有關如何在 Azure 入口網站中建立站對站連線的詳細指示，請參閱 [在 Azure 入口網站中建立站對站連線] (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)。

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>如何設定點對站 VPN 連線？

點對站 VPN 可建立從個別用戶端電腦到虛擬網路的安全連線。 當您想要從遠端位置 (例如從住家、旅館或會議中心) 連線到 Azure 時，這很實用。 若要在 Azure 入口網站中建立點對站連線：

1. 建立虛擬網路。

2. 新增閘道子網路。

3. 指定 DNS 伺服器。 (選用)

4. 建立虛擬網路閘道。

5. 產生憑證。

6. 新增用戶端位址集區。

7. 上傳根憑證公開憑證資料。

8. 產生和安裝 VPN 用戶端組態套件。

9. 安裝匯出的用戶端憑證。

10. 連接到 Azure。

11. 確認您的連線。

如需詳細指示，請參閱[使用憑證驗證設定 VNet 的點對站連線：Azure 入口網站](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)。

### <a name="ssltls"></a>SSL/TLS

Microsoft 建議您一律使用 SSL/TLS 通訊協定來交換不同位置的資料。 選擇使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) 透過專用高速 WAN 連結移動大型資料集的組織，也可以使用 SSL/TLS 或其他通訊協定在應用程式層級加密資料，以提供額外的保護。

### <a name="encryption-by-default"></a>預設加密

Microsoft 會使用加密功能來保護在客戶與 Azure 雲端服務之間傳輸的資料。 如果您透過 Azure 入口網站與 Azure 儲存體互動，則所有交易都會透過 HTTPS 發生。

[傳輸層安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) 是 Microsoft 資料中心將嘗試與連線到 Microsoft 雲端服務的用戶端系統進行交涉的通訊協定。 TLS 提供增強式驗證、訊息隱私權、完整性 (可偵測訊息竄改、攔截和偽造)、互通性、演算法彈性，並方便部署和使用。

此外，也會運用 [完整轉寄密碼](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS)，以便客戶的用戶端系統與 Microsoft 的雲端服務之間的每個連線使用唯一金鑰。 Microsoft 雲端服務的連線也充分利用以 RSA 為基礎的 2,048 位元加密金鑰長度。 TLS、RSA 2,048 位元金鑰長度和 PFS 的組合，可讓他人更難以攔截及存取在 Microsoft 雲端服務與客戶之間傳輸的資料。

傳輸中的資料一律會在 [Data Lake Store] 中加密 (https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview)。 除了在儲存至持續性媒體之前加密資料，也一律會使用 HTTPS 保護傳輸中的資料。 HTTPS 是 Data Lake Store REST 介面支援的唯一通訊協定。

## <a name="summary"></a>摘要

強制使用 Azure 儲存體的 HTTPS 連線、使用共用存取簽章，以及在儲存體帳戶上啟用「需要安全傳輸」，公司即可完成其保護個人資料及這類資料隱私權的目標。 他們也可藉由使用 SMB 3.0 連線並實作用戶端加密來保護個人資料。 從公司網路至 Azure 虛擬網路的站對站 VPN 連線以及來自個別使用者的點對站 VPN 連線將會建立安全通道，透過它即可安全地傳輸個人資料。 Microsoft 的預設加密作法會進一步保護個人資料的隱私權。

## <a name="next-steps"></a>後續步驟

- [Azure 資料安全性和加密最佳做法](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [規劃與設計 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [VPN 閘道常見問題集](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [購買及設定您的 Azure App Service 的 SSL 憑證](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)

