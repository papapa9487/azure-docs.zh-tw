---
title: "將網域委派給 Azure DNS |Microsoft Docs"
description: "了解如何變更網域委派及使用 Azure DNS 名稱伺服器提供網域主機代管。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: gwallace
ms.openlocfilehash: 33b3ec24432ff1268860b9a2e9d5098600a8dedc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="delegate-a-domain-to-azure-dns"></a>將網域委派給 Azure DNS

Azure DNS 可讓您裝載 DNS 區域，並在 Azure 中管理網域的 DNS 記錄。 網域必須從父系網域委派給 Azure DNS，該網域的 DNS 查詢才能送達 Azure DNS。 請記住，Azure DNS 不是網域註冊機構。 本文說明如何將您的網域委派給 Azure DNS。

如果是從註冊機構購買網域，註冊機構會提供選項來設定這些 NS 記錄。 您不必擁有網域，也能在 Azure DNS 中以該網域名稱建立 DNS 區域。 不過，您必須擁有網域，才能在註冊機構中設定委派給 Azure DNS。

例如，假設您購買網域 'contoso.net'，並在 Azure DNS 中建立名稱為 'contoso.net' 的區域。 身為網域的擁有者，註冊機構會提供選項，讓您設定網域的名稱伺服器位址 (亦即 NS 記錄)。 註冊機構會將這些 NS 記錄儲存在父系網域中，在此範例中為 '.net'。 然後，當世界各地的用戶端嘗試解析 'contoso.net' 中的 DNS 記錄時，系統會將他們導向至您在 Azure DNS 區域中的網域。

## <a name="create-a-dns-zone"></a>建立 DNS 區域

1. 登入 Azure 入口網站
1. 在 [中樞] 功能表上，按一下 [新增] > [網路] > [DNS 區域] 以開啟 [建立 DNS 區域] 刀鋒視窗。

    ![DNS 區域](./media/dns-domain-delegation/dns.png)

1. 在 [建立 DNS 區域] 刀鋒視窗中輸入下列的值，然後按一下 [建立]：

   | **設定** | **值** | **詳細資料** |
   |---|---|---|
   |**名稱**|contoso.net|DNS 區域的名稱|
   |**訂用帳戶**|[您的訂用帳戶]|選取要在其中建立應用程式閘道的訂用帳戶。|
   |**資源群組**|**建立新的︰**contosoRG|建立資源群組。 資源群組名稱在您選取的訂用帳戶中必須是唯一的。 若要深入了解資源群組，請閱讀 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) 概觀。|
   |**位置**|美國西部||

> [!NOTE]
> 資源群組是指資源群組的位置，不會對 DNS 區域有任何影響。 DNS 區域一定是「全域」位置，並不會顯示出來。

## <a name="retrieve-name-servers"></a>擷取名稱伺服器

在委派 DNS 區域給 Azure DNS 之前，您必須先知道區域的名稱伺服器名稱。 每次建立區域時，Azure DNS 都會配置某個集區中的名稱伺服器。

1. 建立 DNS 區域之後，在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 在 [所有資源] 刀鋒視窗中，按一下 [contoso.net] DNS 區域。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選...] 方塊中輸入 **contoso.net**， 輕鬆地存取應用程式閘道。 

