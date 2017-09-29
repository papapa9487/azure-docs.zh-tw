---
title: "建立 Web 應用程式的自訂 DNS 記錄 | Microsoft Docs"
description: "如何使用 Azure DNS 來建立 Web 應用程式的自訂網域 DNS 記錄。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: d4b0aa817c3fd7f3304b5122ac584166d8079d3c
ms.contentlocale: zh-tw
ms.lasthandoff: 09/28/2017

---

# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>在自訂網域中建立 Web 應用程式的 DNS 記錄

您可以使用 Azure DNS 來裝載 Web 應用程式的自訂網域。 例如，您正在建立 Azure Web 應用程式，而且想要讓使用者使用 contoso.com 或 www.contoso.com 做為 FQDN 來存取它。

若要這樣做，您必須建立兩筆記錄︰

* 指向 contoso.com 的根 "A" 記錄
* 指向 A 記錄之 www 名稱的 "CNAME" 記錄

請記住，如果您在 Azure 中建立 Web 應用程式的 A 記錄，如果 Web 應用程式的基礎 IP 位址變更，則您必須手動更新 A 記錄。

## <a name="before-you-begin"></a>開始之前

開始之前，您必須先在 Azure DNS 中建立 DNS 區域，並將註冊機構中的區域委派給 Azure DNS。

1. 若要建立 DNS 區域，請依照 [建立 DNS 區域](dns-getstarted-create-dnszone.md)的步驟進行。
2. 若要將 DNS 委派給 Azure DNS，請依照 [DNS 網域委派](dns-domain-delegation.md)中的步驟進行。

建立區域並委派給 Azure DNS 之後，便可以為您的自訂網域建立記錄。

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1.建立自訂網域的 A 記錄

A 記錄可用來將名稱對應到其 IP 位址。 在下列範例中，我們會將 @ 當成 A 記錄指派給 IPv4 位址：

### <a name="step-1"></a>步驟 1

建立 A 記錄，並指派給變數 $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>步驟 2

使用指派的 $rs 變數，將 IPv4 值新增至先前建立的記錄集 "@"。 指派的 IPv4 值將是您 Web 應用程式的 IP 位址。

若要尋找 Web 應用程式的 IP 位址，請依照[在 Azure App Service 中設定自訂網域名稱](../app-service/app-service-web-tutorial-custom-domain.md)的步驟進行。

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>步驟 3

認可對記錄集所做的變更。 使用 `Set-AzureRMDnsRecordSet` 將記錄集的變更上傳到 Azure DNS：

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2.建立自訂網域的 CNAME 記錄

如果您的網域已受 Azure DNS 管理 (請參閱 [DNS 網域委派](dns-domain-delegation.md))，您可以使用下列範例，建立 contoso.azurewebsites.net 的 CNAME 記錄。

### <a name="step-1"></a>步驟 1

開啟 PowerShell 並建立新的 CNAME 記錄集，然後指派給變數 $rs。 此範例會在名為 "contoso.com" 的DNS 區域中建立一個「存留時間」為 600 秒的記錄集類型 CNAME。

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

以下是回應範例。

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>步驟 2

一旦建立 CNAME 記錄集，您需要建立將指向 Web 應用程式的別名值。

使用先前指派的變數 "$rs"，您可以使用下列 PowerShell 命令來建立 Web 應用程式 contoso.azurewebsites.net 的別名。

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

以下是回應範例。

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>步驟 3

使用 `Set-AzureRMDnsRecordSet` Cmdlet 來認可所做的變更：

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

您可以使用 nslookup 來驗證由查詢 "www.contoso.com" 正確建立的記錄，如下所示：

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net
```

## <a name="create-an-awverify-record-for-web-apps"></a>建立 Web Apps 的 awverify 記錄

如果您決定使用 Web 應用程式的 A 記錄，您必須通過驗證程序，才能確保您擁有自訂網域。 此驗證步驟可透過建立名為 "awverify" 的特殊 CNAME 記錄來完成。 本節適用於僅限 A 記錄。

### <a name="step-1"></a>步驟 1

建立 awverify 記錄。 在以下範例中，我們將建立 contoso.com 的 "awverify" 記錄，以驗證自訂網域的擁有權。

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

以下是回應範例。

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>步驟 2

一旦建立 "awverify" 記錄集，請指派 CNAME 記錄集別名。 在以下範例中，我們會將 CNAME 記錄集別名指派為 awverify.contoso.azurewebsites.net。

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

以下是回應範例。

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>步驟 3

使用 `Set-AzureRMDnsRecordSet cmdlet`認可所做的變更，如下列命令所示。

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>後續步驟

依照 [設定 App Service 的自訂網域名稱](../app-service/app-service-web-tutorial-custom-domain.md) 中的步驟設定 Web 應用程式使用自訂網域。

