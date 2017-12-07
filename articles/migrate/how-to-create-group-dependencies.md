---
title: "在 Azure Migrate 中使用群組相依性對應調整評量群組 | Microsoft Docs"
description: "說明如何在 Azure Migrate 服務中使用群組相依性對應調整評量。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: b4d6861f147fbb6e65a9d529f17f78b54075eb90
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2017
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>使用群組相依性對應調整群組

本文說明如何為 [Azure Migrate](migrate-overview.md) 評量設定群組相依性對應。 當想要為現有群組調整設定時，通常會使用此方法，先交叉檢查群組相依性，然後再進行評量。 要執行群組相依性對應的群組所含的機器數量不應超過 10 台。  

## <a name="modify-a-group"></a>修改群組

1. 在 Azure Migrate 專案中的 [管理] 下方，按一下 [群組] ****，然後選取群組。
2. 在群組頁面中，按一下 [檢視相依性] ****，即會開啟群組相依性對應。 

     ![檢視群組](./media/how-to-create-group-dependencies/create-group.png)

3. 若要檢視更細微的相依性，請按一下時間範圍以進行修改。 根據預設，範圍是一小時。 您可以修改時間範圍，或指定開始和結束日期，以及持續時間。
4. 使用 CTRL+按一下滑鼠左鍵，以選取對應的機器。 新增機器或將機器從對應移除。
    - 您只能新增已探索到的機器。
    - 新增和移除群組中的機器會讓先前所做的評量失效。
    - 修改群組時，可以選擇性地建立新評量。
5. 按一下 [確定] 以儲存群組。

    ![新增和移除](./media/how-to-create-group-dependencies/add-remove.png)

如果您要檢查群組相依性對應中顯示之特定機器的相依性，[請設定機器相依性對應](how-to-create-group-machine-dependencies.md)。


## <a name="next-steps"></a>後續步驟

[深入了解](concepts-assessment-calculation.md)評定的計算方式。