1. 從 DNS 區域刀鋒視窗中擷取名稱伺服器。 在此範例引，區域 'contoso.net' 已被指派名稱伺服器 'ns1-01.azure-dns.com'、'ns2-01.azure-dns.net'、'ns3-01.azure-dns.org' 和 'ns4-01.azure-dns.info'：

 ![Dns-nameserver](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS 會自動在包含指派的名稱伺服器的區域中，建立權威 NS 記錄。  您只需要擷取這些記錄，就能透過 Azure PowerShell 或 Azure CLI 查看名稱伺服器的名稱。

下列範例也會提供使用 PowerShell 和 Azure CLI 擷取 Azure DNS 中區域之名稱伺服器的步驟。

### <a name="powershell"></a>PowerShell

```powershell
# The record name "@" is used to refer to records at the top of the zone.
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

以下是回應範例。

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : contosorg
Ttl               : 172800
Etag              : 03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5
RecordType        : NS
Records           : {ns1-07.azure-dns.com., ns2-07.azure-dns.net., ns3-07.azure-dns.org.,
                    ns4-07.azure-dns.info.}
Metadata          :
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set show --resource-group contosoRG --zone-name contoso.net --type NS --name @
```

以下是回應範例。

```json
{
  "etag": "03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosoRG/providers/Microsoft.Network/dnszones/contoso.net/NS/@",
  "metadata": null,
  "name": "@",
  "nsRecords": [
    {
      "nsdname": "ns1-07.azure-dns.com."
    },
    {
      "nsdname": "ns2-07.azure-dns.net."
    },
    {
      "nsdname": "ns3-07.azure-dns.org."
    },
    {
      "nsdname": "ns4-07.azure-dns.info."
    }
  ],
  "resourceGroup": "contosoRG",
  "ttl": 172800,
  "type": "Microsoft.Network/dnszones/NS"
}
```

## <a name="delegate-the-domain"></a>委派網域

現已建立 DNS 區域且您擁有名稱伺服器，必須使用 Azure DNS 名稱伺服器來更新父系網域。 每個註冊機構都有自己的 DNS 管理工具，可變更網域的名稱伺服器記錄。 在註冊機構的 DNS 管理頁面中，請編輯 NS 記錄，並將 NS 記錄取代為 Azure DNS 建立的記錄。

委派網域給 Azure DNS 時，您必須使用 Azure DNS 提供的名稱伺服器名稱。 不論您的網域名稱為何，建議將名稱伺服器的 4 個名稱全部用上。 網域委派不需要名稱伺服器名稱，即可使用相同的最上層網域作為您的網域。

您不應該使用「黏附記錄」指向 Azure DNS 名稱伺服器 IP 位址，因為這些 IP 位址日後可能變更。 Azure DNS 目前不支援使用您區域中名稱伺服器名稱的委派 (有時稱為「虛名名稱伺服器」)。

## <a name="verify-name-resolution-is-working"></a>確認名稱解析正常運作

完成委派之後，您可以使用 'nslookup' 之類的工具來查詢您區域的 SOA 記錄 (這也是在建立區域時自動建立)，以確認名稱解析正常運作。

您不必指定 Azure DNS 名稱伺服器，如果已正確設定委派，正常的 DNS 解析程序會自動尋找名稱伺服器。

```
nslookup -type=SOA contoso.com
```

以下是上述命令的回應範例︰

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.com
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="delegate-sub-domains-in-azure-dns"></a>在 Azure DNS 中委派子網域

如果您想要設定個別的子區域，您可以在 Azure DNS 中委派子網域。 例如，假設您想要設定個別的子區域 'partners.contoso.net'，請在 Azure DNS 中設定及委派 'contoso.net'。

1. 在 Azure DNS 中建立子區域 'partners.contoso.net'。
2. 查閱子區域中的權威 NS 記錄，來取得在 Azure DNS 中裝載子區域的名稱伺服器。
3. 在指向子區域的上層區域中設定 NS 記錄，以委派子區域。

### <a name="create-a-dns-zone"></a>建立 DNS 區域

1. 登入 Azure 入口網站
1. 在 [中樞] 功能表上，按一下 [新增] > [網路] > [DNS 區域] 以開啟 [建立 DNS 區域] 刀鋒視窗。

    ![DNS 區域](./media/dns-domain-delegation/dns.png)

1. 在 [建立 DNS 區域] 刀鋒視窗中輸入下列的值，然後按一下 [建立]：

   | **設定** | **值** | **詳細資料** |
   |---|---|---|
   |**名稱**|partners.contoso.net|DNS 區域的名稱|
   |**訂用帳戶**|[您的訂用帳戶]|選取要在其中建立應用程式閘道的訂用帳戶。|
   |**資源群組**|**使用現有的︰**contosoRG|建立資源群組。 資源群組名稱在您選取的訂用帳戶中必須是唯一的。 若要深入了解資源群組，請閱讀 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) 概觀。|
   |**位置**|美國西部||

> [!NOTE]
> 資源群組是指資源群組的位置，不會對 DNS 區域有任何影響。 DNS 區域一定是「全域」位置，並不會顯示出來。

### <a name="retrieve-name-servers"></a>擷取名稱伺服器

1. 建立 DNS 區域之後，在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 在 [所有資源] 刀鋒視窗中，按一下 [partners.contoso.net] DNS 區域。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選...] 方塊中輸入 **partners.contoso.net**， 輕鬆地存取 DNS 區域。

