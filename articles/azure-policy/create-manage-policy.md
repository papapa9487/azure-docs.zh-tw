---
title: "使用 Azure 原則來建立和管理原則以強制執行組織相容性 | Microsoft Docs"
description: "使用 Azure 原則來強制執行標準、符合法規合規性與稽核需求、控制成本、維護安全性與效能一致性，並且強制整個企業的設計原則。"
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 55e5a60294fc5ccb2a55b1e572af2fd27c68f462
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>建立和管理原則來強制執行相容性

了解如何在 Azure 中建立和管理原則，對於保持符合公司標準和服務等級協定而言，相當重要。 在本教學課程中，您將學習使用 Azure 原則在整個組織執行與建立、指派及管理原則相關的某些常見工作，例如：

> [!div class="checklist"]
> * 指派原則來強制執行您在未來建立的資源條件
> * 建立及指派計畫定義，以追蹤多個資源的相容性
> * 解決不相容或拒絕的資源
> * 在整個組織中實作新的原則

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="opt-in-to-azure-policy"></a>加入 Azure 原則

Azure 原則目前僅供有限預覽，因此您必須註冊以要求存取權。

1. 移至 Azure 原則，位置是：https://aka.ms/getpolicy，然後選取左窗格中的 [註冊]。

   ![搜尋原則](media/assign-policy-definition/sign-up.png)

2. 藉由選取您想要使用之 [訂用帳戶] 清單中的訂用帳戶，加入 Azure 原則。 接著，選取 [註冊]。

   您的訂用帳戶清單包含您的所有 Azure 訂用帳戶。

   ![加入以使用 Azure 原則](media/assign-policy-definition/preview-opt-in.png)

   根據需求，我們可能需要數天的時間接受您的註冊要求。 一旦您的要求被接受，系統會透過電子郵件通知您，您可以開始使用服務。

## <a name="assign-a-policy"></a>指派原則

使用 Azure 原則強制執行相容性的第一步是指派原則定義。 原則定義會定義在何種條件下強制執行原則，以及要採取什麼動作。 在此範例中，我們會指派稱為「需要 SQL Server 12.0 版」的內建原則定義，以強制執行所有 SQL Server 資料庫都必須是 v12.0 才能相容的條件。

1. 藉由搜尋並選取左窗格中的 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

   ![搜尋原則](media/assign-policy-definition/search-policy.png)

2. 選取 Azure 原則分頁左窗格上的 [指派]。 指派是已指派在特定範圍內發生的原則。
3. 從 [指派] 窗格頂端選取 [指派原則]。

   ![指派原則定義](media/create-manage-policy/select-assign-policy.png)

4. 在 [指派原則] 分頁上，按一下 [原則] 欄位旁的![原則定義按鈕](media/assign-policy-definition/definitions-button.png)，以開啟可用定義的清單。

   ![開啟可用原則定義](media/create-manage-policy/open-policy-definitions.png)

5. 選取 [需要 SQL Server 12.0 版]。
   
   ![找出原則](media/create-manage-policy/select-available-definition.png)

6. 為原則指派提供顯示**名稱**。 在此情況下，讓我們使用「需要 SQL Server 12.0 版」。 您也可以新增選擇性的 [描述] 。 描述會提供有關此原則指派如何確保在此環境中建立的所有 SQL Server 都是 12.0 版的詳細資料。
7. 將定價層變更為**標準**，以確保原則套用至現有的資源。

   Azure 原則有兩個定價層 – 免費和標準。 使用免費層次，您只能在未來的資源上強制執行原則，而使用標準層，您也能在現有資源上強制執行這些原則，以更加了解相容性狀態。 因為還是有限預覽版本，所以尚未發行計價模式，因此您選取「標準」也不會收到帳單。 若要深入了解定價，請參閱 [Azure 原則定價](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/)。

8. 選取**範圍** - 您先前加入 Azure 原則時註冊的訂用帳戶 (或資源群組)。 範圍會決定在哪些資源或資源群組上強制執行原則指派。 範圍從訂用帳戶到資源群組。

   我們針對此範例使用這個訂用帳戶 - **Azure Analytics Capacity Dev**。 您的訂用帳戶不同。

10. 選取 [指派]。

## <a name="implement-a-new-custom-policy"></a>實作新的自訂原則

既然我們已指派原則定義，我們將建立新的原則，藉由確保在您的環境中建立的 VM 不是 G 系列，來節省成本。 如此一來，每次您組織中的使用者嘗試建立 G 系列的 VM 時，要求就會被拒絕。

1. 選取左窗格中 [製作] 底下的 [定義]。

   ![定義正在製作中](media/create-manage-policy/definition-under-authoring.png)

