---
title: "在 Azure DNS 中託管反向 DNS 對應區域 | Microsoft Docs"
description: "了解如何使用 Azure DNS 為您的 IP 範圍託管反向 DNS 對應區域"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 3e10b25d2f9b91c96af2958fef6dc6a4fdbff301
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="hosting-reverse-dns-lookup-zones-in-azure-dns"></a>在 Azure DNS 中託管反向 DNS 對應區域

本文說明如何在 Azure DNS 中為指派的 IP 範圍託管反向 DNS 對應區域。 由反向對應區域代表的 IP 範圍必須指派給您的組織，通常由您的 ISP 指派。

若要為指派給 Azure 服務之 Azure 擁有的 IP 位址設定反向 DNS，請參閱[為配置給 Azure 服務的 IP 位址設定反向對應](dns-reverse-dns-for-azure-services.md)。

在閱讀本文之前，您應該先熟悉這篇 [Azure 反向 DNS 和支援概觀](dns-reverse-dns-overview.md)。

本文會逐步引導您使用 Azure 入口網站、Azure PowerShell、Azure CLI 1.0 或 Azure CLI 2.0，建立第一個反向對應 DNS 區域和記錄。

## <a name="create-a-reverse-lookup-dns-zone"></a>建立反向對應 DNS 區域

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 在 [中樞] 功能表上，按一下 [新增] > [網路] > 然後按一下 [DNS 區域] 開啟 [建立 DNS 區域] 刀鋒視窗。

   ![DNS 區域](./media/dns-reverse-dns-hosting/figure1.png)

