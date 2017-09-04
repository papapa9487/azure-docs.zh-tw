---
title: "針對 Azure DevTest Labs VM 中的構件失敗進行診斷 | Microsoft Docs"
description: "了解如何針對 DevTest Labs 中的構件失敗進行疑難排解"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: e4f2946d0ba0756f36622aded0e8594acabb9527
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---
# <a name="diagnose-artifact-failures-in-the-lab"></a>針對實驗室中的構件失敗進行診斷 
建立構件之後，您可以檢查以確認其成功或失敗。 DevTest Labs 中的構件記錄提供資訊，可讓您用來診斷構件失敗。 您有幾種不同的方式，可以檢視 Windows VM 的構件記錄資訊。

> [!NOTE]
> 為了確保正確識別並說明失敗，請務必正確結構化構件。 如需如何正確建構構件的資訊，請參閱[建立自訂構件](devtest-lab-artifact-author.md)。 若要查看正確結構化的構件範例，請參閱此[測試參數類型](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)構件。

## <a name="troubleshoot-artifact-failures-using-the-azure-portal"></a>使用 Azure 入口網站為構件失敗進行疑難排解
若要使用 Azure 入口網站為構件建立期間的失敗進行診斷，請遵循下列步驟：

1. 從資源清單中選取您的實驗室。

2. 選擇 Windows VM，其中包含您要調查的構件。

3. 在左面板的 [一般] 下，選擇 [構件]。 與該 VM 建立關聯的構件清單隨即顯示，並指出構件的名稱及其狀態。

   ![構件 Git 儲存機制範例](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. 選擇顯示 [失敗] 狀態的構件。 此構件會隨即開啟並顯示延伸訊息，其中包含有關構件失敗的詳細資料。

   ![構件 Git 儲存機制範例](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-vm"></a>針對 VM 中的構件失敗進行疑難排解
若要檢視虛擬機器中的構件記錄，請遵循下列步驟：

1. 登入 VM，其中包含您要診斷的構件。

2. 巡覽至 C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.9\Status，其中 1.9 是 CSE 版本號碼。

   ![構件 Git 儲存機制範例](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. 開啟**狀態**檔案，以檢視可協助診斷該 VM 之成品失敗的資訊。




[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相關部落格文章
* [Join a VM to existing AD Domain using a resource manager template in Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (在 Azure DevTest Labs 中使用 Resource Manager 範本將 VM 加入至現有 AD 網域)

## <a name="next-steps"></a>後續步驟
* 了解如何[將 Git 存放庫新增至實驗室](devtest-lab-add-artifact-repo.md)。


