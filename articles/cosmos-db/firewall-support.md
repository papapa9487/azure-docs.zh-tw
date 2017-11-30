---
title: "Azure Cosmos DB 防火牆支援和 IP 存取控制 | Microsoft Docs"
description: "了解如何使用 IP 存取控制原則進行 Azure Cosmos DB 資料庫帳戶上的防火牆支援。"
keywords: "IP 存取控制，防火牆支援"
services: cosmos-db
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: ankshah
ms.openlocfilehash: 1ceaa834ff68d5dca4abce561f9185e89af582af
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="azure-cosmos-db-firewall-support"></a>Azure Cosmos DB 防火牆支援
為了保護 Azure Cosmos DB 資料庫帳戶中所儲存的資料，Azure Cosmos DB 已支援利用強式雜湊式訊息驗證碼 (HMAC) 的密碼型[授權模型](https://msdn.microsoft.com/library/azure/dn783368.aspx)。 現在，除了密碼型授權模型之外，Azure Cosmos DB 還支援使用原則驅動的 IP 型存取控制來進行輸入防火牆支援。 此模型與傳統資料庫系統的防火牆規則相類似，且可為 Azure Cosmos DB 資料庫帳戶提供額外的安全性層級。 您現在可以使用這個模型，設定只能從一組核准的電腦和 (或) 雲端服務存取 Azure Cosmos DB 資料庫帳戶。 透過這些核准的電腦和服務組合來存取 Azure Cosmos DB 資源，仍然需要呼叫者呈現有效的授權權杖。

## <a name="ip-access-control-overview"></a>IP 存取控制概觀
只要要求伴隨有效的授權權杖，預設就可以從公用網際網路存取 Azure Cosmos DB 資料庫帳戶。 若要設定 IP 原則型存取控制，使用者必須以 CIDR 形式提供這組 IP 位址或 IP 位址範圍，以作為指定資料庫帳戶的允許用戶端 IP 清單。 套用這個組態之後，伺服器將會封鎖源自此允許清單外部之電腦的所有要求。  下圖說明 IP 型存取控制的連線處理流程：

![顯示 IP 型存取控制之連接程序的圖表](./media/firewall-support/firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>從雲端服務的連接
在 Azure 中，雲端服務是使用 Azure Cosmos DB 裝載中介層服務邏輯的常見方式。 若要從雲端服務存取 Azure Cosmos DB 資料庫帳戶，必須[設定 IP 存取控制原則](#configure-ip-policy)，以將雲端服務的公用 IP 位址新增至與 Azure Cosmos DB 資料庫帳戶相關聯的允許 IP 位址清單。  這確保雲端服務的所有角色執行個體都能存取您的 Azure Cosmos DB 資料庫帳戶。 您可以在 Azure 入口網站中擷取雲端服務的 IP 位址，如下列螢幕擷取畫面所示：

![這個螢幕擷取畫面顯示 Azure 入口網站中所顯示雲端服務的公用 IP 位址](./media/firewall-support/public-ip-addresses.png)

當您新增其他角色執行個體來相應放大雲端服務時，那些新的執行個體將可自動存取 Azure Cosmos DB 資料庫帳戶，因為它們是相同雲端服務的一部分。

## <a name="connections-from-virtual-machines"></a>從虛擬機器的連接
您也可以使用[虛擬機器](https://azure.microsoft.com/services/virtual-machines/)或[虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)，透過 Azure Cosmos DB 裝載中介層服務。  若要設定允許從虛擬機器存取 Azure Cosmos DB 資料庫帳戶，必須[設定 IP 存取控制原則](#configure-ip-policy)，以將虛擬機器和 (或) 虛擬機器擴展集的公用 IP 位址設定為 Azure Cosmos DB 資料庫帳戶的其中一個允許 IP 位址。 您可以在 Azure 入口網站中擷取虛擬機器的 IP 位址，如下列螢幕擷取畫面所示。

![這個螢幕擷取畫面顯示 Azure 入口網站中所顯示虛擬機器的公用 IP 位址](./media/firewall-support/public-ip-addresses-dns.png)

當您將其他虛擬機器執行個體新增至群組時，它們即可自動存取您的 Azure Cosmos DB 資料庫帳戶。

## <a name="connections-from-the-internet"></a>從網際網路的連接
從網際網路上的電腦存取 Azure Cosmos DB 資料庫帳戶時，必須將電腦的用戶端 IP 位址或 IP 位址範圍新增至 Azure Cosmos DB 資料庫帳戶的允許 IP 位址清單。 

## <a name="connections-from-azure-paas-service"></a>從 Azure PaaS 服務的連線 
在 Azure 中，Azure 串流分析、Azure Functions 之類的 PaaS 服務會與 Azure Cosmos DB 搭配使用。 若要能夠從 IP 位址尚無法使用的此類服務存取 Azure Cosmos DB 資料庫帳戶，必須[設定 IP 存取控制原則](#configure-ip-policy)，以將雲端服務的公用 IP 位址 0.0.0.0 新增至與您的 Azure Cosmos DB 資料庫帳戶相關聯的允許 IP 位址清單。  這可確保 Azure PaaS 服務能夠存取具有此規則的 Azure Cosmos DB 帳戶。 

 ## <a id="configure-ip-policy"></a> 設定 IP 存取控制原則
您可以在 Azure 入口網站中設定 IP 存取控制原則，也可以透過 [Azure CLI](cli-samples.md)、[Azure Powershell](powershell-samples.md) 或 [REST API](/rest/api/documentdb/)，以程式設計方式更新 `ipRangeFilter` 屬性來設定。 IP 位址/範圍必須以逗號分隔，而且不得包含任何空格。 範例："13.91.6.132,13.91.6.1/24"。 透過這些方法更新資料庫帳戶時，請務必填入所有屬性，以避免重設為預設設定。

> [!NOTE]
> 啟用 Azure Cosmos DB 資料庫帳戶的 IP 存取控制原則，即會封鎖所設定之允許 IP 位址範圍清單外部的電腦對您 Azure Cosmos DB 資料庫帳戶的所有存取。 透過這個模型，也會封鎖從入口網站瀏覽資料平面作業，確保存取控制的完整性。

為了簡化開發工作，Azure 入口網站可協助您識別用戶端電腦的 IP 並新增至允許清單，讓您電腦上執行的應用程式可以存取 Azure Cosmos DB 帳戶。 此處的用戶端 IP 位址是入口網站所偵測到的。 它可能是您電腦的用戶端 IP 位址，但也可能是網路閘道的 IP 位址。 移至生產環境之前，別忘記移除它。

若要在 Azure 入口網站中設定 IP 存取控制原則，請瀏覽至 [Azure Cosmos DB 帳戶] 刀鋒視窗、按一下導覽功能表中的 [防火牆]，然後按一下 [開啟] 

![顯示如何在 Azure 入口網站中開啟 [防火牆] 刀鋒視窗的螢幕擷取畫面](./media/firewall-support/azure-portal-firewall.png)

在新窗格中，指定 Azure 入口網站是否可以存取帳戶，並適當地新增其他地址和範圍，然後按一下 [儲存]。  

> [!NOTE]
> 當您啟用 IP 存取控制原則時，您必須加入您 Azure 入口網站的 IP 位址以維持存取權。 入口網站 IP 位址是：
> |區域|IP 位址|
> |------|----------|
> |所有區域 (下面指定的區域除外)|104.42.195.92、40.76.54.131、52.176.6.30、52.169.50.45、52.187.184.26|
> |德國|51.4.229.218|
> |中國|139.217.8.252|
> |US Gov|52.244.48.71|
>

![顯示如何在 Azure 入口網站中進行防火牆設定的螢幕擷取畫面](./media/firewall-support/azure-portal-firewall-configure.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>針對 IP 存取控制原則進行疑難排解
### <a name="portal-operations"></a>入口網站作業
啟用 Azure Cosmos DB 資料庫帳戶的 IP 存取控制原則，即會封鎖所設定之允許 IP 位址範圍清單外部的電腦對您 Azure Cosmos DB 資料庫帳戶的所有存取。 因此，如果您想要啟用資料層面作業，例如瀏覽集合和查詢文件，您需要在入口網站使用 [防火牆] 刀鋒視窗，明確允許存取 Azure 入口網站。 

![顯示如何允許存取 Azure 入口網站的螢幕擷取畫面](./media/firewall-support/azure-portal-access-firewall.png)

### <a name="sdk--rest-api"></a>SDK & Rest API
基於安全性考量，如果從電腦透過 SDK 或 REST API 的存取不在允許清單上，則會傳回沒有其他詳細資料的一般「404 找不到」回應。 請確認您針對 Azure Cosmos DB 資料庫帳戶設定的 IP 允許清單，以確保會將正確的原則組態套用至您的 Azure Cosmos DB 資料庫帳戶。

## <a name="next-steps"></a>後續步驟
如需網路相關效能秘訣的相關資訊，請參閱[效能秘訣](performance-tips.md)。

