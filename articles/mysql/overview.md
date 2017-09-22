---
title: "Azure Database for MySQL 關聯式資料庫服務的概觀 | Microsoft Docs"
description: "Azure Database for MySQL 關聯式資料庫服務的概觀。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 3eff97a9e7568566cd01a4eb3c7ee66806eac4cc
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---


# <a name="what-is-azure-database-for-mysql-service-introduction"></a>什麼是 Azure Database for MySQL？ 服務簡介
Azure Database for MySQL 是 Microsoft 雲端中以 [MySQL Community Edition](https://www.mysql.com/products/community/) 資料庫引擎為基礎的關聯式資料庫服務。  Azure Database for MySQL 提供：

- 多個服務層級的可預測效能。
- 零應用程式停機的動態延展性。
- 內建高可用性。
- 資料保護。

這些功能幾乎不需要管理，而且免費提供。 這些功能可讓您專注於快速開發應用程式及加快上市時間，而不是將寶貴的時間和資源耗費在管理虛擬機器和基礎結構上。 此外，您可以使用開放原始碼工具與選擇的平台繼續開發應用程式，並提供企業所需的速度和效率而不需要學習新技能。

![Azure Database for MySQL 概念圖](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

本文介紹 Azure Database for MySQL 在效能、延展性和管理能力方面的核心概念和功能，並提供連結讓您進一步了解詳細資料。 請參閱下列快速入門，以便開始使用產品：
- [使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-portal.md)
- [使用 Azure CLI 建立適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-cli.md)

如需一組 Azure CLI 範例，請參閱︰
- [Azure Database for MySQL 的 Azuer CLI 範例](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>無須停機即可調整效能和規模
Azure Database for MySQL 目前提供兩個服務層︰基本和標準。 每個服務層提供不同的效能和功能，以支援輕量到重量級的資料庫工作負載。 您可以在小型資料庫中建置第一個應用程式，一個月只需少許花費，接著再以零停機的方式變更您的服務層，以隨著解決方案需求做調整。 動態延展性可讓您的資料庫以透明的方式回應快速變化的資源需求。 您只需支付您所需的資源費用。

## <a name="monitoring-and-alerting"></a>監視和警示
當您調高和調低效能時，要如何知道該在何處停止？ 使用內建的效能監視和警示功能，加上以計算單位為基礎的效能分級。 使用這些功能，您可以根據目前或專案的效能需求快速評估相應增加或減少的影響。 如需詳細資訊，請參閱[概念︰服務層](concepts-service-tiers.md)。

## <a name="keep-your-app-and-business-running"></a>讓您的應用程式和業務持續運作
Azure 領先業界的 99.99% 可用性服務等級協定 (SLA) (由 Microsoft 管理之資料中心的全球網路提供支援)，可協助讓您的應用程式 24 小時全年無休地運作。 使用每一 Azure Database for MySQL 伺服器，您可以利用內建的安全性、容錯功能，以及可能需要另外設計、購買、建置和管理的資料保護功能。 使用 Azure Database for MySQL，您可以使用時間點還原將伺服器回復成先前的狀態，最久可至 35 天前。

## <a name="secure-your-data"></a>保護您的資料
Azure 資料庫服務一向重視資料安全性，Azure Database for MySQL 以限制存取、保護靜止和移動中資料及協助監視活動等功能承襲了這項傳統。 如需 Azure 平台安全性的相關資訊，請造訪 [Azure 信任中心 (英文)](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx)。

適用於 MySQL 的 Azure 資料庫服務針對靜止資料使用儲存體加密。 包含備份在內的資料會在磁碟上加密 (不包括執行查詢時由引擎所建立的暫存檔案)。 該服務使用包含在 Azure 儲存體加密中的 AES 256 位元加密，且金鑰是由系統進行管理。 儲存體加密會一律啟用，且無法停用。

根據預設，適用於 MySQL 的 Azure 資料庫服務已設為針對跨網路的動態資料需要 [SSL 連線安全性](./concepts-ssl-connection-security.md)。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將伺服器與應用程式之間的資料流加密，有助於抵禦「中間人」攻擊。  (選擇性) 如果您的用戶端應用程式不支援 SSL 連線能力，您可以停用需要 SSL 才能連接到您資料庫服務的功能。

## <a name="next-steps"></a>後續步驟
您現已閱讀 Azure Database for MySQL 簡介並回答了「什麼是 Azure Database for MySQL？」問題，您就可以：
- 查看定價頁面的成本比較和計算機。 [價格](https://azure.microsoft.com/pricing/details/mysql/)
- 從建立您的第一部伺服器開始。 [使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-portal.md)
- 使用 Python、PHP、Ruby、C\#、Java、Node.js 建立您的第一個應用程式：[用於連線至 Azure Database for MySQL 的連線庫](concepts-connection-libraries.md)

