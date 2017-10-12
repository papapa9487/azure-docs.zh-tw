---
title: "Azure 訂用帳戶限制與配額 | Microsoft Docs"
description: "提供通用的 Azure 訂用帳戶和服務限制、配額和條件約束的清單。 這包括如何增加限制和最大值的詳細資訊。"
services: 
documentationcenter: 
author: rothja
manager: jeffreyg
editor: 
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: byvinyal
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: f9f7449fbfc6a1f2fe96e19dd2b13845e602946d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/13/2017

---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure 訂用帳戶和服務限制、配額與限制
本文件列出一些最常見的 Microsoft Azure 限制，有時也稱為配額。 本文件目前未涵蓋所有 Azure 服務。 清單將隨著時間擴展並更新以涵蓋更多平台。

請瀏覽 [Azure 定價一覽](https://azure.microsoft.com/pricing/) 以深入了解 Azure 定價。 您可以在那裡使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)或透過瀏覽服務 (例如，[Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)) 的定價詳細資料頁面來估計成本。 如需協助您管理成本的祕訣，請參閱[使用 Azure 計費與成本管理避免非預期的成本](billing/billing-getting-started.md)。

> [!NOTE]
> 如果您想要將限制或配額提升到**預設限制**以上，您可以[免費提出線上客戶支援要求](azure-supportability/resource-manager-core-quotas-request.md)。 您無法將限制提升到高於下表中所示的**上限**值。 如果沒有**上限**欄，資源即沒有可調整的限制。 
> 
> 免費試用訂用帳戶無法增加限制或配額。 如果您有免費試用，則可以升級到 [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/) 訂用帳戶。 如需詳細資訊，請參閱[將 Azure 免費試用版升級至隨收隨付](billing/billing-upgrade-azure-subscription.md)。
> 

## <a name="limits-and-the-azure-resource-manager"></a>限制和 Azure 資源管理員
現在您可以結合多個 Azure 中的資源到單一的 Azure 資源群組。 使用資源群組時的限制是，在全域時會使用 Azure 資源管理員在地區層級管理。 如需 Azure 資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](azure-resource-manager/resource-group-overview.md)。

在以下的限制中，已加入了新資料表，以反映在使用 Azure 資源管理員時的限制方面的任何差異。 例如，有**訂用帳戶限制**資料表和**訂用帳戶限制 - Azure Resource Manager** 資料表。 當某個限制同時適用於這兩個案例時，只會顯示在第一個資料表中。 除非另有說明，限制在所有區域中全域適用。

> [!NOTE]
> 請務必強調 Azure 資源群組中資源的配額是基於您的訂閱可以存取的每一區域，而不是每一訂閱 (服務管理配額則是)。 讓我們以核心配額為例。 如果您需要要求增加配額以支援核心，您必須決定您想要在哪些區域中使用多少個核心，然後提出 Azure 資源群組核心配額的特定要求，以取得您想要的數量和區域。 因此，如果您需要在西歐使用 30 個核心以在該處執行應用程式，您應該在西歐特別要求 30 個核心。 但是您在任何其他區域中的核心配額將不會增加 -- 僅西歐會有 30 個核心配額。
> <!-- -->
> 因此，考慮決定每個區域中您的工作負載所需的 Azure 資源群組配額，並在要考慮部署的每個區域中要求該數量可能會有所幫助。 請參閱 [移難排解部署問題](resource-manager-common-deployment-errors.md) ，以取得探索您特定區域目前的配額的其他說明。
> 
> 

