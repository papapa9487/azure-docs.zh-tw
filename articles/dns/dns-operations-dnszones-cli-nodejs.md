---
title: "管理 Azure DNS 中的 DNS 區域 - Azure CLI 1.0 | Microsoft Docs"
description: "您可以使用 Azure CLI 1.0 管理 DNS 區域。 本文說明如何在 Azure DNS 上更新、刪除及建立 DNS 區域。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
ms.openlocfilehash: 588c87749f049eff5b9e0729f6769c8367ba41e4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-10"></a>如何使用 Azure CLI 1.0 管理 Azure DNS 中的 DNS 區域

> [!div class="op_single_selector"]
> * [入口網站](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

本指南說明如何使用適用於 Windows、Mac 和 Linux 的跨平台 Azure CLI 1.0 來管理 DNS 區域。 您也可以使用 [Azure PowerShell](dns-operations-dnszones.md) 或 Azure 入口網站來管理 DNS 區域。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本

您可以使用下列其中一個 CLI 版本來完成工作︰

* [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md) - 適用於傳統和資源管理部署模型的 CLI。
* [Azure CLI 2.0](dns-operations-dnszones-cli.md) - 適用於資源管理部署模型的新一代 CLI。

## <a name="introduction"></a>簡介

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]

## <a name="getting-help"></a>取得說明

所有與 Azure DNS 相關的 CLI 1.0 命令都會以 `azure network dns` 開頭。 使用 `--help` 選項 (簡短形式為 `-h`) 即可取得每個命令的說明。  例如：

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>建立 DNS 區域

使用 `azure network dns zone create` 命令建立 DNS 區域。 如需協助，請參閱 `azure network dns zone create -h`。

下列範例會在稱為 *MyResourceGroup* 的資源群組中建立稱為 *contoso.com* 的 DNS 區域：

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>使用標籤建立 DNS 區域

下列範例示範如何使用 `--tags` 參數 (簡短形式為 `-t`)，利用 *project = demo* 和 *env = test* 這兩個 [Azure Resource Manager 標籤](dns-zones-records.md#tags)，建立 DNS 區域：

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>取得 DNS 區域

若要擷取 DNS 區域，請使用 `azure network dns zone show`。 如需協助，請參閱 `azure network dns zone show -h`。

下列範例會從資源群組 MyResourceGroup 傳回 DNS 區域 contoso.com 及其相關聯的資料。 

```azurecli
azure network dns zone show MyResourceGroup contoso.com
```

以下是回應範例。

```
info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

請注意，`azure network dns zone show` 不會傳回 DNS 記錄。 若要列出 DNS 記錄，請使用 `azure network dns record-set list`。


## <a name="list-dns-zones"></a>列出 DNS 區域

若要列舉 DNS 區域，請使用 `azure network dns zone list`。 如需協助，請參閱 `azure network dns zone list -h`。

指定資源群組只會列出資源群組內的區域︰

```azurecli
azure network dns zone list MyResourceGroup
```

省略資源群組則會列出訂用帳戶中的所有區域︰

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>更新 DNS 區域

您可以使用 `azure network dns zone set`變更 DNS 區域資源。 如需協助，請參閱 `azure network dns zone set -h`。

此命令不會更新區域內的任何 DNS 記錄集 (請參閱[如何管理 DNS 記錄](dns-operations-recordsets-cli-nodejs.md))。 它只用來更新區域資源本身的屬性。 這些屬性目前僅限於區域資源的 [Azure Resource Manager「標籤」](dns-zones-records.md#tags)。

下列範例示範如何更新 DNS 區域上的標籤。 現有標籤會由指定值取代。

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>刪除 DNS 區域

可以使用 `azure network dns zone delete`刪除 DNS 區域。 如需協助，請參閱 `azure network dns zone delete -h`。

> [!NOTE]
> 刪除 DNS 區域也會刪除該區域內的所有 DNS 記錄。 此作業無法復原。 如果 DNS 區域正在使用中，當該區域遭到刪除時，使用該區域的服務將會失敗。
>
>若要防止區域意外遭到刪除，請參閱[如何保護 DNS 區域和記錄](dns-protect-zones-recordsets.md)。

此命令會提示您確認。 選擇性的 `--quiet` 參數 (簡短形式為 `-q`) 會隱藏這個提示。

下列範例示範如何從資源群組 MyResourceGroup 刪除區域 contoso.com。

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>後續步驟

了解如何在 DNS 區域中[管理記錄集和記錄](dns-getstarted-create-recordset-cli-nodejs.md)。

了解如何[將您的網域委派給 Azure DNS](dns-domain-delegation.md)。

