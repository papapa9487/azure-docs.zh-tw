---
title: "說明適用於 MySQL 的 Azure 資料庫中的計算單位 | Microsoft Docs"
description: "適用於 MySQL 的 Azure DB：本文說明計算單位的概念，以及當您的工作負載到達計算單位上限時，會發生什麼狀況。"
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 2c4894ae9a4235f9ced4a8d9b991238543646f53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="explaining-compute-units-in-azure-database-for-mysql"></a>說明適用於 MySQL 的 Azure 資料庫中的計算單位
本主題說明計算單位的概念，以及當您的工作負載到達計算單位上限時，會發生什麼狀況。

## <a name="what-are-compute-units"></a>什麼是計算單位？
計算單位是 CPU 處理輸送量的量值，保證可供單一適用於 MySQL 伺服器的 Azure 資料庫使用。 計算單位是 CPU 和記憶體資源的混合量值。 一般而言，50 個計算單位等於半個核心。 100 個計算單位等於一個核心。 2,000 個計算單位等於 20 個保證可供您伺服器使用之處理輸送量的核心。

每個計算單位的記憶體數量會針對基本和標準定價層進行最佳化。 藉由提升效能等級來使計算單位數加倍，等於讓該單一適用於 MySQL 的 Azure 資料庫可用的資源集合加倍。

例如，標準的 800 個計算單位所提供的 CPU 輸送量與記憶體，比標準的 100 個計算單位設定多 8 倍。 不過，儘管相較於基本的 100 個計算單位，標準的 100 個計算單位會提供相同的 CPU 輸送量，但是，標準定價層中預先設定的記憶體量是針對基本定價層所設定之記憶體量的兩倍。 因此，比起選取相同計算單位數的基本定價層，標準定價層提供更佳的工作負載效能與更低的交易延遲。

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>如何判斷我的工作負載所需的計算單位數？
如果您想要移轉在內部部署或虛擬機器上執行的現有 MySQL 伺服器，可透過評估工作負載需要多少個處理輸送量核心，來決定計算單位數。 

如果您現有的內部部署或虛擬機器伺服器目前使用 4 個核心 (不含計算 CPU 超執行緒)，一開始請為適用於 MySQL 的 Azure 資料庫伺服器設定 400 個計算單位。 計算單位可根據您的工作負載需求，動態地相應增加或減少，而且幾乎不會產生任何應用程式停機時間。 

在 Azure 入口網站中監視計量圖表，或是撰寫 Azure CLI 命令來測量計算單位。 要監視的相關計量包括計算單位百分比和計算單位限制。

>[!IMPORTANT]
> 如果您發現儲存體 IOPS 未完全用到極限，請考慮同時監視計算單位使用率。 提高計算單位會減少因 CPU 或記憶體有限而造成的效能瓶頸，因此可能會提高 IO 輸送量。

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>當我達到計算單位上限時會發生什麼狀況？
效能等級會受校正和管理，以提供資源來將您的資料庫工作負載執行到所選定價層和效能等級的上限。 

如果您的工作負載達到計算單位或已佈建 IOPS 限制的上限，您可以繼續使用最大允許層級的資源，但可能會遇到較長的查詢延遲。 除非是速度慢到使查詢逾時，否則這些限制會導致工作負載的速度變慢，而不會產生錯誤。 

如果您的工作負載到達連線數目上限，則會引發明確的錯誤。 如需資源限制的詳細資訊，請參閱[適用於 MySQL 的 Azure 資料庫中的限制](concepts-limits.md)。

## <a name="next-steps"></a>後續步驟
如需定價層的詳細資訊，請參閱[適用於 MySQL 的 Azure 資料庫定價層](./concepts-service-tiers.md)。
