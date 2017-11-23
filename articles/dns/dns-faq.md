---
title: "Azure DNS 常見問題集 | Microsoft Docs"
description: "關於 Azure DNS 的常見問題集"
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: a8cff450730d05970398989ceb3e8585aefd6771
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2017
---
# <a name="azure-dns-faq"></a>Azure DNS 常見問題集

## <a name="about-azure-dns"></a>關於 Azure DNS

### <a name="what-is-azure-dns"></a>什麼是 Azure DNS？

網域名稱系統 (DNS) 負責將網站或服務名稱轉譯 (或解析) 為其 IP 位址。 Azure DNS 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。

Azure DNS 中的 DNS 網域裝載於 Azure 的 DNS 名稱伺服器全球網路。 我們使用「任一傳播」網路，所以每個 DNS 查詢是由最接近的可用 DNS 伺服器回答。 這為您的網域提供快速的效能與高可用性。

Azure DNS 服務是架構在 Azure Resource Manager 上。 因此可得益於 Resource Manager 的功能，如角色型存取控制、稽核記錄檔、資源鎖定。 您可以透過 Azure 入口網站、Azure PowerShell Cmdlet 和跨平台 Azure CLI 來管理網域和記錄。 需要自動化 DNS 管理的應用程式可以透過 REST API 和 SDK 與服務進行整合。

### <a name="how-much-does-azure-dns-cost"></a>Azure DNS 的費用是多少？

Azure DNS 計費模型所根據的是 Azure DNS 中裝載的 DNS 區域數量，以及它們所接收的 DNS 查詢數量。 根據使用量會提供折扣。

