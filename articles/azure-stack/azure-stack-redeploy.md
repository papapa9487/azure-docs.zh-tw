---
title: "重新部署 Azure Stack | Microsoft Docs"
description: "重新部署 Azure Stack。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="redeploy-azure-stack"></a>重新部署 Azure Stack
若要重新部署 Azure Stack，您必須從頭開始進行，如下所述。

## <a name="steps-to-redeploy-azure-stack"></a>重新部署 Azure Stack 的步驟
1. 在開發套件主機上，開啟提升權限的 PowerShell 主控台 > 瀏覽至 asdk installer.ps1 指令碼 > 執行此程式碼 > 按一下 [重新開機]。
2. 選取基礎作業系統 (非 Azure Stack) 並按一下 [下一步]。
3. 開發套件主機重新開機之後，刪除先前部署過程中所使用的 CloudBuilder.vhdx 檔案。
4. [部署開發套件](azure-stack-run-powershell-script.md)。

## <a name="next-steps"></a>後續步驟
[連接至 Azure Stack](azure-stack-connect-azure-stack.md)


