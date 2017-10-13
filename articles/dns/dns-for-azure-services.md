---
title: "使用 Azure DNS 搭配其他 Azure 服務 | Microsoft Docs"
description: "了解如何使用 Azure DNS 來解析其他 Azure 服務的名稱"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: gwallace
ms.openlocfilehash: a286508fe445208b6bb348d07434b5722cc3f11e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Azure DNS 如何與其他 Azure 服務搭配運作

Azure DNS 是一種託管的 DNS 管理與名稱解析服務。 這可讓您為其他應用程式和已在 Azure 中部署的服務建立公用 DNS 名稱。 為您的自訂網域中的 Azure 服務建立名稱，就和為您的服務新增正確類型的記錄一樣簡單。

* 若使用動態配置的 IP 位址，您必須建立 DNS CNAME 記錄，對應至 Azure 為您的服務建立的 DNS 名稱。 DNS 標準禁止您在區域頂點使用 CNAME 記錄。
* 若使用靜態配置的 IP 位址，您可以用任何名稱建立 DNS A 記錄，包括在區域頂點的 *裸網域* 名稱。

下表概述可用於各種 Azure 服務的支援記錄類型。 如您在表中所見，Azure DNS 只支援網際網路面向網路資源的 DNS 記錄。 Azure DNS 無法用於內部私人位址的名稱解析。

| Azure 服務 | 網路介面 | 說明 |
| --- | --- | --- |
| 應用程式閘道 |[前端公用 IP](dns-custom-domain.md#public-ip-address) |您可以建立 DNS A 或 CNAME 記錄。 |
| 負載平衡器 |[前端公用 IP](dns-custom-domain.md#public-ip-address)  |您可以建立 DNS A 或 CNAME 記錄。 負載平衡器可以有動態指派的 IPv6 公用 IP 位址。 因此，您必須建立用於 IPv6 位址的 CNAME 記錄。 |
| 流量管理員 |公開名稱 |您只能建立對應至指派給您的流量管理員設定檔之 trafficmanager.net 名稱的 CNAME。 如需詳細資訊，請參閱 [流量管理員的運作方式](../traffic-manager/traffic-manager-overview.md#traffic-manager-example)。 |
| 服務雲端 |[公用 IP](dns-custom-domain.md#public-ip-address) |若使用靜態配置的 IP 位址，您可以建立 DNS A 記錄。 若使用動態配置的 IP 位址，您必須建立對應至 *cloudapp.net* 名稱的 CNAME 記錄。 此規則適用於在傳統入口網站建立的 VM，因為它們被部署為雲端服務。 如需詳細資訊，請參閱 [在 雲端服務中設定自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 |
| App Service | [外部 IP](dns-custom-domain.md#app-service-web-apps) |若使用外部 IP 位址，您可以建立 DNS A 記錄。 若不是，則必須建立對應至 azurewebsites.net 名稱的 CNAME 記錄。 如需詳細資訊，請參閱 [將自訂網域名稱對應至 Azure 應用程式](../app-service/app-service-web-tutorial-custom-domain.md) |
| Resource Manager VM |[公用 IP](dns-custom-domain.md#public-ip-address) |Resource Manager VM 可以有公用 IP 位址。 帶有公用 IP 位址的 VM 也可能擺在負載平衡器後。 您可以為公用位址建立 DNS A 或 CNAME 記錄。 這個自訂名稱可以用來略過負載平衡器上的 VIP。 |
| 傳統 VM |[公用 IP](dns-custom-domain.md#public-ip-address) |使用 PowerShell 或 CLI 建立的傳統 VM 可設定為使用動態或靜態 (保留) 的虛擬位址。 您可以分別建立 DNS CNAME 或 A 記錄。 |
