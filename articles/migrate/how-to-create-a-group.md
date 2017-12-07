---
title: "使用 Azure Migrate 分組機器以進行評量 | Microsoft Docs"
description: "說明如何在使用 Azure Migrate 服務進行評量之前先分組機器。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 966e0aebf56ab049e898d1787bfdfbb2ef23635e
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-for-assessment"></a>分組機器以進行評量

本文說明如何使用 [Azure Migrate](migrate-overview.md) 為評量建立機器群組。 Azure Migrate 會評估群組中的機器，檢查機器是否適合移轉至 Azure，並提供在 Azure 中執行機器的大小調整建議和成本估計。


## <a name="create-a-group"></a>建立群組

1. 在 Azure Migrate 專案的 [儀表板]，按一下 [群組] **** >[+群組] ****，並指定群組名稱。
2. 將一台或多台機器新增至群組，並按一下 [建立] ****。 
3. 您可以選擇性地選取此項目，為群組執行新的評量。 

    ![建立群組](./media/how-to-create-a-group/create-group.png)

建立群組之後，可以在 [群組] 頁面上選取群組加以修改，也可新增或移除機器。

## <a name="next-steps"></a>後續步驟

- 了解如何使用[機器相依性對應](how-to-create-group-machine-dependencies.md)來建立更詳細的群組。
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
