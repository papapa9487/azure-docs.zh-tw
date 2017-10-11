---
title: "使用 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則 | Microsoft Docs"
description: "使用 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/10/2017
ms.openlocfilehash: 20ac1392949a6f604e68d984cb50273b61051037
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>使用 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則
伺服器等級防火牆規則可讓系統管理員從指定的 IP 位址或 IP 位址範圍，存取適用於 PostgreSQL 的 Azure 資料庫伺服器。 

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [建立適用於 PostgreSQL 的 Azure 資料庫](quickstart-create-server-database-portal.md)伺服器

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 入口網站中建立伺服器層級的防火牆規則
1. 在 [PostgreSQL 伺服器] 刀鋒視窗的 [設定] 標題下，按一下 [連線安全性]，開啟適用於 PostgreSQL 的 Azure 資料庫的 [連線安全性] 刀鋒視窗。

  ![Azure 入口網站 - 按一下 [連線安全性]](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. 按一下工具列上的 [新增我的 IP]。 這會使用 Azure 系統發現的電腦 IP 位址自動建立規則。

  ![Azure 入口網站 - 按一下 [新增我的 IP]](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. 儲存設定之前，請確認您的 IP 位址。 在某些情況下，Azure 入口網站觀察到的 IP 位址，不同於用來存取網際網路和 Azure 伺服器的 IP 位址。 因此，您可能需要變更起始 IP 和結束 IP，規則才會正常運作。
使用搜尋引擎或其他線上工具，檢查您自己的 IP 位址 (例如，使用 Bing 搜尋「我的 IP 位址是什麼」)。

  ![使用 Bing 搜尋「我的 IP 是什麼」](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. 新增其他位址範圍。 在「適用於 PostgreSQL 的 Azure 資料庫」防火牆的規則中，您可以指定單一 IP 位址，或位址範圍。 如果您想要將規則限制到單一 IP 位址，請在 [起始 IP] 和 [結束 IP] 欄位中輸入相同位址。 開放防火牆可讓系統管理員和使用者登入他們在 PostgreSQL 伺服器上具備有效認證的任何資料庫。

  ![Azure 入口網站 - 防火牆規則 ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. 按一下工具列上的 [儲存]，儲存此伺服器等級防火牆規則。 等待確認已成功更新防火牆規則。

  ![Azure 入口網站 - 按一下 [儲存]](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)


## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>透過 Azure 入口網站管理現有的伺服器層級防火牆規則
重複步驟來管理防火牆規則。
* 若要新增目前的電腦，請按一下 [+新增我的 IP] 按鈕。 按一下 [儲存]  儲存變更。
* 若要新增其他 IP 位址，請輸入「規則名稱」、「起始 IP 位址」和「結束 IP 位址」。 按一下 [儲存]  儲存變更。
* 若要修改現有的規則，請按一下和修改規則中的任何欄位。 按一下 [儲存]  儲存變更。
* 若要刪除現有的規則，請按一下省略符號 [...]，然後按一下 [刪除] 移除規則。 按一下 [儲存]  儲存變更。

## <a name="next-steps"></a>後續步驟
- 同樣地，您可以透過指令碼[使用 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-cli.md)
- 如需連線至「適用於 PostgreSQL 的 Azure 資料庫」伺服器的說明，請參閱[「適用於 PostgreSQL 的 Azure 資料庫」的連線庫](concepts-connection-libraries.md)
