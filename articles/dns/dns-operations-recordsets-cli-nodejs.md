---
title: "使用 Azure CLI 1.0 管理 Azure DNS 中的 DNS 記錄 | Microsoft Docs"
description: "將網域裝載於 Azure DNS 時，在 Azure DNS 管理 DNS 記錄集和記錄。 對記錄集和記錄執行作業的所有 CLI 1.0 命令。"
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2016
ms.author: jonatul
ms.openlocfilehash: 76473349b2c6121d8f289a86b46c10a8b8697b34
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="manage-dns-records-in-azure-dns-using-the-azure-cli-10"></a>使用 Azure CLI 1.0 管理 Azure DNS 中的 DNS 記錄

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

本文適用於 Windows、Mac 和 Linux，將會說明如何使用跨平台 Azure 命令列介面 (CLI) 管理 DNS 區域的 DNS 記錄。 您也可以使用 [Azure PowerShell](dns-operations-recordsets.md) 或 [Azure 入口網站](dns-operations-recordsets-portal.md)來管理 DNS 記錄。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本

您可以使用下列其中一個 CLI 版本來完成工作︰

* [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md) - 適用於傳統和資源管理部署模型的 CLI。
* [Azure CLI 2.0](dns-operations-recordsets-cli.md) - 適用於資源管理部署模型的新一代 CLI。

此文章中的範例假設您已[安裝 Azure CLI 1.0、登入，並建立 DNS 區域](dns-operations-dnszones-cli-nodejs.md)。

## <a name="introduction"></a>簡介

在 Azure DNS 中建立 DNS 記錄前，您需要先了解 Azure DNS 如何將 DNS 記錄組織成 DNS 記錄集。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

如需在 Azure DNS 的 DNS 記錄的詳細資訊，請參閱 [DNS 區域與記錄](dns-zones-records.md)。

## <a name="create-a-dns-record"></a>建立 DNS 記錄

若要建立 DNS 記錄，請使用 `azure network dns record-set add-record` 命令。 如需協助，請參閱 `azure network dns record-set add-record -h`。

建立記錄時，您必須指定資源群組名稱、區域名稱、記錄集名稱、記錄類型，以及所建立記錄的詳細資料。 提供的記錄集名稱必須是「相對」名稱，表示它不能包含區域名稱。

如果記錄集不存在，此命令會為您建立。 如果記錄集已經存在，此命令會將您指定的記錄新增至現有的記錄集。

