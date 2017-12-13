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
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: d8257056fddda408b622d3da11c707ff39e180db
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2017
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>關於 Azure Databricks 的常見問題

本文列出關於 Azure Databricks 的最常見疑問。 其中也會列出您在使用 Databricks 時可能遇到的一些常見問題。 如需詳細資訊，請參閱[何謂 Azure Databricks](what-is-azure-databricks.md)。 

## <a name="can-i-use-my-own-keys-for-local-encryption"></a>我可以使用自己的金鑰進行本機加密嗎？ 
在目前版本中，不支援您從 Azure Key Vault 使用自己的金鑰。 

## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>我可以將 Azure 虛擬網路與 Databricks 搭配使用嗎？
新的虛擬網路會在 Azure Databricks 佈建期間建立。 在這個版本中，您無法使用自己的 Azure 虛擬網路。

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>我如何從筆記本中存取 Azure Data Lake Store？ 

請遵循下列步驟：
1. 在 Azure Active Directory (Azure AD) 中，佈建服務主體並記錄其金鑰。
2. 在 Data Lake Store 中，將必要權限指派給服務主體。
3. 若要存取 Data Lake Store 中的檔案，請在 Notebook 中使用服務主體認證。

如需詳細資訊，請參閱[搭配 Azure Databricks 使用 Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store)。

## <a name="fix-common-problems"></a>修正常見問題

以下是您使用 Databricks 時可能遇到的一些問題。

### <a name="this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>此訂用帳戶未註冊為可以使用命名空間 ‘Microsoft.Databricks’

#### <a name="error-message"></a>錯誤訊息

「此訂用帳戶未註冊為可以使用命名空間 'Microsoft.Databricks'。 如需了解如何註冊訂用帳戶，請參閱 https://aka.ms/rps-not-found。 (錯誤碼：MissingSubscriptionRegistration)」

#### <a name="solution"></a>方案

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 依序選取 [訂用帳戶]、您所使用的訂用帳戶及 [資源提供者]。 
3. 在資源提供者清單中，針對 **Microsoft.Databricks** 選取 [註冊]。 您在訂用帳戶中必須具有參與者或擁有者角色，才能註冊資源提供者。


### <a name="your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>您的帳戶 {email} 在 Azure 入口網站的 Databricks 工作區資源中沒有擁有者或參與者角色

#### <a name="error-message"></a>錯誤訊息

「您的帳戶 {email} 在 Azure 入口網站的 Databricks 工作區資源中沒有「擁有者」或「參與者」角色。 如果您是租用戶中的來賓使用者，也會發生此錯誤。 請向系統管理員申請授予存取權，或將您直接新增為 Databricks 工作區中的使用者。」 

#### <a name="solution"></a>方案

以下是此問題的幾種解決方案：

* 若要將租用戶初始化，您必須以租用戶的一般使用者身分登入，而不是以來賓使用者身分登入。 您在 Databricks 工作區資源上也必須具有參與者角色。 您可以在 Azure 入口網站的 Databricks 工作區中，從 [存取控制 (IAM)] 索引標籤來授與使用者存取權。

* 如果您的電子郵件網域名稱指派給 Azure AD 中的多個目錄，也可能發生此錯誤。 若要避開此問題，請使用 Databricks 工作區，在包含訂用帳戶的目錄中建立新的使用者。

    a. 在 Azure 入口網站中，移至 Azure AD。 選取 [使用者與群組] > [新增使用者]。

    b. 以 `@<tenant_name>.onmicrosoft.com` 電子郵件 (而非 `@<your_domain>` 電子郵件) 來新增使用者。 您可以在 Azure 入口網站中 Azure AD 下方的[自訂網域] 中找到此項。
    
    c. 在 Databricks 工作區資源上，為這位新的使用者授與**參與者**角色。
    
    d. 以新的使用者身分登入 Azure 入口網站，並尋找 Databricks 工作區。
    
    e. 以此使用者的身分啟動 Databricks 工作區。


### <a name="your-account-email-has-not-been-registered-in-databricks"></a>您的帳戶 {email} 並未在 Databricks 中註冊 

#### <a name="solution"></a>方案

如果您並未建立工作區，但已新增為使用者，請連絡建立該工作區的人員。 請該人員使用 Azure Databricks 管理主控台來將您新增。 如需指示，請參閱[新增和管理使用者](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html)。 如果您建立了工作區，但仍然收到此錯誤，請再次嘗試從 Azure 入口網站選取 [初始化工作區]。

### <a name="cloud-provider-launch-failure-while-setting-up-the-cluster"></a>設定叢集時的雲端提供者啟動失敗

#### <a name="error-message"></a>錯誤訊息

「雲端提供者啟動失敗：設定叢集時發生雲端提供者錯誤。 請參閱 Databricks 指南以取得詳細資訊。 Azure 錯誤碼：PublicIPCountLimitReached。 Azure 錯誤訊息：無法為此區域的此訂用帳戶建立超過 60 個以上的公用 IP 位址。」

#### <a name="solution"></a>方案

Databricks 叢集會在每個節點上使用一個公用 IP 位址。 如果您的訂用帳戶已經使用其所有公用 IP，您應該[要求增加配額](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request)。 選擇 [配額] 作為 [問題類型]，並選擇 [網路：ARM] 作為 [配額類型]。 在 [詳細資料] 中，申請提高公用 IP 位址配額。 例如，如果您目前的限制是 60，而您想要建立具有 100 個節點的叢集，請申請將限制提高到 160。

### <a name="a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster"></a>設定叢集時的第二種類型雲端提供者啟動失敗

#### <a name="error-message"></a>錯誤訊息

「雲端提供者啟動失敗：設定叢集時發生雲端提供者錯誤。 請參閱 Databricks 指南以取得詳細資訊。
Azure 錯誤碼：MissingSubscriptionRegistration Azure 錯誤訊息：訂用帳戶未註冊為使用命名空間 'Microsoft.Compute'。 如需了解如何註冊訂用帳戶，請參閱 https://aka.ms/rps-not-found。」

#### <a name="solution"></a>方案

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 依序選取 [訂用帳戶]、您所使用的訂用帳戶及 [資源提供者]。 
3. 在資源提供者清單中，針對 **Microsoft.Compute** 選取 [註冊]。 您在訂用帳戶中必須具有參與者或擁有者角色，才能註冊資源提供者。

如需更多詳細指示，請參閱[資源提供者和類型](../azure-resource-manager/resource-manager-supported-services.md)。

## <a name="next-steps"></a>後續步驟

- [快速入門：開始使用 Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [何謂 Azure Databricks？](what-is-azure-databricks.md)