1. 在 [建立 DNS 區域] 刀鋒視窗中，命名 DNS 區域。 區域名稱針對 IPv4 和 IPv6 前置詞的製作方式不同。 請遵循 [IPv4](#ipv4) 或 [IPv6](#ipv6) 的指示來命名區域。 完成後按一下 [建立] 以建立區域。

### <a name="ipv4"></a>IPv4

IPv4 反向對應區域的名稱是以其所代表的 IP 範圍為根據。 格式應如下：`<IPv4 network prefix in reverse order>.in-addr.arpa`。 如需範例，請參閱 [Azure 反向 DNS 和支援概觀](dns-reverse-dns-overview.md#ipv4)。

> [!NOTE]
> 在 Azure DNS 中建立無類別反向 DNS 對應區域時，區域名稱中必須使用連字號 (`-`) 而不是正斜線 ('/')。
>
> 例如，IP 範圍 192.0.2.128/26 必須使用 `128-26.2.0.192.in-addr.arpa` 作為區域名稱，而不是 `128/26.2.0.192.in-addr.arpa`。
>
> 這是因為，雖然 DNS 標準兩者都支援，但 Azure DNS 不支援包含正斜線 (`/`) 字元的 DNS 區域名稱。

下例示範如何透過 Azure 入口網站，在 Azure DNS 中建立名為 `2.0.192.in-addr.arpa` 的「類別 C」反向 DNS 區域：

 ![建立 DNS 區域](./media/dns-reverse-dns-hosting/figure2.png)

「資源群組位置」定義資源群組的位置，對 DNS 區域沒有任何影響。 DNS 區域一定是「全域」位置，並不會顯示出來。

下列範例示範如何使用 Azure PowerShell 和 Azure CLI 完成這項工作：

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

IPv6 反向對應區域的名稱格式應該如下：`<IPv6 network prefix in reverse order>.ip6.arpa`。  如需範例，請參閱 [Azure 反向 DNS 和支援概觀](dns-reverse-dns-overview.md#ipv6)。


下例示範如何透過 Azure 入口網站，在 Azure DNS 中建立名為 `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` 的 IPv6 反向 DNS 對應區域：

 ![建立 DNS 區域](./media/dns-reverse-dns-hosting/figure3.png)

「資源群組位置」定義資源群組的位置，對 DNS 區域沒有任何影響。 DNS 區域一定是「全域」位置，並不會顯示出來。

下列範例示範如何使用 Azure PowerShell 和 Azure CLI 完成這項工作：

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azurecli-10"></a>AzureCLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>委派反向 DNS 對應區域

建立反向 DNS 對應區域之後，您必須確定該區域是從父區域委派。 DNS 委派可讓 DNS 名稱解析程序尋找託管 DNS 反向 DNS 對應區域的名稱伺服器。 這可讓這些名稱伺服器接聽您位址範圍中的 IP 位址 DNS 反向查詢。

至於正向對應區域，委派 DNS 區域的程序請參閱[將您的網域委派給 Azure DNS](dns-delegate-domain-azure-dns.md)。 反向對應區域的委派運作方式相同。 唯一的差別是您需要以提供 IP 範圍的 ISP 設定名稱伺服器，而不是您的網域名稱註冊機構。

## <a name="create-a-dns-ptr-record"></a>建立 DNS PTR 記錄

### <a name="ipv4"></a>IPv4

下例會逐步解說在 Azure DNS 的反向 DNS 區域中建立 PTR 記錄的程序。 關於其他記錄類型和修改現有的記錄，請參閱[使用 Azure 入口網站管理 DNS 記錄和記錄集](dns-operations-recordsets-portal.md)。

1.  在 [DNS 區域] 刀鋒視窗頂端，選取 [+ 記錄集] 以開啟 [新增記錄集] 刀鋒視窗。

 ![DNS 區域](./media/dns-reverse-dns-hosting/figure4.png)

1. 在 [新增記錄集] 刀鋒視窗上。 
1. 從記錄的 [類型] 功能表中選取 [PTR]。  
1. PTR 記錄的記錄集名稱必須是反向順序的其餘 IPv4 位址。 在本例中，已填入前三個八位元當作區域名稱的一部分 (.2.0.192)。 因此，[名稱] 欄位只要再填入最後一個八位元。 例如，您可以將 IP 位址是 192.0.2.15 的資源記錄集命名為 "**15**"。  
1. 在 [網域名稱] 欄位中，使用 IP 輸入資源的完整網域名稱 (FQDN)。
1. 選取刀鋒視窗底部的 [確定]，建立 DNS 記錄。

 ![新增記錄集](./media/dns-reverse-dns-hosting/figure5.png)

下列範例示範如何使用 PowerShell 和 Azure CLI 完成這項工作：

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azurecli-10"></a>AzureCLI 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

下例會逐步引導您完成建立新 'PTR' 記錄的程序。 關於其他記錄類型和修改現有的記錄，請參閱[使用 Azure 入口網站管理 DNS 記錄和記錄集](dns-operations-recordsets-portal.md)。

1. 在 [DNS 區域] 刀鋒視窗頂端，選取 [+ 記錄集] 以開啟 [新增記錄集] 刀鋒視窗。

  ![DNS 區域刀鋒視窗](./media/dns-reverse-dns-hosting/figure6.png)

2. 在 [新增記錄集] 刀鋒視窗上。 
3. 從記錄的 [類型] 功能表中選取 [PTR]。  
4. PTR 記錄的記錄集名稱必須是反向順序的其餘 IPv6 位址。 它絕不能包含任何零壓縮。 在本例中，已填入 IPv6 的第一組 64 位元當作區域名稱的一部分 (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa)。 因此，[名稱] 欄位只要再填入最後一組 64 位元。 以反向順序輸入 IP 位址的最後一組 64 位元，每個十六進位數字之間使用句點當作分隔符號。 例如，您可以將 IP 位址是 2001:0db8:abdc:0000:f524:10bc:1af9:405e 的資源記錄集命名為 "**e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f**"。  
5. 在 [網域名稱] 欄位中，使用 IP 輸入資源的完整網域名稱 (FQDN)。
6. 選取刀鋒視窗底部的 [確定]，建立 DNS 記錄。

![新增記錄集刀鋒視窗](./media/dns-reverse-dns-hosting/figure7.png)

下列範例示範如何使用 PowerShell 和 Azure CLI 完成這項工作：

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azurecli-10"></a>AzureCLI 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>檢視記錄

若要檢視您所建立的記錄，請巡覽至您在 Azure 入口網站的 DNS 區域。 在 [DNS 區域] 刀鋒視窗的下半部，您可以看到 DNS 區域的記錄。 您應該會看到預設 NS 和 SOA 記錄 (在每個區域中建立)，還有您已建立的任何新記錄。

### <a name="ipv4"></a>IPv4

[DNS 區域] 刀鋒視窗，顯示 IPv4 PTR 記錄：

![DNS 區域刀鋒視窗](./media/dns-reverse-dns-hosting/figure8.png)

下列範例示範如何使用 PowerShell 或 Azure CLI 檢視 PTR 記錄：

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

[DNS 區域] 刀鋒視窗，顯示 IPv6 PTR 記錄：

![DNS 區域刀鋒視窗](./media/dns-reverse-dns-hosting/figure9.png)

下列範例示範如何使用 PowerShell 和 Azure CLI 檢視記錄：

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>常見問題集

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>我可以在 Azure DNS 上，為 ISP 指派的 IP 區塊託管反向 DNS 對應區域嗎？

是。 完全支援在 Azure DNS 中為您自己的 IP 範圍託管反向對應 (ARPA) 區域。

依本文所述，在 Azure DNS 中建立反向對應區域，然後使用您的 ISP [委派區域](dns-domain-delegation.md)。  接著，您就可以使用與其他記錄類型相同的方式來管理每個反向對應的 PTR 記錄。

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>託管反向 DNS 對應區域的費用如何？

在 Azure DNS 中為 ISP 指派的 IP 區塊託管反向 DNS 對應區域，會依[標準 Azure DNS 費率](https://azure.microsoft.com/pricing/details/dns/)計價。

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>我可以同時在 Azure DNS 中為 IPv4 和 IPv6 位址託管反向 DNS 對應區域嗎？

是。 本文說明如何在 Azure DNS 中同時建立 IPv4 和 IPv6 反向 DNS 對應區域。

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>可以匯入現有的反向 DNS 對應區域嗎？

是。 您可以使用 Azure CLI 將現有的 DNS 區域匯入到 Azure DNS。 正向對應區域和反向對應區域都適用。

如需詳細資訊，請參閱[使用 Azure CLI 匯入及匯出 DNS 區域檔案](dns-import-export.md)。

## <a name="next-steps"></a>後續步驟

如需反向 DNS 的詳細資訊，請參閱維基百科的 [reverse DNS lookup](http://en.wikipedia.org/wiki/Reverse_DNS_lookup) (反向 DNS 對應)。
<br>
了解如何[管理 Azure 服務的反向 DNS 記錄](dns-reverse-dns-for-azure-services.md)。