如果建立新的記錄集，則會使用預設存留時間 (TTL) 3600。 如需如何使用不同 TTL 的指示，請參閱[建立 DNS 記錄集](#create-a-dns-record-set)。

下列範例會在 MyResourceGroup 資源群組的 contoso.com 區域中建立稱為 www 的 A 記錄。 A 記錄的 IP 位址是 1.2.3.4。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

若要在區域頂點 (在此案例中為 "contoso.com") 建立記錄，請使用記錄名稱 "@" (包括引號)：

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>建立 DNS 記錄集

在上述範例中，DNS 記錄不是新增至現有記錄集，就是記錄集是以*隱含方式*建立。 您也可以先*明確地*建立記錄集，再於其中新增記錄。 Azure DNS 支援「空白」記錄集，其可做為預留位置，以在建立 DNS 記錄之前保留 DNS 名稱。 空白記錄集可在 Azure DNS 控制面板中看到，但不會出現在 Azure DNS 名稱伺服器上。

請使用 `azure network dns record-set create` 命令建立記錄集。 如需協助，請參閱 `azure network dns record-set create -h`。

明確地建立記錄集可讓您指定記錄集屬性，例如[存留時間 (TTL)](dns-zones-records.md#time-to-live) 和中繼資料。 [記錄集中繼資料](dns-zones-records.md#tags-and-metadata)可用來將應用程式特定資料與每一個資料集產生關聯 (以索引鍵值組的形式)。

下列範例使用 `--ttl` 參數 (簡短形式 `-l`) 建立 60 秒 TTL 的空白記錄集：

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --ttl 60
```

下列範例使用 `--metadata` 參數 (簡短形式 `-m`) 建立具有兩個中繼資料項目 ("dept=finance" 和 "environment=production") 的記錄集：

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

建立好空白記錄集之後，可依[建立 DNS 記錄](#create-a-dns-record)所述使用 `azure network dns record-set add-record` 新增記錄。

## <a name="create-records-of-other-types"></a>建立其他類型的記錄

參閱如何建立 'A' 記錄的詳細資訊後，下列範例會示範如何建立 Azure DNS 所支援其他記錄類型的記錄。

用來指定記錄資料的參數，根據記錄的類型而所有不同。 例如，對於類型 "A" 的記錄，您可使用參數 `-a <IPv4 address>` 指定 IPv4 位址。 每個記錄類型的參數可以使用 `azure network dns record-set add-record -h` 列出。

在每個案例中，我們會說明如何建立單一記錄。 記錄會新增至現有記錄集，或者會以隱含方式建立記錄集。 如需明確建立記錄集和定義記錄集參數的詳細資訊，請參閱[建立 DNS 記錄集](#create-a-dns-record-set)。

我們沒有提供 SOA 記錄集的建立範例，因為已與每一個 DNS 區域完成 SOA 建立與刪除，且無法個別建立或刪除 SOA。 然而，[可以對 SOA 進行修改，如稍後範例所示](#to-modify-an-SOA-record)。

### <a name="create-an-aaaa-record"></a>建立 AAAA 記錄

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-aaaa AAAA --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-a-caa-record"></a>建立 CAA 記錄

下列範例示範如何建立 CAA 記錄。 

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com ca1 CAA --caaflags 0 --caatag issue --caavalue "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>建立 CNAME 記錄

> [!NOTE]
> DNS 標準在區域頂點不允許 CNAME 記錄 (`-Name "@"`)，也不允許包含一個記錄以上的記錄集。
> 
> 如需詳細資訊，請參閱 [CNAME 記錄](dns-zones-records.md#cname-records)。

```azurecli
azure network dns record-set add-record  MyResourceGroup contoso.com  test-cname CNAME --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>建立 MX 記錄

此範例會使用記錄集名稱 "@"，在區域頂點 (在此案例中，"contoso.com") 建立 MX 記錄。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "@" MX --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>建立 NS 記錄

```azurecli
azure network dns record-set add-record MyResourceGroup  contoso.com  test-ns NS --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>建立 PTR 記錄

在此情況下，'my-arpa-zone.com' 代表表示您 IP 範圍的 ARPA 區域。 此區域中的每個 PTR 記錄集都與此 IP 範圍內的一個 IP 位址相對應。  記錄名稱 '10' 是此記錄所代表的這個 IP 範圍內 IP 位址的最後一個八位元。

```azurecli
azure network dns record-set add-record MyResourceGroup my-arpa-zone.com "10" PTR --ptrdname "myservice.contoso.com"
```

### <a name="create-an-srv-record"></a>建立 SRV 記錄

建立 [SRV 記錄集](dns-zones-records.md#srv-records)時，指定記錄集名稱中的 *\_服務* 和 *\_通訊協定*。 在區域頂點建立一筆 SRV 記錄集時，不需要在記錄集名稱中包含 "@"。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "_sip._tls" SRV --priority 10 --weight 5 --port 8080 --target "sip.contoso.com"
```

### <a name="create-a-txt-record"></a>建立 TXT 記錄

下列範例示範如何建立 TXT 記錄。 如需 TXT 記錄中，所支援字串長度上限的相關資訊，請參閱 [TXT 記錄](dns-zones-records.md#txt-records)。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-txt TXT --text "This is a TXT record"
```

## <a name="get-a-record-set"></a>取得記錄集

若要擷取現有的記錄集，使用 `azure network dns record-set show`。 如需協助，請參閱 `azure network dns record-set show -h`。

和建立記錄或記錄集時相同，提供的記錄集名稱必須是「相對」名稱，表示它不能包含區域名稱。 您也必須指定記錄類型、包含記錄集的區域，以及包含區域的資源群組。

下列範例會從 MyResourceGroup 資源群組的 contoso.com 區域中，擷取類型為 A 的 www 記錄：

```azurecli
azure network dns record-set show MyResourceGroup contoso.com www A
```

## <a name="list-record-sets"></a>列出記錄集

您可以使用 `azure network dns record-set list` 命令來列出 DNS 區域中的所有記錄。 如需協助，請參閱 `azure network dns record-set list -h`。

這個範例會傳回資源群組 MyResourceGroup 之區域 contoso.com 中的所有記錄集，不論其名稱或記錄類型為何︰

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```

此範例會傳回符合指定記錄類型 (此案例中為 'A' 記錄) 的所有記錄集：

```azurecli
azure network dns record-set list MyResourceGroup contoso.com --type A
```

## <a name="add-a-record-to-an-existing-record-set"></a>將記錄新增至現有的記錄集

您可以使用 `azure network dns record-set add-record` 在新的記錄集內建立記錄，或用它將記錄新增至現有記錄集。

如需詳細資訊，請參閱上面的[建立 DNS 記錄](#create-a-dns-record)和[建立其他類型的記錄](#create-records-of-other-types)。

## <a name="remove-a-record-from-an-existing-record-set"></a>從現有的記錄集移除記錄。

若要從現有記錄集內移除 DNS 記錄，請使用 `azure network dns record-set delete-record`。 如需協助，請參閱 `azure network dns record-set delete-record -h`。

此命令會刪除記錄集內的 DNS 記錄。 如果記錄集內的最後一個記錄遭到刪除，記錄集本身**不會**遭到刪除。 反而會留下空白記錄集。 若要改為刪除記錄集，請參閱[刪除記錄集](#delete-a-record-set)。

您必須使用和使用 `azure network dns record-set add-record` 建立記錄時相同的參數，指定要刪除的記錄和應從哪個區域中刪除。 這些參數在上面的[建立 DNS 記錄](#create-a-dns-record)和[建立其他類型的記錄](#create-records-of-other-types)中有所說明。

此命令會提示您確認。 使用 `--quiet` 參數 (簡短形式 `-q`) 可以抑制此提示。

下列範例會在 MyResourceGroup 資源群組的 contoso.com 區域中，刪除名為 www 之記錄集內值為 '1.2.3.4' 的 A 記錄。 確認提示已抑制。

```azurecli
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4 --quiet
```

## <a name="modify-an-existing-record-set"></a>修改現有記錄集

每個記錄集都包含[存留時間 (TTL)](dns-zones-records.md#time-to-live)、[中繼資料](dns-zones-records.md#tags-and-metadata)和 DNS 記錄。 下列各節說明如何修改每個屬性。

### <a name="to-modify-an-a-aaaa-mx-ns-ptr-srv-or-txt-record"></a>修改 A、AAAA、MX、NS、PTR、SRV 或 TXT 記錄

若要修改類型為 A、AAAA、MX、NS、PTR、SRV 或 TXT 的現有記錄，您應該先新增記錄，再刪除現有記錄。 如需如何刪除和新增記錄的詳細指示，請參閱本文稍早的章節。

下列範例說明如何修改 'A' 記錄，從 IP 位址 1.2.3.4 到 IP 位址 5.6.7.8：

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 5.6.7.8
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

### <a name="to-modify-a-cname-record"></a>修改 CNAME 記錄

若要修改 CNAME 記錄，請使用 `azure network dns record-set add-record` 來新增記錄值。 不同於其他記錄類型，CNAME 記錄集只能包含單一記錄。 因此，當您新增記錄時，現有記錄就會遭到「取代」，並不需要另外刪除。  系統會提示您接受這項取代。

此範例會修改 MyResourceGroup 資源群組之 contoso.com 區域中的 CNAME 記錄集 www，使其指向 'www.fabrikam.net' 而非其現有值︰

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www CNAME --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>修改 SOA 記錄

使用 `azure network dns record-set set-soa-record` 修改指定 DNS 區域的 SOA。 如需協助，請參閱 `azure network dns record-set set-soa-record -h`。

下列範例說明如何設定資源群組 MyResourceGroup 之區域 contoso.com 的 SOA 記錄 'email' 屬性：

```azurecli
azure network dns record-set set-soa-record rg1 contoso.com --email admin.contoso.com
```


### <a name="to-modify-ns-records-at-the-zone-apex"></a>在區域頂點修改 NS 記錄

系統會自動使用每個 DNS 區域在區域頂點建立 NS 記錄集。 此記錄集包含指派給區域的 Azure DNS 名稱伺服器的名稱。

您可以將其他名稱伺服器新增至此 NS 記錄集，以支援使用多個 DNS 提供者的共同裝載網域。 您也可以修改此記錄集的 TTL 和中繼資料。 不過，您無法移除或修改預先填入的 Azure DNS 名稱伺服器。

請注意，這只適用於區域頂點的 NS 記錄集。 區域中的其他 NS 記錄集 (如用於委派子區域) 可以修改，沒有任何限制。

下列範例顯示如何將其他的名稱伺服器新增至在區域頂點的 NS 記錄集：

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>修改現有記錄集的 TTL

若要修改現有記錄集的 TTL，請使用 `azure network dns record-set set`。 如需協助，請參閱 `azure network dns record-set set -h`。

下列範例說明如何修改記錄集 TTL，在此案例中是修改為 60 秒︰

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --ttl 60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>修改現有記錄集的中繼資料

[記錄集中繼資料](dns-zones-records.md#tags-and-metadata)可用來將應用程式特定資料與每一個資料集產生關聯 (以索引鍵值組的形式)。 若要修改現有記錄集的中繼資料，請使用 `azure network dns record-set set`。 如需協助，請參閱 `azure network dns record-set set -h`。

下列範例說明如何使用 `--metadata` 參數 (簡短形式 `-m`) 修改具有兩個中繼資料項目 ("dept=finance" 和 "environment=production") 的記錄集。 請注意，任何現有的中繼資料都會由給定值所「取代」。

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

## <a name="delete-a-record-set"></a>刪除記錄集

您可以使用 `azure network dns record-set delete` 命令來刪除記錄集。 如需協助，請參閱 `azure network dns record-set delete -h`。 刪除記錄集時，也會刪除記錄集內的所有記錄。

> [!NOTE]
> 您無法在區域頂點 (`-Name "@"`) 刪除 SOA 和 NS 記錄集。  這些項目在區域建立時即會自動建立，且當區域刪除時則會自動刪除。

下列範例會從 MyResourceGroup 資源群組的 contoso.com 區域中，刪除類型為 A 的 www 記錄集：

```azurecli
azure network dns record-set delete MyResourceGroup contoso.com www A
```

系統會提示您確認刪除作業。 若要抑制此提示，請使用 `--quiet` 參數 (簡短形式 `-q`)。

## <a name="next-steps"></a>後續步驟

深入了解[ Azure DNS 中的區域和記錄](dns-zones-records.md)。
<br>
了解使用 Azure DNS 時，如何[保護區域和記錄](dns-protect-zones-recordsets.md)。
