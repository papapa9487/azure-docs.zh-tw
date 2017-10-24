---
title: "在 Azure Stack 上使用 SQL 配接器 RP所提供的資料庫 | Microsoft Docs"
description: "如何建立及管理使用 SQL 配接器的資源提供者佈建的 SQL 資料庫"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 0cc08c37e879b00f8cd9a4046a4c81c55dab167c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-sql-databases"></a>建立 SQL 資料庫

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

自助服務資料庫是透過使用者入口網站體驗所提供的。 使用者需要供應項目中包含資料庫服務的訂用帳戶。

1. 登入 [Azure Stack ](azure-stack-poc.md) 使用者入口網站 (服務管理員也可以使用管理入口網站)。

2. 按一下 [+新增] &gt;[資料+儲存體] &gt;[SQL Server 資料庫 (預覽)] &gt;[新增]。

3. 在表單中填入資料庫詳細資料，包括**資料庫名稱**、**大小上限**，並視需要變更其他參數。 系統會要求您為資料庫挑選一個 SKU。 新增主控伺服器時，系統會指派一個 SKU 給它們。 並在構成 SKU 之主控伺服器的該集區中建立資料庫。

  ![新資料庫](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > 資料庫大小必須至少為 64 MB。 可以使用設定來增加。

4. 填入登入設定：[資料庫登入] 和 [密碼]。 這些設定為僅供您存取此資料庫而建立的 SQL 驗證認證。 此登入使用者名稱必須是全域唯一的。 建立新的登入設定或選取現有的設定。 您可以將登入設定重複用於其他使用相同 SKU 的資料庫。

    ![建立新的資料庫登入](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. 提交表單並等候部署完成。

    請在產生的刀鋒視窗中注意 [連接字串] 欄位。 您可以在 Azure Stack 中，於需要 SQL Server 存取權的任何應用程式 (例如 Web 應用程式) 中使用該字串。

    ![擷取連接字串](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-databases"></a>刪除 SQL 資料庫
從入口網站，

>[!NOTE]
>
>從 RP 刪除 SQL AlwaysOn 資料庫時，會成功從主要伺服器與 AlwaysOn 可用性群組中刪除該資料庫，但根據設計，SQL AG 會使每個複本中的資料庫處於還原中狀態，並且不會卸除資料庫 (除非觸發卸除動作)。 如果未卸除資料庫，次要複本會進入「未進行同步處理」狀態。 透過 RP 以相同方式重新將新資料庫新增至 AG 仍能運作。 請參閱![移除次要資料庫](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server)

## <a name="manage-database-credentials"></a>管理資料庫認證
您可以更新資料庫認證 (登入設定)。

## <a name="verify-sql-alwayson-databases"></a>確認 SQL AlwaysOn 資料庫
AlwaysOn 資料庫應該會顯示為已同步處理，而且在所有執行個體和可用性群組中可供使用。 容錯移轉之後，資料庫應該會順暢地連接。 您可以使用 SQL Server Management Studio 來確認資料庫正在同步處理：

![確認 AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)