2. 選取 [+ 原則定義]。
3. 輸入以下資訊：

   - 原則定義的名稱 - 需要小於 G 系列的 VM SKU
   - 原則定義目的的描述 – 此原則定義會強制讓此範圍中建立的所有 VM，具有比 G 系列小的 SKU，以降低成本。
   - 原則定義所在的訂用帳戶 – 在此情況下，我們的原則定義將會存放在 **Advisor Analytics Capacity Dev**。 您的訂用帳戶清單不同。
   - 使用下列項目寫入 json 程式碼：
      - 原則參數。
      - 原則規則/條件，在此情況下 – VM SKU 大小等於 G 系列
      - 原則效果，在此情況下 – **拒絕**。
   
   以下是 json 應該會有的外觀

```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/sku.name",
            "like": "Standard_G*"
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
}
```

   若要檢視 json 程式碼的範例，請參閱這篇文章 - [資源原則概觀](../azure-resource-manager/resource-manager-policy.md)
   
4. 選取 [ **儲存**]。

## <a name="create-and-assign-an-initiative-definition"></a>建立並指派計畫定義

使用計畫定義，您可以群組數個原則定義，以達成一個總體目標。 您建立計畫定義，以確保定義範圍內的資源保持與組成計畫定義的原則定義相容。  請參閱 [Azure 原則概觀](./azure-policy-introduction.md)，以取得計畫定義的詳細資訊。

### <a name="create-an-initiative-definition"></a>建立計畫定義

1. 選取左窗格中 [製作] 底下的 [定義]。

   ![選取定義](media/create-manage-policy/select-definitions.png)

2. 選取分頁頂端的 [計畫定義]，這個選項會帶您前往 [計畫定義] 表單。
3. 輸入計畫的名稱和說明。

   在此範例中，我們想要確保資源都符合保障安全的原則定義，計畫的名稱會是**保障安全**，描述是：**已經建立計畫以處理與保護資源安全相關聯的所有原則定義**。

   ![計畫定義](media/create-manage-policy/initiative-definition.png)

4. 瀏覽 [可用定義] 的清單，並選取您想要新增至該計畫的原則定義。 針對我們的**保障安全**計畫，新增下列內建原則定義：
   - 需要 SQL Server 12.0 版
   - 監視資訊安全中心未受保護的 Web 應用程式。
   - 監視整個資訊安全中心的寬鬆網路。
   - 監視資訊安全中心的可能應用程式白名單。
   - 監視資訊安全中心的未加密 VM 磁碟。

   ![計畫定義](media/create-manage-policy/initiative-definition-2.png)

   從清單中選取原則定義之後，您會在 [原則和參數] 底下看到它，如上所示。

5. 選取 [ **建立**]。

### <a name="assign-an-initiative-definition"></a>指派計畫定義

1. 移至 [製作] 底下的 [定義] 索引標籤。
2. 搜尋您建立的**保障安全**計畫定義。
3. 選取計畫定義，然後選取 [指派]。

   ![指派定義](media/create-manage-policy/assign-definition.png)

4. 填寫 [指派] 表單，輸入：
   - 名稱：保障安全指派
   - 描述：此計畫指派適合在 **Azure Advisor Capacity Dev** 訂用帳戶中強制執行此原則定義群組
   - 定價層：標準
   - 您想要套用此指派的範圍：**Azure Advisor Capacity Dev**

5. 選取 [指派]。 

## <a name="resolve-a-non-compliant-or-denied-resource"></a>解決不相容或拒絕的資源

遵循上述的範例，在指派原則定義需要 SQL Server 12.0 版之後，使用不同版本建立的 SQL Server 就會遭到拒絕。 在本節中，我們會逐步解決建立不同版本 SQL Server 的被拒絕嘗試。

1. 選取左窗格中的 [指派]。
2. 瀏覽所有原則指派，並啟動 [需要 SQL Server 12.0 版] 指派。
3. 要求排除您嘗試建立 SQL Server 的資源群組。 在此情況下，我們會排除 Microsoft.Sql/servers/databases：baconandbeer/Cheetos 和 baconandbeer/Chorizo。

   ![要求排除](media/create-manage-policy/request-exclusion.png)

   您解決被拒絕資源的其他方法包括：如果您有強烈的理由需要建立的 SQL Server，則洽詢與原則相關聯的連絡人，如果您有存取權就直接編輯原則。

4. 選取 [ **儲存**]。

在本節中，您藉由要求排除資源，來解決拒絕您嘗試建立 SQL Server 12.0 版。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續進行後續的教學課程，請勿清除在此指南中建立的資源。 如果您不打算繼續，請使用下列步驟來刪除前面建立的任何指派或定義：

1. 選取左窗格中的 [定義] (或 [指派]，如果您嘗試刪除指派)。
2. 搜尋您剛剛建立的新計畫或原則定義 (或指派)。
3. 選取定義或指派結尾的省略符號，然後選取 [刪除定義] (或 [刪除指派])。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已成功完成下列項目：

> [!div class="checklist"]
> * 指派原則來強制執行您在未來建立的資源條件
> * 建立及指派計畫定義，以追蹤多個資源的相容性
> * 解決不相容或拒絕的資源
> * 在整個組織中實作新的原則

若要深入了解原則定義的結構，請閱讀這篇文章：

> [!div class="nextstepaction"]
> [原則定義結構](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure)