## <a name="service-specific-limits"></a>特定服務的限制
* [Active Directory](#active-directory-limits)
* [API 管理](#api-management-limits)
* [App Service](#app-service-limits)
* [應用程式閘道](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [自動化](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [事件格線](#azure-event-grid-limits)
* [Azure Redis 快取](#azure-redis-cache-limits)
* [備份](#backup-limits)
* [批次](#batch-limits)
* [BizTalk 服務](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [雲端服務](#cloud-services-limits)
* [Container Instances](#container-instances-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [DNS](#dns-limits)
* [事件中樞](#event-hubs-limits)
* [IoT 中心](#iot-hub-limits)
* [IoT 中樞裝置佈建服務](#iot-hub-device-provisioning-service-limits)
* [金鑰保存庫](#key-vault-limits)
* [Log Analytics / Operational Insights](#log-analytics-limits)
* [媒體服務](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [行動服務](#mobile-services-limits)
* [監視](#monitor-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [網路功能](#networking-limits)
* [網路監看員](#network-watcher-limits)
* [通知中樞服務](#notification-hub-service-limits)
* [資源群組](#resource-group-limits)
* [排程器](#scheduler-limits)
* [Search](#search-limits)
* [服務匯流排](#service-bus-limits)
* [站台復原](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [儲存體](#storage-limits)
* [StorSimple 系統](#storsimple-system-limits)
* [串流分析](#stream-analytics-limits)
* [訂用帳戶](#subscription-limits)
* [流量管理員](#traffic-manager-limits)
* [虛擬機器](#virtual-machines-limits)
* [虛擬機器擴展集](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>訂用帳戶限制
#### <a name="subscription-limits"></a>訂用帳戶限制
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>訂用帳戶限制 - Azure Resource Manager
使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。 使用 Azure 資源管理員時未變更的限制不會在以下列出。 請參閱先前的資料表來瞭解這些限制。

如需處理限制 Resource Manager 要求的資訊，請參閱[節流 Resource Manager 要求](resource-manager-request-limits.md)。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>資源群組限制
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>虛擬機器限制
#### <a name="virtual-machine-limits"></a>虛擬機器限制
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>虛擬機器限制 - Azure 資源管理員
使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。 使用 Azure 資源管理員時未變更的限制不會在以下列出。 請參閱先前的資料表來瞭解這些限制。

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>虛擬機器擴展集限制
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>容器執行個體限制
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="networking-limits"></a>網路限制
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>網路限制
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>應用程式閘道限制
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>網路監看員限制
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>流量管理員限制
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>DNS 限制
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>儲存體限制
如需儲存體帳戶限制的其他詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](storage/common/storage-scalability-targets.md)。
<!--like # storage accts --> 
#### <a name="storage-service-limits"></a>儲存體服務限制
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>虛擬機器磁碟限制 
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

如需其他詳細資訊，請參閱 [虛擬機器大小](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 。

#### <a name="managed-virtual-machine-disks"></a>受管理的虛擬機器磁碟

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>未受管理的虛擬機器磁碟

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>儲存體資源提供者限制
[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="cloud-services-limits"></a>雲端服務限制
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>應用程式服務限制
下列應用程式服務限制包含 Web 應用程式、行動應用程式、API 應用程式和邏輯應用程式的限制。

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>排程器限制
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch 限制
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk 服務限制
下表顯示 Azure Biztalk 服務的限制。

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB 限制
Azure Cosmos DB 是一個全域調整資料庫，可以調整輸送量和儲存體來因應您應用程式的需要。 如果您有關於 Azure Cosmos DB 調整的問題，請傳送電子郵件給 askcosmosdb@microsoft.com。

### <a name="mobile-engagement-limits"></a>Mobile Engagement 限制
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>搜尋限制
定價層會決定搜尋服務的容量和限制。 層級包括：

*  多租用戶服務，與其他 Azure 訂戶共用，適用於評估及小型開發專案。
*  可針對規模較小的生產工作負載提供專用的計算資源，以及針對高可用性的查詢工作負載提供最多 3 個複本。
*  適用於較大型生產工作負載。 標準層內具有多個層級，如此就能讓您選擇最符合工作負載設定檔的資源設定。

**每一訂用帳戶的限制**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**每個搜尋服務的限制**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

若要深入了解更細微的限制，例如文件大小、每秒的查詢數、金鑰、要求和回應，請參閱 [Azure 搜尋服務的服務限制](search/search-limits-quotas-capacity.md)。

### <a name="media-services-limits"></a>媒體服務限制
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>CDN 限制
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>行動服務限制
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>監視限制
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>通知中樞服務限制
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>事件中樞限制
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>服務匯流排限制
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT 中樞限制
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>IoT 中樞裝置佈建服務限制
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Data Factory 限制
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics 限制
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store 限制
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="stream-analytics-limits"></a>串流分析限制
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory 限制
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Azure Event Grid 限制
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple 系統限制
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Log Analytics 限制
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>備份限制
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Site Recovery 限制
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights 限制
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>API 管理限制
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Azure Redis 快取限制
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>金鑰保存庫限制
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>自動化限制
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>SQL Database 限制
如需 SQL Database 的限制，請參閱 [SQL Database 資源限制](sql-database/sql-database-resource-limits.md)。

## <a name="see-also"></a>另請參閱
[了解 Azure 限制和增加](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Azure 的虛擬機器和雲端服務大小](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[雲端服務的大小](cloud-services/cloud-services-sizes-specs.md)

