---
title: "Azure Stack 中的 DNS | Microsoft Docs"
description: "Azure Stack 中的 DNS"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: ac93b5eb4228cef373428b7b69932d5993d54fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="dns-in-azure-stack"></a>Azure Stack 中的 DNS

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

Azure Stack 包括下列 DNS 功能：
* 支援 DNS 主機名稱解析
* 使用 API 建立和管理 DNS 區域和記錄

## <a name="support-for-dns-hostname-resolution"></a>支援 DNS 主機名稱解析
您可以指定公用 IP 資源的 DNS 網域名稱標籤，以建立 domainnamelabel.location 與 Azure Stack 受管理 DNS 伺服器中公用 IP 位址的對應。  

例如，如果您建立公用 IP 資源並以 **contoso** 作為本機 Azure Stack 位置的網域名稱標籤，則完整網域名稱 (FQDN) **contoso.local.cloudapp.azurestack.external** 會解析為資源的公用 IP 位址。 您可以使用此 FQDN 來建立自訂網域 CNAME 記錄，其指向 Azure Stack 中的公用 IP 位址。

> [!IMPORTANT]
> 每個所建立的網域名稱標籤，皆必須具有唯一的 Azure Stack 位置。

如果您使用入口網站建立公用 IP 位址，會看起來像這樣：

![建立公用 IP 位址](media/azure-stack-whats-new-dns/image01.png)

如果您想要用負載平衡資源與公用 IP 位址建立關聯，此設定非常實用。 例如，您將可能有負載平衡器處理來自 Web 應用程式的要求。 在負載平衡器的背後，是位於一或多個虛擬機器上的網站。 現在您可以透過 DNS 名稱來存取負載平衡網站，而不必透過 IP 位址。

## <a name="create-and-manage-dns-zones-and-records-using-api"></a>使用 API 建立和管理 DNS 區域和記錄
您可以建立和管理 DNS 區域和 Azure Stack 中的記錄。  

Azure Stack 提供就像 Azure 的 DNS 服務，使用與 Azure DNS API 一致的 API。  只要將您的網域裝載於 Azure Stack DNS 中，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具、計費方式和支援來管理 DNS 記錄。 

很明顯地，Azure Stack DNS 的基礎結構會比 Azure 更為精簡。 因此，範圍、級別和效能會取決於 Azure Stack 部署和其部署環境的級別。  所以，像是效能、可用性、全域發佈和高可用性 (HA)，每個部署都不盡相同。

## <a name="comparison-with-azure-dns"></a>與 Azure DNS 比較
Azure Stack 中的 DNS 與 Azure 中的 DNS 類似，僅有兩個主要例外狀況：
* **不支援 AAAA 記錄**

    Azure Stack「不」支援 AAAA 記錄，因為 Azure Stack 並不支援 IPv6 位址。  這是 Azure 和 Azure Stack DNS 之間的主要差異。
* **不是多租用戶**

    有別於 Azure，在 Azure Stack 中的 DNS 服務不是多租用戶。 因此每個租用戶無法建立相同的 DNS 區域。 僅有首個訂用帳戶嘗試建立區域會成功，後續要求皆會失敗。  這是已知問題，也是 Azure 和 Azure Stack DNS 之間的主要差異。 此問題將在未來版本中解決。

此外，Azure Stack DNS 如何實作標記、中繼資料、Etag 和限制皆有細微差異。

下列資訊針對適用於 Azure Stack 的 DNS，並會與 Azure DNS 稍有不同。 若要深入了解 Azure DNS，請在 Microsoft Azure 文件網站中參閱《[DNS 區域和及記錄](../../dns/dns-zones-records.md)》。

### <a name="tags-metadata-and-etags"></a>標記、中繼資料和 Etag

**標記**

Azure Stack DNS 支援在 DNS 區域資源上，使用 Azure Resource Manager 標記。 不支援在 DNS 記錄集上使用標記，不過，支援在 DNS 記錄集上使用「中繼資料」作為替代，接下來會詳述。

**Metadata**

Azure Stack DNS 支援使用「中繼資料」來替代記錄集標記，用以標註記錄集。 類似於標記，中繼資料可讓您建立名稱-值組與每個記錄集之間的關聯。 例如，這可在用以記錄每個記錄集目的上非常實用。 與標記不同的是，中繼資料無法用來提供 Azure 帳單篩選過的檢視，也無法在 Azure Resource Manager 原則中指定。

**Etag**

假設有兩個人或兩個處理序同時嘗試修改 DNS 記錄。 何者獲勝？ 獲勝者知道他已覆寫另一人所做的變更嗎？

Azure Stack DNS 使用 Etag 以安全地處理相同資源的並行變更。 Etag 和 Azure Resource Manager「標籤」分開。 每個 DNS 資源 (區域或記錄集) 都有一個相關聯的 Etag。 每當擷取資源時，也會擷取其 Etag。 更新資源時，您可以選擇傳回 Etag，讓 Azure Stack DNS 可以確認伺服器上的 Etag 相符。 因為每次更新資源都會重新產生 Etag，Etag 不符就表示發生並行變更。 建立新的資源時也可以使用 Etag，以確保該資源尚不存在。

根據預設，Azure Stack DNS PowerShell 會使用 Etag 來禁止對區域和記錄集進行並行變更。 選擇性的 *-Overwrite* 參數可以用來停用 Etag 檢查，在此情況下，會覆寫任何已發生的並行變更。

在 Azure Stack DNS REST API 層級上，將會使用 HTTP 標頭指定 Etag。 下表提供它們的行為：

| 頁首 | 行為|
|--------|---------|
| None   | PUT 一定成功 (沒有 Etag 檢查)|
| If-match| 唯有當資源存在且 Etag 符合時，PUT 才會成功|
| If-match *| 唯有當資源存在時，PUT 才會成功|
| If-none-match *| 唯有當資源不存在時，PUT 才會成功|

### <a name="limits"></a>限制

使用 Azure Stack DNS 時，會適用下列的預設限制︰

| 資源| 預設限制|
|---------|--------------|
| 每一訂用帳戶的區域| 100|
| 每一區域的記錄集| 5000|
| 每一記錄集的記錄| 20|

## <a name="next-steps"></a>後續步驟
[適用於 Azure Stack 的 iDNS 簡介](azure-stack-understanding-dns.md)