1. 從 DNS 區域刀鋒視窗中擷取名稱伺服器。 在此範例引，區域 'contoso.net' 已被指派名稱伺服器 'ns1-01.azure-dns.com'、'ns2-01.azure-dns.net'、'ns3-01.azure-dns.org' 和 'ns4-01.azure-dns.info'：

 ![Dns-nameserver](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS 會自動在包含指派的名稱伺服器的區域中，建立權威 NS 記錄。  您只需要擷取這些記錄，就能透過 Azure PowerShell 或 Azure CLI 查看名稱伺服器的名稱。

### <a name="create-name-server-record-in-parent-zone"></a>在父系區域中建立名稱伺服器記錄

1. 瀏覽至 Azure 入口網站中的 **contoso.net** DNS 區域。
1. 按一下 [+ 記錄集]
1. 在 [新增記錄集] 刀鋒視窗上，輸入下列值，然後按一下 [確定]：

   | **設定** | **值** | **詳細資料** |
   |---|---|---|
   |**名稱**|合作夥伴|子 DNS 區域的名稱|
   |**類型**|NS|使用 NS 作為名稱伺服器記錄。|
   |**TTL**|1|存留時間。|
   |**TTL 單位**|小時|將存留時間單位設定為小時|
   |**名稱伺服器**|{partners.contoso.net 區域中的名稱伺服器}|輸入 partners.contoso.net 區域中的 4 個名稱伺服器。 |

   ![Dns-nameserver](./media/dns-domain-delegation/partnerzone.png)


### <a name="delegating-sub-domains-in-azure-dns-with-other-tools"></a>使用其他工具在 Azure DNS 中委派子網域

下列範例會提供使用 PowerShell 和 CLI 在 Azure DNS 中委派子網域的步驟︰

#### <a name="powershell"></a>PowerShell

下列 PowerShell 範例將示範其運作方式。 透過 Azure 入口網站或跨平台 Azure CLI 也可執行相同的步驟。

```powershell
# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
$parent = New-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
$child = New-AzureRmDnsZone -Name partners.contoso.net -ResourceGroupName contosoRG

# Retrieve the authoritative NS records from the child zone as shown in the next example. This contains the name servers assigned to the child zone.
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

# Create the corresponding NS record set in the parent zone to complete the delegation. The record set name in the parent zone matches the child zone name, in this case "partners".
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

使用 `nslookup` 透過查閱子區域的 SOA 記錄，確認一切都已正確設定。

```
nslookup -type=SOA partners.contoso.com
```

```
Server: ns1-08.azure-dns.com
Address: 208.76.47.8

partners.contoso.com
    primary name server = ns1-08.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
#!/bin/bash

# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
az network dns zone create -g contosoRG -n contoso.net
az network dns zone create -g contosoRG -n partners.contoso.net
```

從輸出擷取 `partners.contoso.net` 區域的名稱伺服器。

```
{
  "etag": "00000003-0000-0000-418f-250de2b2d201",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosorg/providers/Microsoft.Network/dnszones/partners.contoso.net",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "partners.contoso.net",
  "nameServers": [
    "ns1-09.azure-dns.com.",
    "ns2-09.azure-dns.net.",
    "ns3-09.azure-dns.org.",
    "ns4-09.azure-dns.info."
  ],
  "numberOfRecordSets": 2,
  "resourceGroup": "contosorg",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

建立每部名稱伺服器的記錄集和 NS 記錄。

```azurecli
#!/bin/bash

# Create the record set
az network dns record-set ns create --resource-group contosorg --zone-name contoso.net --name partners

# Create a ns record for each name server.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns1-09.azure-dns.com.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns2-09.azure-dns.net.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns3-09.azure-dns.org.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns4-09.azure-dns.info.
```

## <a name="delete-all-resources"></a>刪除所有資源

若要刪除這篇文章中建立的所有資源，請完成下列步驟︰

1. 在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 在 [所有資源] 刀鋒視窗中，按一下 [contosorg] 資源群組。 如果您選取的訂用帳戶已有幾個資源，可以在 [依名稱篩選...] 方塊中輸入 **contosorg**， 輕鬆地存取資源群組。
1. 在 [contosorg] 刀鋒視窗中，按一下 [刪除] 按鈕。
1. 入口網站會要求您輸入資源群組的名稱，以確認您想要刪除它。 輸入 contosorg 作為資源群組名稱，然後按一下 [刪除]。 刪除資源群組會刪除資源群組內的所有資源，所以務必確認資源群組的內容，然後再刪除它。 入口網站會刪除資源群組內包含的所有資源，然後刪除資源群組本身。 這個程序需要幾分鐘的時間。

## <a name="next-steps"></a>後續步驟

[管理 DNS 區域](dns-operations-dnszones.md)

[管理 DNS 記錄](dns-operations-recordsets.md)
