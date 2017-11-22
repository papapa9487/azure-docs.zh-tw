---
title: "Azure Databricks：常見問題與說明 | Microsoft Docs"
description: "取得有關 Azure Databricks 的常見問題解答和疑難排解資訊。"
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: nitinme
ms.openlocfilehash: b6b001087cba5f8550d4fea3e4a2f7c1c865beae
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Azure Databricks 預覽：常見問題與說明

本文會列出關於 Azure Databricks 的最常見疑問。 也會列出使用 Azure Databricks 時可能會遇到的一些常見問題。 如需 Azure Databricks 的詳細資訊，請參閱[何謂 Azure Databricks？](what-is-azure-databricks.md) 

## <a name="common-questions"></a>常見問題

本區段會列出關於 Azure Databricks 的常見問題。

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>我可以使用自己的金鑰進行本機加密嗎？ 
在目前版本中，不支援您從 Azure Keyvault 使用自己的金鑰。 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>可以搭配使用 Azure VNET 與 Azure Databricks 嗎？
新的 VNET 會建立為 Azure Databricks 佈建的一部分。 作為此版本的一部分，您無法使用 Azure VNET。

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>我如何從筆記本中存取 Azure Data Lake Store？ 

1. 在 Azure Active Directory 中，佈建服務主體並記錄其金鑰。
2. 在 Azure Data Lake Store 中指派必要權限給服務主體。
3. 若要存取 Azure Data Lake Store 中的檔案，請在筆記本中使用服務主體認證。

如需詳細資訊，請參閱[搭配 Azure Databricks 使用 Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store)。

## <a name="troubleshooting"></a>疑難排解

本區段描述如何針對 Azure Databricks 的常見問題進行移難排解。

### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>問題：您的帳戶 {email} 在 Azure 入口網站的 Databricks 工作區資源中沒有「擁有者」或「參與者」角色。

**錯誤訊息**

您的帳戶 {email} 在 Azure 入口網站的 Databricks 工作區資源中沒有「擁有者」或「參與者」角色。 如果您是租用戶中的來賓使用者，也會發生此錯誤。 請要求系統管理員授與您存取權，或將您直接新增為 Databricks 工作區中的使用者。 

**方案**

若要初始化租用戶，您必須以租用戶的一般使用者身分登入，而不是使用來賓使用者。 您在 Databricks 工作區資源上也必須要有「參與者」角色。 您可以在 Azure 入口網站的 Azure Databricks 工作區中，從 [存取控制 (IAM)] 索引標籤中授與使用者存取權。

### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>問題：您的帳戶 {email} 未在 Databricks 中登錄 

**方案**

如果您未建立工作區，而您已新增為工作區的使用者，請連絡工作區建立者，讓他使用 Azure Databricks 主控台來新增您的帳戶。 如需指示，請參閱[新增和管理使用者](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html)。 如果您已建立工作區，而您仍然收到此錯誤，請從 Azure 入口網站再嘗試按一下 [初始化工作區]。

### <a name="issue-cloud-provider-launch-failure-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>問題：雲端提供者啟動失敗：設定叢集時發生雲端提供者錯誤。

**錯誤訊息**

雲端提供者啟動失敗：設定叢集時發生雲端提供者錯誤。 請參閱 Databricks 指南以取得詳細資訊。 Azure 錯誤碼：PublicIPCountLimitReached。 Azure 錯誤訊息：無法在此區域中，為此訂用帳戶建立 60 個以上的公用 IP 位址。

**方案**

Azure Databricks 叢集會在每個節點上使用一個公用 IP 位址。 如果您的訂用帳戶已經使用其所有公用 IP，您應該[要求增加配額](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request)。 選擇 [配額] 作為 [問題類型]、[網路 ARM] 作為 [配額類型]，然後在 [詳細資料]中要求增加公用 IP 位址配額 (例如，如果您限制目前是 60，而您想要建立一個有 100 個節點的叢集，則要求提高限制到 160)。

## <a name="next-steps"></a>後續步驟
如需建立 Data Factory 第 2 版的逐步指示，請參閱下列教學課程：

- [快速入門：開始使用 Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [何謂 Azure Databricks？](what-is-azure-databricks.md)

