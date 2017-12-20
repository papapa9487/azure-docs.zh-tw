---
title: "在 Azure 入口網站中建立 Batch 帳戶 | Microsoft Docs"
description: "了解如何在 Azure 入口網站中建立 Azure Batch 帳戶，以在雲端中執行大規模的平行工作負載"
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebda2f11f93b04a5592d18f8e15c8fc3b560aac3
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>使用 Azure 入口網站建立 Batch 帳戶

> [!div class="op_single_selector"]
> * [Azure 入口網站](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
>
>

了解如何在 [Azure 入口網站][azure_portal]中建立 Azure Batch 帳戶，並選擇符合您計算案例的帳戶屬性。 了解如何尋找存取金鑰和帳戶 URL 等重要帳戶屬性。

如需 Batch 帳戶和案例的相關背景，請參閱[功能概觀](batch-api-basics.md)。



## <a name="create-a-batch-account"></a>建立批次帳戶：

> [!NOTE]
> 建立 Batch 帳戶時，您通常應該選擇預設的 **Batch 服務**模式，可指定在幕後將集區配置在 Azure 管理的訂用帳戶中。 在其他**使用者訂用帳戶**模式中 (大部分情節中不再建議使用) 建立集區時，Batch VM 和其他資源會直接建立在您的訂用帳戶中。 若要在使用者訂用帳戶模式中建立 Batch 帳戶，您也必須向 Azure Batch 註冊訂用帳戶，並與 Azure Key Vault 中的帳戶建立關聯。

1. 登入 [Azure 入口網站][azure_portal]。
2. 按一下 [新增]，並搜尋 Marketplace 取得 [Batch 服務]。

    ![Marketplace 中的批次][marketplace_portal]
3. 選取 [Batch 服務]，按一下 [建立]，並輸入 [新增 Batch 帳戶] 設定。 請參閱下列詳細資料。

    ![建立批次帳戶：][account_portal]

    a. **帳戶名稱**：您選擇的名稱必須是將建立帳戶的 Azure 區域內的唯一名稱 (請參閱下面的「位置」)。 帳戶名稱只能包含小寫字元或數字，且長度必須為 3-24 個字元。

    b.這是另一個 C# 主控台應用程式。 **訂用帳戶**：要在其中建立 Batch 帳戶的訂用帳戶。 如果您只有一個訂用帳戶，則預設會選取此項目。

    c. **集區配置模式**：如果出現這項設定，請接受預設的 [Batch 服務]。

    c. **資源群組**：為新的 Batch 帳戶選取現有的資源群組，或選擇性地建立一個新的資源群組。

    d. **位置**：要在其中建立 Batch 帳戶的 Azure 區域。 只有您的訂用帳戶和資源群組所支援的區域會顯示為選項。

    e. **儲存體帳戶** (選用)：與 Batch 帳戶相關聯的一般用途 Azure 儲存體帳戶。 這是大部分 Batch 帳戶的建議作法。 如需詳細資訊，請參閱本文稍後的[連結的 Azure 儲存體帳戶](#linked-azure-storage-account)。

4. 按一下 [建立]  來建立帳戶。



## <a name="view-batch-account-properties"></a>檢視 Batch 帳戶屬性
一旦建立帳戶後，按一下帳戶以存取其設定和屬性。 您可以使用左側功能表來存取所有的帳戶設定和屬性。

![Azure 入口網站中的 Batch 帳戶頁面][account_blade]

* **Batch 帳戶 URL**︰當您使用 [Batch API](batch-apis-tools.md#azure-accounts-for-batch-development) 開發應用程式時，需要帳戶 URL 才能存取 Batch 資源。 Batch 帳戶 URL 具有下列格式︰

    `https://<account_name>.<region>.batch.azure.com`

![入口網站中的 Batch 帳戶 URL][account_url]

* **存取金鑰**︰若要從您的應用程式驗證對 Batch 帳戶的存取權，可以使用帳戶存取金鑰。 (Batch 也支援 Azure Active Directory 驗證。)

    若要檢視或重新產生存取金鑰，請選取 [金鑰]。

    ![Azure 入口網站中的 Batch 帳戶金鑰][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>連結的 Azure 儲存體帳戶

您可以將一般用途的 Azure 儲存體帳戶連結至您的 Batch 帳戶，在許多情節都很有用。 Batch 的[應用程式套件](batch-application-packages.md)功能會使用 Azure Blob 儲存體，如同 [Batch 檔案慣例 .NET](batch-task-output.md) 程式庫所為。 這些選擇性功能可協助您部署您的 Batch 工作所執行的應用程式，並保存其所產生的資料。

我們建議建立 Batch 帳戶專用的新儲存體帳戶。 Azure Batch 目前僅支援一般用途的儲存體帳戶類型。 [關於 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)中的步驟 5 [儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)中會說明此帳戶類型。

![建立一般用途的儲存體帳戶][storage_account]

> [!NOTE]
> 重新產生已連結儲存體帳戶的存取金鑰時，請格外小心。 只重新產生單一儲存體帳戶金鑰，並按一下連結的儲存體帳戶頁面上的 [同步金鑰]。 等候 5 分鐘，讓金鑰傳播至您的集區中的計算節點，然後重新產生並同步處理其他金鑰 (如有必要)。 如果您同時重新產生這兩個金鑰，計算節點將無法同步處理任何一個金鑰，而且將無法存取儲存體帳戶。
>
>

![重新產生儲存體帳戶金鑰][4]

## <a name="additional-configuration-for-user-subscription-mode"></a>使用者訂用帳戶模式的其他組態

如果您選擇在使用者訂用帳戶模式中建立 Batch 帳戶，請在建立帳戶之前執行下列其他的步驟。

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>允許 Azure Batch 存取訂用帳戶 (一次性作業)
當您在使用者訂用帳戶模式中建立第一個 Batch 帳戶時，您必須向 Batch 註冊訂用帳戶。 (如果您先前已做過，請跳至下一節。)

1. 登入 [Azure 入口網站][azure_portal]。

2. 按一下 [更多服務] > [訂用帳戶]，然後按一下您想要用於 Batch 帳戶的訂用帳戶。

3. 在 [訂用帳戶] 頁面中，按一下 [存取控制 (IAM)] > [新增]。

    ![訂用帳戶存取控制][subscription_access]

4. 在 [新增權限] 頁面中，選取 [參與者] 角色，並搜尋 Batch API。 搜尋這些字串，直到您找到 API 為止：
    1. **MicrosoftAzureBatch**。
    2. **Microsoft Azure Batch**。 較新的 Azure AD 租用戶可以使用這個名稱。
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** 是 Batch API 的識別碼。 

5. 一旦您找到 Batch API 之後，請選取它，然後按一下 [儲存]。

    ![新增 Batch 權限][add_permission]

### <a name="create-a-key-vault"></a>建立金鑰保存庫
在使用者訂用帳戶模式中，需要與要建立之 Batch 帳戶屬於相同資源群組的 Azure Key Vault。 請確定資源群組位於 Batch 為[可用](https://azure.microsoft.com/regions/services/)且您訂用帳戶支援的區域中。

1. 在 [Azure 入口網站][azure_portal]中，按一下 [新增] > [安全性 + 身分識別] > [Key Vault]。

2. 在 [建立 Key Vault] 頁面中，輸入 Key Vault 的名稱，並在您需要的 Batch 帳戶區域中建立資源群組。 將其餘設定保留為預設值，然後按一下 [建立]。




## <a name="batch-service-quotas-and-limits"></a>Batch 服務配額和限制
如同您的 Azure 訂用帳戶與其他 Azure 服務，某些[配額和限制](batch-quota-limit.md) 會套用至 Batch 帳戶。 Batch 帳戶的目前配額會出現在 [配額] 中。

![Azure 入口網站中的 Batch 帳戶配額][quotas]



此外，透過在 Azure 入口網站中提交的免費產品支援要求，可增加其中多項配額。 如需要求配額加的詳細資料，請參閱 [Azure Batch 服務的配額和限制](batch-quota-limit.md) 。

## <a name="other-batch-account-management-options"></a>其他 Batch 帳戶管理選項
除了使用 Azure 入口網站以外，您可以使用下列各項建立及管理 Batch 帳戶︰

* [Batch PowerShell Cmdlet](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>後續步驟
* 若要深入了解 Batch 服務概念和功能，請參閱 [Batch 功能概觀](batch-api-basics.md) 。 本文討論主要 Batch 資源 (例如集區、計算節點、作業和工作)，並提供能夠進行大規模計算工作負載的服務功能概觀。
* 了解使用 [Batch .NET 用戶端程式庫](batch-dotnet-get-started.md)或 [Python](batch-python-tutorial.md) 開發啟用 Batch 之應用程式的基本概念。 這些簡介文章會介紹使用 Batch 服務在多個計算節點上執行工作負載的可行應用程式，並說明如何使用 Azure 儲存體進行工作負載檔案預備和擷取。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "重新產生儲存體帳戶金鑰"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png