如需詳細資訊，請參閱 [Azure DNS 定價頁面](https://azure.microsoft.com/pricing/details/dns/)。

### <a name="what-is-the-sla-for-azure-dns"></a>Azure DNS 有怎樣的 SLA？

我們保證有效的 DNS 要求至少有 99.99% 的時間都至少會從一部 Azure DNS 名稱伺服器收到回應。

如需詳細資訊，請參閱 [Azure DNS SLA 頁面](https://azure.microsoft.com/support/legal/sla/dns)。

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>什麼是「DNS 區域」？ 它與 DNS 網域相同嗎？ 

網域是網域名稱系統中的唯一名稱，例如 'contoso.com'。

DNS 區域用來裝載特定網域的 DNS 記錄。 例如，網域 'contoso.com' 可能包含數筆 DNS 記錄，例如 'mail.contoso.com' (用於郵件伺服器) 和 'www.contoso.com' (用於網站)。 這些會裝載在 'contoso.com' DNS 區域中。

網域名稱「只是一個名稱」，而 DNS 區域則是包含網域名稱之 DNS 記錄的資料資源。 Azure DNS 可讓您裝載 DNS 區域，並在 Azure 中管理網域的 DNS 記錄。 它也提供 DNS 名稱伺服器以回應來自網際網路的 DNS 查詢。

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>我是否必須先購買 DNS 網域名稱才能使用 Azure DNS？ 

不一定。

您無須購買網域，就能將 DNS 區域裝載在 Azure DNS 中。 您無須擁有網域名稱，即可隨時建立 DNS 區域。 此區域的 DNS 查詢只有在導向到指派給此區域的 Azure DNS 名稱伺服器時，才會進行解析。

如果您想要將您的 DNS 區域連結到全球 DNS 階層，讓來自世界各地的 DNS 查詢都能找到您的 DNS 區域，並以您的 DNS 記錄回應這些查詢，您就必須購買網域名稱。

## <a name="azure-dns-features"></a>Azure DNS 功能

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS 是否支援 DNS 型流量路由或端點容錯移轉？

DNS 型流量路由和端點容錯移轉是由「Azure 流量管理員」所提供。 這是一項可以與 Azure DNS 一起使用的個別 Azure 服務。 如需詳細資訊，請參閱[流量管理員概觀](../traffic-manager/traffic-manager-overview.md)。

Azure DNS 僅支援裝載「靜態」DNS 網域，其中所指定 DNS 記錄的每個 DNS 查詢一律會收到相同的 DNS 回應。

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS 是否支援網域名稱註冊？

否。 Azure DNS 目前不支援購買網域名稱。 若想要購買網域，必須洽詢第三方網域名稱註冊機構。 註冊機構通常會收取些微年費。 然後便可以在 Azure DNS 裝載這些網域來管理 DNS 記錄。 如需詳細資訊，請參閱 [將網域委派給 Azure DNS](dns-domain-delegation.md) 。

這是我們的待處理項目上所追蹤的一項功能。 您可以使用我們的意見反應網站來[表達您對這項功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)。

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS 是否支援「私人」網域？
「私人」網域的支援是使用私人 DNS 區域進行實作。  這項功能目前可供作為預覽使用。  私人 DNS 區域是使用與網際網路面向 Azure DNS 區域相同的工具使用進行管理，但只能從您指定的虛擬網路內將它們進行解析。  如需詳細資訊，請參閱[概觀](private-dns-overview.md)。

如需有關 Azure 中其他內部 DNS 選項的資訊，請參閱 [VM 與角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS 是否支援 DNSSEC？

否。 Azure DNS 目前不支援 DNSSEC。

這是我們的待處理項目上所追蹤的一項功能。 您可以使用我們的意見反應網站來[表達您對這項功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)。

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS 是否支援區域傳輸 (AXFR/IXFR)？

否。 Azure DNS 目前不支援區域傳輸。 您可以[使用 Azure CLI 將 DNS 區域匯入到 Azure DNS 中](dns-import-export.md)。 接著便可以透過 [Azure DNS 管理入口網站](dns-operations-recordsets-portal.md)、我們的 [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell Cmdlet](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)，來管理 DNS 記錄。

這是我們的待處理項目上所追蹤的一項功能。 您可以使用我們的意見反應網站來[表達您對這項功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)。

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS 是否支援 URL 重新導向？

否。 URL 重新導向服務實際上並不是 DNS 服務，它們是在 HTTP 層級運作，而不是在 DNS 層級。 有些 DNS 提供者會將 URL 重新導向服務結合成其整體方案的一部分。 Azure DNS 目前不支援此做法。

這項功能列入我們的待處理項目追蹤。 您可以使用我們的意見反應網站來[表達您對這項功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)。

## <a name="using-azure-dns"></a>使用 Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>我是否可以使用 Azure DNS 和另一個 DNS 提供者來共同裝載某個網域？

是。 Azure DNS 支援與其他 DNS 服務共同裝載網域。

若要這麼做，您必須將網域的 NS 記錄 (這可控制哪些提供者會收到該網域的 DNS 查詢) 修改成指向該兩個提供者的名稱伺服器。 您必須在 3 個地方修改這些 NS 記錄：在 Azure DNS 中、在另一個提供者中，以及在父區域中 (通常是透過網域名稱註冊機構設定)。 如需有關 DNS 委派的詳細資訊，請參閱 [DNS 網域委派](dns-domain-delegation.md)。

此外，您必須確保網域的 DNS 記錄在這兩個 DNS 提供者之間保持同步。 Azure DNS 目前不支援 DNS 區域傳輸。 您必須使用 [Azure DNS 管理入口網站](dns-operations-recordsets-portal.md)、我們的 [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell Cmdlet](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)，來同步處理 DNS 記錄。

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>我是否必須將我的網域委派給全部 4 個 Azure DNS 名稱伺服器？

是。 Azure DNS 會為每個 DNS 區域指派 4 個名稱伺服器，以隔離錯誤和提升恢復能力。 為了符合 Azure DNS SLA 的要求，您必須將您的網域委派給這所有 4 個名稱伺服器。

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Azure DNS 在使用量上有什麼限制？

使用 Azure DNS 時，會適用下列的預設限制︰

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>我是否可以在資源群組之間或訂用帳戶之間移動 Azure DNS 區域？

是。 您可以在資源群組之間或訂用帳戶之間移動 DNS 區域。

移動 DNS 區域時，對 DNS 查詢並沒有影響。 指派給區域的名稱伺服器會維持不變，系統會將 DNS 查詢當作一般輸送量來處理。

如需有關如何移動 DNS 區域的詳細資訊和指示，請參閱[將資源移動到新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md)。

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>DNS 變更需要多長的時間才會生效？

新的 DNS 區域和 DNS 記錄通常幾秒內就會在 Azure DNS 名稱伺服器上快速反映出來。

對現有 DNS 記錄的變更可能需要較長的時間，但仍應該會在 60 秒內於 Azure DNS 名稱伺服器上反映出來。 在此情況下，Azure DNS 外的 DNS 快取 (由 DNS 用戶端和 DNS 遞迴解析程式所執行) 也可能影響讓 DNS 變更生效所需的時間。 您可以使用每一個記錄集的「存留時間」(TTL) 屬性來控制這個快取持續時間。

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>我要如何保護我的 DNS 區域免於遭到意外刪除？

Azure DNS 的管理是透過 Azure Resource Manager，因此享有 Azure Resource Manager 所提供的存取控制功能優點。 角色型存取控制可用來控制哪些使用者具備 DNS 區域和記錄集的讀取或寫入存取權。 您可以套用資源鎖定來防止意外修改或刪除 DNS 區域和記錄集。

如需詳細資訊，請參閱[保護 DNS 區域和記錄](dns-protect-zones-recordsets.md)。

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>我要如何設定 Azure DNS 中的 SPF 記錄？

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6-"></a>Azure DNS Nameservers 需透過 IPv6 解析嗎？ 

是。 Azure DNS Nameservers 是雙重堆疊 (具有 IPv4 和 IPv6 位址)。 若要尋找指派至您 DNS 區域的 Azure DNS nameservers IPv6 位址，您可以使用 nslookup 之類的工具 (例如 `nslookup -q=aaaa <Azure DNS Nameserver>`)。

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>我要如何設定 Azure DNS 中的「國際網域名稱」(IDN)？

「國際網域名稱」(IDN) 的運作方式是使用 '[Punycode](https://en.wikipedia.org/wiki/Punycode)' 將每個 DNS 名稱編碼。 建立 DNS 查詢時，會使用這些以 Punycode 編碼的名稱來建立。

您可以透過先將區域名稱或記錄集名稱轉換成 Punycode，來設定 Azure DNS 中的「國際網域名稱」(IDN)。 Azure DNS 目前不支援以 Punycode 作為轉換目標或來源的內建轉換。

## <a name="next-steps"></a>後續步驟

[深入了解 Azure DNS](dns-overview.md)
<br>
[深入了解使用私人網域的 Azure DNS](private-dns-overview.md)
<br>
[深入了解 DNS 區域和記錄](dns-zones-records.md)
<br>
[開始使用 Azure DNS](dns-getstarted-portal.md)

