---
title: "管理 Azure-SSIS 整合執行階段 | Microsoft Docs"
description: "了解如何在佈建 Azure Data Factory 中的 Azure SSIS 整合執行階段後重新設定。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: spelluru
ms.openlocfilehash: cc0ed958a9e1018ed9f06fdcc94873ae5420ba95
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2017
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>管理 Azure-SSIS 整合執行階段
[建立 Azure SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)文章向您示範如何使用 Azure Data Factory 建立 Azure SSIS 整合執行階段。 本文補充該文，提供如何停止、啟動、重新設定或移除 Azure SSIS 整合執行階段的詳細資訊。  

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用 Data Factory 服務的 1 版 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-introduction.md)。

## <a name="stop"></a>停止 
停止 Azure-SSIS 整合執行階段。 此命令會釋出其所有節點並停止計費。

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="start"></a>Start 
啟動 Azure-SSIS 整合執行階段。 此命令會配置其所有節點並開始計費。   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="reconfigure"></a>重新設定
佈建並啟動 Azure SSIS 整合執行階段的執行個體之後，您可以執行一連串`Stop` - `Set` - `Start` PowerShell Cmdlet 來重新設定。 例如，下列 PowerShell 指令碼會將配置給 Azure SSIS 整合執行階段執行個體的節點數目變為 5。

1. 首先，執行下列命令來停止 Azure SSIS 整合執行階段：

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. 現在，將 Azure SSIS 整合執行階段相應放大為五個節點。

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. 接著啟動 Azure-SSIS 整合執行階段。 這會配置其所有節點以執行 SSIS 套件。   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="remove"></a>移除
若要移除現有的 Azure SSIS 整合執行階段，請執行下列命令： 

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
```

## <a name="next-steps"></a>後續步驟
如需 Azure-SSIS 執行階段的詳細資訊，請參閱下列主題： 

- [Azure-SSIS 整合執行階段](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供整合執行階段的一般概念性資訊，包括 Azure-SSIS IR。 
- [教學課程：將 SSIS 套件部署至 Azure](tutorial-deploy-ssis-packages-azure.md)。 本文逐步說明如何建立 Azure-SSIS IR，並使用 Azure SQL Database 裝載 SSIS 目錄。 
- [如何：建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)。 本文展開教學課程，並提供使用 Azure SQL 受管理執行個體 (私人預覽) 以及將 IR 加入 VNet 的指示。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 這篇文章會示範如何在傳回的資訊中擷取 Azure SSIS IR 的相關資訊和狀態的描述。 
- [將 VNet 加入至 Azure SSIS IR](join-azure-ssis-integration-runtime-virtual-network.md)。 這篇文章提供將 Azure SSIS IR 加入至 Azure 虛擬網路 (VNet) 的概念資訊。 它也提供使用 Azure 入口網站來設定 VNet，好讓 Azure SSIS IR 可加入 VNet 的步驟。 
