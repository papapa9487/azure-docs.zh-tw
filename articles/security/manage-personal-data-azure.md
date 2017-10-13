---
title: "在 Microsoft Azure 中管理個人資料 | Microsoft Docs"
description: "指導如何在 Azure Active Directory 與 Azure SQL Database 更正、更新、刪除與匯出個人資料"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 3b57c92bd744644ea81878712b4272ed3ece4e2e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-personal-data-in-microsoft-azure"></a>在 Microsoft Azure 中管理個人資料

本文指導如何在 Azure Active Directory 與 Azure SQL Database 更正、更新、刪除與匯出個人資料。

## <a name="scenario"></a>案例

一家位在都柏林的公司在愛爾蘭與全世界各地為當地與國際客戶的高端渡假地婚禮提供一站購足的服務。 該公司的辦公室、客戶、員工及合作廠商遍及世界各地，為其場地提供完整的服務。

該公司追蹤許多不同的項目，像是包含受邀者食物過敏與飲食偏好之回信。 婚禮賓客可以報名參照各種活動，像是騎馬、衝浪、划船等，甚至在婚禮舉行的數月前就能透過中央網頁彼此互動。 該公司會收集員工、合作廠商、客戶及婚禮賓客的個人資訊。 由於國際商務慣例，該公司必須遵守多種層級的規定。

## <a name="problem-statement"></a>問題陳述

- 資料管理員必須能夠更正不正確的個人資訊，並更新不完整或有異動的個人資訊。

- 資料管理員必須能夠在資料主體要求時刪除個人資訊。

- 資料管理員在資料主體要求時，必須以通用的結構化格式將資料匯出並提供給對方。

## <a name="company-goals"></a>公司目標

- 必須在 Azure Active Directory 與 Azure SQL Database 中更正或更新不正確或不完整的客戶、婚禮賓客、員工及合作廠商的個人資訊。

- 必須應資料主體要求，刪除 Azure Active Directory 與 Azure SQL Database 中的個人資訊。

- 必須應資料主體要求，以通用的結構化格式匯出個人資料。

## <a name="solutions"></a>解決方案

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Azure Active Directory：改正/更正不正確或不完整的個人資料，並清除/刪除個人資料/使用者設定檔

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 的雲端式、多租用戶目錄和身分識別管理服務。
您可以在您的 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) 環境中使用 [Azure 入口網站](https://portal.azure.com/)更正、更新或刪除客戶和員工使用者設定檔與使用者工作資訊，其中包含如使用者名稱、職稱、地址或電話號碼等個人資料。

您必須使用具備目錄全域管理員身分的帳戶來登入。

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>如何在 Azure Active Directory 中更正或更新使用者設定檔和工作資訊？

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。

2. 選取 [更多服務]，在文字方塊中輸入「使用者和群組」，然後選取 **Enter**。

    ![media/image1.png](media/manage-personal-data-azure/image001.png)

3. 在 [使用者和群組] 刀鋒視窗上，選取 [使用者]。

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. 在 [使用者和群組 - 使用者] 刀鋒視窗中，從清單選取使用者，然後在所選取使用者的刀鋒視窗上，選取 [設定檔] 以檢視必須更正或更新的使用者設定檔資訊。

    ![media/image3.png](media/manage-personal-data-azure/image005.png)

5. 更正或更新資訊，然後在命令列中選取 [儲存]。

6.  在所選取使用者的刀鋒視窗上，選取 [工作資訊] 來檢視必須更正或更新的使用者工作資訊。

    ![media/image4.png](media/manage-personal-data-azure/image007.png)

7. 更正或更新使用者工作資訊，然後在命令列中選取 [儲存]。

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>如何在 Azure Active Directory 中刪除使用者設定檔？

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。

2. 選取 [更多服務]，在文字方塊中輸入「使用者和群組」，然後選取 **Enter**。

    ![](media/manage-personal-data-azure/image001.png)

3. 在 [使用者和群組] 刀鋒視窗上，選取 [使用者]。

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. 在 [使用者和群組 - 使用者]  刀鋒視窗上，從清單中選取一個使用者。

    ![media/image3.png](media/manage-personal-data-azure/image007.png)

5. 在所選使用者的刀鋒視窗上選取 [概觀]，然後在命令列中選取 [刪除]。

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>SQL Database：改正/更正不正確或不完整的個人資料；清除/刪除個人資料；匯出個人資料 

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 是雲端資料庫，可協助開發人員建置及維護應用程式。

您可以使用標準 SQL 查詢在 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 中更新個人資料，也可將其刪除。 此外，個人資料可透過各種方法 (如 Azure SQL Server 匯入與匯出精靈) 及各種格式 (如 BACPAC 檔案) 從 SQL Database 匯出。

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>如何更正、更新或清除 SQL Database 中的個人資料？

若要了解如何更正或更新 SQL Database 中的個人資料，請造訪[更新 (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) (英文)、[更新文字](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql) (英文)、[使用通用資料表運算式更新](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql) (英文) 或[更新寫入文字](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql) (英文) 文件。

若要深入了解如何刪除 SQL Database 中的個人資料，請造訪[刪除 (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) (英文) 文件。

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>如何在 SQL Database 中將個人資料匯出至 BACPAC 檔案？

BACPAC 檔案包含 SQL Database 資料和中繼資料，是副檔名為 BACPAC 的 zip 檔案。 這可使用 [Azure 入口網站](https://portal.azure.com/)、SQLPackage 命令列公用程式、SQL Server Management Studio (SSMS) 或 PowerShell 完成。

若要了解如何將資料匯出至 BACPAC 檔案，請造訪[將 Azure SQL 資料庫匯出到 BACPAC 檔案](https://docs.microsoft.com/azure/sql-database/sql-database-export)頁面，其中包括上述每種方法的詳細指示。

如何使用 SQL Server 匯入與匯出精靈從 SQL Database 匯出個人資料？

此精靈可以幫助您將來源的資料複製到目的地。 如需精靈的簡介，包括取得方式、權限資訊，以及如何取得此工具的說明，請造訪[使用 SQL Server 匯入和匯出精靈來匯入或匯出資料](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard)網頁。

如需精靈步驟的概觀，請造訪 [SQL Server 匯入和匯出精靈步驟](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard) (英文) 網頁。

## <a name="next-steps"></a>後續步驟：

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

