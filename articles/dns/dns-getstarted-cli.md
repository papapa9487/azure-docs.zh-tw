---
title: "利用 Azure CLI 2.0 開始使用 Azure DNS | Microsoft Docs"
description: "了解如何在 Azure DNS 中建立 DNS 區域和記錄。 這份逐步指南將引導您使用 Azure CLI 2.0 建立和管理第一個 DNS 區域和記錄。"
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: adcbe69abca72aa0372b78eb357efdc56acf1c26
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>利用 Azure CLI 2.0 開始使用 Azure DNS

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

本文將逐步引導您使用跨平台 Azure CLI 2.0 (適用於 Windows、Mac 和 Linux)，建立第一個 DNS 區域和記錄。 您也可以使用 Azure 入口網站或 Azure PowerShell 執行這些步驟。

DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始將網域裝載到 Azure DNS 中，您必須建立該網域名稱的 DNS 區域。 接著在此 DNS 區域內，建立網域的每筆 DNS 記錄。 最後，若要將 DNS 區域發佈至網際網路，您需要設定網域的名稱伺服器。 以下說明上述各步驟。

這些指示假設您已經安裝並登入 Azure CLI 2.0。 如需說明，請參閱[如何使用 Azure CLI 2.0 管理 DNS 區域](dns-operations-dnszones-cli.md)。

## <a name="create-the-resource-group"></a>建立資源群組

建立 DNS 區域之前，會建立資源群組以包含 DNS 區域。 以下顯示命令。

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>建立 DNS 區域

使用 `az network dns zone create` 命令建立 DNS 區域。 若要查看此命令的說明，請輸入 `az network dns zone create -h`。

下列範例會在 MyResourceGroup 資源群組中建立稱為 contoso.com 的 DNS 區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```


## <a name="create-a-dns-record"></a>建立 DNS 記錄

若要建立 DNS 記錄，請使用 `az network dns record-set [record type] add-record` 命令。 如需說明，例如 A 記錄，請參閱 `azure network dns record-set A add-record -h`。

下列範例會在資源群組 "MyResourceGroup" 中的 DNS 區域 "contoso.com" 中，建立具有相對名稱 "www" 的記錄。 記錄集的完整名稱是 "www.contoso.com"。 記錄類型為 "A"，IP 位址是 "1.2.3.4"，使用預設 TTL 3600 秒 (1 小時)。

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

關於其他記錄類型、具有多個記錄的記錄集、其他 TTL 值和修改現有的記錄，請參閱[使用 Azure CLI 2.0 管理 DNS 記錄和記錄集](dns-operations-recordsets-cli.md)。


## <a name="view-records"></a>檢視記錄

若要列出區域中的 DNS 記錄，請使用︰

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```


## <a name="update-name-servers"></a>更新名稱伺服器

當您滿意 DNS 區域且已正確設定記錄之後，您必須設定網域名稱來使用 Azure DNS 名稱伺服器。 這可讓網際網路上的其他使用者找到您的 DNS 記錄。

`az network dns zone show` 命令可顯示您的區域的名稱伺服器。 若要查看名稱伺服器的名稱，請使用 JSON 輸出，如下列範例所示。

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

這些名稱伺服器應該向網域名稱註冊機構 (您購買網域名稱的來源) 設定。 您的註冊機構會提供選項來設定網域的名稱伺服器。 如需詳細資訊，請參閱[將網域委派給 Azure DNS](dns-domain-delegation.md)。

## <a name="delete-all-resources"></a>刪除所有資源
 
若要刪除這篇文章中建立的所有資源，請採取下列步驟︰

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure DNS，請參閱 [Azure DNS 概觀](dns-overview.md)。

若要深入了解在 Azure DNS 中管理 DNS 區域，請參閱[使用 Azure CLI 2.0 在 Azure DNS 中管理 DNS 區域](dns-operations-dnszones-cli.md)。

若要深入了解在 Azure DNS 中管理 DNS 記錄，請參閱[使用 Azure CLI 2.0 在 Azure DNS 中管理 DNS 記錄和記錄集](dns-operations-recordsets-cli.md)。
