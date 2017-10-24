---
title: "適用於 MySQL 的 Azure 資料庫中的限制 | Microsoft Docs"
description: "描述適用於 MySQL 的 Azure 資料庫中的預覽限制。"
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/8/2017
ms.openlocfilehash: dc5ad012398b7d07886cd22c20975e61f820d7e0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2017
---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>適用於 MySQL 的 Azure 資料庫中的限制 (預覽)
適用於 MySQL 的 Azure 資料庫服務目前為公開預覽狀態。 下列各節說明資料庫服務中的容量和功能限制。 另請參閱適用於 MySQL 資料庫引擎的[一般限制](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) \(英文\)。

## <a name="service-tier-maximums"></a>服務層上限
適用於 MySQL 的 Azure 資料庫具有多個可在建立伺服器時從中選擇的服務層。 如需詳細資訊，請參閱[了解每個服務層中可用的項目](concepts-service-tiers.md)。  

在預覽期間，每個服務層中具有連線、計算單位及儲存體的數目上限，如下： 

|                            |                   |
| :------------------------- | :---------------- |
| **連接數目上限**        |                   |
| 基本的 50 個計算單位     | 50 個連接    |
| 基本的 100 個計算單位    | 100 個連接   |
| 標準的 100 個計算單位 | 200 個連接   |
| 標準的 200 個計算單位 | 400 個連接   |
| 標準的 400 個計算單位 | 400 個連接   |
| 標準的 800 個計算單位 | 1600 個連線  |
| **計算單位數目上限**      |                   |
| 基本服務層         | 100 個計算單位 |
| 標準服務層      | 800 個計算單位 |
| **儲存體上限**            |                   |
| 基本服務層         | 1 TB              |
| 標準服務層      | 1 TB              |

到達太多連接時，您可能會收到下列錯誤：
> 錯誤 1040 (08004)：太多的連接

## <a name="preview-functional-limitations"></a>預覽功能限制

### <a name="scale-operations"></a>調整作業
- 目前不支援跨服務層動態調整伺服器。 也就是在基本和標準服務層之間切換。
- 目前不支援依需求在預先建立的伺服器上動態增加儲存體。
- 不支援減少伺服器儲存體大小。

### <a name="server-version-upgrades"></a>伺服器版本升級
- 目前不支援在主要資料庫引擎版本之間進行自動轉換。

### <a name="subscription-management"></a>訂用帳戶管理
- 目前不支援跨訂用帳戶和資源群組動態移動預先建立的伺服器。

### <a name="point-in-time-restore"></a>還原時間點
- 不允許還原到不同服務層和/或計算單位與儲存體大小。
- 不支援還原已卸除的伺服器。

## <a name="next-steps"></a>後續步驟
- [每個服務層中可用的項目](concepts-service-tiers.md)
- [支援的 MySQL 資料庫版本](concepts-supported-versions.md)
