---
title: "適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則 | Microsoft Docs"
description: "描述適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/10/2017
ms.openlocfilehash: a8e1980900b430e56b0c01a4446dc525d25698da
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則
「適用於 PostgreSQL 的 Azure 資料庫」伺服器防火牆會防止對您資料庫伺服器進行的所有存取，直到您指定哪些電腦擁有權限為止。 此防火牆會根據每一個要求的來源 IP 位址來授與伺服器存取權。
若要設定您的防火牆，您可以建立防火牆規則，指定可接受的 IP 位址範圍。 您可以在伺服器層級建立防火牆規則。

**防火牆規則：**這些規則可讓用戶端完整存取適用於 PostgreSQL 的 Azure 資料庫伺服器，也就是相同邏輯伺服器內的所有資料庫。 使用 Azure 入口網站或使用 Azure CLI 命令，即可設定伺服器層級的防火牆規則。 若要建立伺服器層級的防火牆規則，您必須是訂用帳戶擁有者或訂用帳戶參與者。

## <a name="firewall-overview"></a>防火牆概觀
根據預設，防火牆會封鎖對適用於 PostgreSQL 之 Azure 資料庫伺服器的所有資料庫存取。 若要從其他電腦開始使用您的伺服器，請指定一或多個伺服器層級的防火牆規則，以便啟用對伺服器的存取。 使用防火牆規則，來指定允許從網際網路存取的 IP 位址範圍。 存取 Azure 入口網站本身，並不會受到防火牆規則所影響。
來自網際網路和 Azure 的連接嘗試必須先通過防火牆，才能到達您的 PostgreSQL 資料庫，如下圖所示：

![防火牆運作方式的範例流程](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>從網際網路連線
伺服器層級的防火牆規則可套用至相同「適用於 PostgreSQL 的 Azure 資料庫」伺服器上的所有資料庫。 如果要求的 IP 位址在伺服器層級防火牆規則中指定的其中一個範圍內，就會允許連線。
如果要求的 IP 位址不在任何伺服器層級防火牆規則中指定的範圍內，連線要求就會失敗。
例如，如果您的應用程式透過適用於 PostgreSQL 的 JDBC 驅動程式來連接，若您在防火牆封鎖連接期間嘗試進行連接，則可能會遇到此錯誤。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="programmatically-managing-firewall-rules"></a>以程式設計方式管理防火牆規則
除了 Azure 入口網站之外，防火牆規則還可以程式設計方式使用 Azure CLI 來管理。
另請參閱[使用 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-cli.md)。

## <a name="troubleshooting-the-database-server-firewall"></a>針對資料庫伺服器防火牆問題進行疑難排解
對於適用於 PostgreSQL 的 Microsoft Azure 資料庫伺服器服務的存取未如預期般運作時，請考慮下列幾點：

* **對允許清單的變更尚未生效：**對適用於 PostgreSQL 之 Azure 資料庫伺服器防火牆組態的變更最多可能會延遲 5 分鐘才能生效。

* **登入未獲授權或使用不正確的密碼：**如果適用於 PostgreSQL 的 Azure 資料庫伺服器上的登入沒有權限，或使用的密碼不正確，與適用於 PostgreSQL 的 Azure 資料庫伺服器連接就會遭到拒絕。 建立防火牆設定只是讓用戶端有機會嘗試連線到您的伺服器；每個用戶端仍然必須提供必要的安全性認證。

例如，使用 JDBC 用戶端，可能會出現下列錯誤。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **動態 IP 位址：** 如果您有使用動態 IP 位址的網際網路連線，並且在通過防火牆時遇到問題，您可以嘗試下列其中一個解決方案：

* 要求您的網際網路服務提供者 (ISP) 將可存取適用於 PostgreSQL 的 Azure 資料庫伺服器之 IP 位址範圍指派給您的用戶端電腦，然後將 IP 位址範圍新增為防火牆規則。

* 改為針對您的用戶端電腦取得靜態 IP 位址，然後將該靜態 IP 位址新增為防火牆規則。

## <a name="next-steps"></a>後續步驟
如需有關建立伺服器層級防火牆規則的文章，請參閱：
* [使用 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-portal.md)。
* [使用 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-cli.md).
