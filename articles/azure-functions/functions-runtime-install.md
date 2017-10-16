---
title: "Azure Functions 執行階段安裝 | Microsoft Docs"
description: "如何安裝 Azure Functions 執行階段"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: 1e4188313a87d07f396e5f8edc8969dd5da2c436
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>安裝 Azure Functions 執行階段預覽

如果您想要安裝 Azure Functions 執行階段預覽，您必須遵循下列步驟︰

1. 請確定您的電腦符合最低需求
1. 下載 [Azure Functions 執行階段預覽安裝程式](https://aka.ms/azafr)。 
1. 安裝 Azure Functions 執行階段預覽
1. 完成 Azure Functions 執行階段預覽的設定

## <a name="prerequisites"></a>必要條件

安裝 Azure Functions 執行階段預覽之前，您必須備妥下列各項：

1. 執行 Microsoft Windows Server 2016 或 Microsoft Windows 10 Creators Update (Professional 或 Enterprise Edition) 的電腦。
1. 在您的網路內執行的 SQL Server 執行個體。  最小版本需求是 SQL Server Express。

## <a name="install-the-azure-functions-runtime-preview"></a>安裝 Azure Functions 執行階段預覽

Azure Functions 執行階段預覽安裝程式會引導您完成安裝 Azure Functions 執行階段預覽版的管理角色和背景工作角色。  管理角色和背景工作角色可以安裝在同一部電腦上。  不過，當您新增更多 Functions 時，您必須將更多背景工作角色部署在其他電腦上，才能將您的函式擴展至多個背景工作角色。

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>在同一部電腦上安裝管理角色和背景工作角色

1. 執行 Azure Functions 執行階段預覽安裝程式。

    ![Azure Functions 執行階段預覽安裝程式][1]

1. 按 [下一步] 跳過安裝程式的第一個階段
1. 閱讀 **EULA** 的條款之後，**勾選方塊接受條款**，然後按 [下一步] 前進。
1. 現在，選取您想要在這部電腦上安裝的角色：**Functions 管理角色**及/或 **Functions 背景工作角色**，然後按 [下一步]

    ![Azure Functions 執行階段預覽安裝程式 - 選取角色][3]

    > [!NOTE]
    > 您可以在其他許多電腦上安裝 **Functions 背景工作角色**，若要這樣做，請遵循這些指示，在安裝程式中只選取 [Functions 背景工作角色]。

1. 按 [下一步]，將**Azure Functions 執行階段安裝程式**安裝在電腦上。
1. 完成後，安裝程式將會啟動 **Azure Functions 執行階段設定工具**。

    ![Azure Functions 執行階段預覽安裝程式完成][5]

    > [!NOTE]
    > 如果您要安裝在 **Windows 10**，但先前未啟用 [容器] 功能，**Azure Functions 執行階段** 安裝程式會提示您重新開機才能完成安裝。

## <a name="configure-the-azure-functions-runtime"></a>設定 Azure Functions 執行階段

若要完成 Azure Functions 執行階段安裝，您必須完成設定。

1. **Azure Functions 執行階段設定工具**會顯示您的電腦上安裝哪些角色。

    ![Azure Functions 執行階段預覽設定工具][6]

1. 按一下 [資料庫] 索引標籤，輸入 SQL Server 執行個體的連線詳細資料，然後按一下 [套用]。  需要如此，Azure Functions 執行階段才能建立資料庫以支援執行階段。
    
    ![Azure Functions 執行階段預覽資料庫設定][7]

1. 按一下 [認證] 索引標籤。在此畫面上，您必須建立兩個新的認證，以使用 FileShare 來裝載您的所有 Azure Functions。  在 [檔案共用擁有者] 和 [檔案共用使用者] 中，指定**使用者名稱和密碼**組合，然後按一下 [套用]。

    ![Azure Functions 執行階段預覽認證][8]

1. 按一下 [檔案共用] 索引標籤。在此畫面中，您必須指定 [檔案共用位置] 的詳細資料。  您可以建立此項目，也可以使用現有的檔案共用，然後按一下 [套用]。  如果您選取新的檔案共用位置，您必須指定供 Azure Functions 執行階段使用的目錄。
    
    ![Azure Functions 執行階段預覽檔案共用][9]

1. 按一下 [IIS] 索引標籤。此索引標籤會顯示 IIS 中將建立 Azure Functions 執行階段安裝之網站的詳細資訊。  按一下 [套用] 完成。

    ![Azure Functions 執行階段預覽 IIS][10]

1. 按一下 [服務] 索引標籤。此索引標籤會顯示 Azure Functions 執行階段安裝中的服務狀態。  初始設定之後，如果 **Azure Functions 主機啟用服務**未執行，請按一下 [啟動服務]

    ![Azure Functions 執行階段預覽設定完成][11]

1. 最後，瀏覽至 **Azure Functions 執行階段入口網站**：`https://<machinename>/`

    ![Azure Functions 執行階段預覽入口網站][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png