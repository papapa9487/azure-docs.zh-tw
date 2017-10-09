---
title: "從 Azure 下載市集項目 | Microsoft Docs"
description: "我可以將市集項目從 Azure 下載到我的 Azure Stack 部署。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/27/2017

---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>將市集項目從 Azure 下載到 Azure Stack

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

當您決定要將哪些內容包含在 Azure Stack 市集中時，應該考慮可從 Azure Marketplace 取得的內容。 您可以從已預先測試而能在 Azure Stack 上執行的 Azure Marketplace 項目策劃清單下載。 新項目會不斷新增到此清單中，因此請務必回來查看是否有新內容。

若要下載市集項目，您必須先[向 Azure 註冊 Azure Stack](azure-stack-register.md)。 

## <a name="download"></a>下載
1. 登入 Azure Stack 系統管理員入口網站 (https://portal.local.azurestack.external)。
2. 有些市集項目可能非常龐大。  按一下 [資源提供者] > [儲存體] 來檢查，以確定您的系統上有足夠的空間。

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. 按一下 [更多服務] > [市集管理]。

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. 按一下 [從 Azure 新增] 以查看可供下載的項目清單。 您可以按一下清單中的每個項目來檢視其描述與下載大小。

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. 選取清單中您想要的項目，然後按一下 [下載]。 這會開始下載您所選項目的 VM 映像。 下載時間會有所不同。

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. 下載完成之後，您可以用 Azure Stack 操作員或使用者身分來部署新市集項目。 按一下 [+新增]，在類別中搜尋新市集項目，然後選取該項目。
7. 按一下 [建立] 以開啟新下載項目的建立體驗。 依照逐步指示來部署您的項目。

## <a name="next-steps"></a>後續步驟

[建立及發佈 Marketplace 項目](azure-stack-create-and-publish-marketplace-item.md)

