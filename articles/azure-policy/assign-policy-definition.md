---
title: "建立原則指派，以識別 Azure 環境中的不相容資源 | Microsoft Docs"
description: "這篇文章會引導您逐步完成建立原則定義來識別不相容的資源。"
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 85136ff2783b21472ef02aee15f8ec5844a00c12
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>建立原則指派，以識別 Azure 環境中的不相容資源
了解 Azure 中相容性的第一個步驟是了解您與自己的目前資源所處的位置。 本快速入門會逐步引導您完成程序來建立原則指派，以識別出未使用受控磁碟的虛擬機器。

在此程序結束時，您將已成功識別出因未使用受控磁碟而「不符合規範」的虛擬機器。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="opt-in-to-azure-policy"></a>加入 Azure 原則

「Azure 原則」目前是以「公開預覽」的形式提供，您必須註冊才能要求存取。

1. 移至 Azure 原則，位置是：https://aka.ms/getpolicy ，然後選取左窗格中的 [註冊]。

   ![搜尋原則](media/assign-policy-definition/sign-up.png)

2. 藉由選取您想要使用之 [訂用帳戶] 清單中的訂用帳戶，加入 Azure 原則。 接著，選取 [註冊]。

   ![加入以使用 Azure 原則](media/assign-policy-definition/preview-opt-in.png)

   針對「預覽版」，系統會自動核准您的要求。 請等候 30 分鐘讓系統處理您的註冊。

## <a name="create-a-policy-assignment"></a>建立原則指派

在本快速入門中，我們會建立一個原則指派，並且指派 *Audit Virtual Machines without Managed Disks* (稽核沒有受控磁碟的虛擬機器) 原則定義。

1. 選取 Azure 原則分頁左窗格上的 [指派]。
2. 從 [指派] 窗格頂端選取 [指派原則]。

   ![指派原則定義](media/assign-policy-definition/select-assign-policy.png)

3. 在 [指派原則] 分頁上，按一下 [原則] 欄位旁的![原則定義按鈕](media/assign-policy-definition/definitions-button.png)，以開啟可用定義的清單。

   ![開啟可用原則定義](media/assign-policy-definition/open-policy-definitions.png)

   Azure 原則隨附您可以使用的內建原則定義。 內建原則定義如下：

   - 強制執行標籤和其值
   - 套用標籤和其值
   - 需要 SQL Server 12.0 版

4. 搜尋您的原則定義以尋找 *Audit VMs that do not use managed disks* (稽核未使用受控磁碟的 VM) 定義。 按一下該原則，然後按一下 [指派]。

   ![尋找正確的原則定義](media/assign-policy-definition/select-available-definition.png)

5. 為原則指派提供顯示**名稱**。 在此案例中，我們將使用 *Audit VMs that do not use managed disks* (稽核未使用受控磁碟的 VM)。 您也可以新增選擇性的 [描述]。 此描述可用來提供詳細資料，說明此原則指派如何識別出在此環境中建立且未使用受控磁碟的所有虛擬機器。
6. 將定價層變更為**標準**，以確保原則套用至現有的資源。

   Azure 原則有兩個定價層 – 免費和標準。 使用免費層次，您只能在未來的資源上強制執行原則，而使用標準層，您也能在現有資源上強制執行這些原則，以更加了解相容性狀態。 因為還是有限預覽版本，所以尚未發行計價模式，因此您選取「標準」也不會收到帳單。 若要深入了解定價，請參閱 [Azure 原則定價](https://azure.microsoft.com/pricing/details/azure-policy/)。

7. 選取您想要套用原則的 [範圍]。  範圍會決定在哪些資源或資源群組上強制執行原則指派。 範圍從訂用帳戶到資源群組。
8. 選取您先前加入 Azure 原則時註冊的訂用帳戶 (或資源群組)。 在此範例中，我們使用這個訂用帳戶 - **Azure Analytics Capacity Dev**，但是您的選項會有不同。

   ![尋找正確的原則定義](media/assign-policy-definition/assign-policy.png)

9. 選取 [指派]。

您現在可以識別不相容的資源，以了解環境的相容性狀態。

## <a name="identify-non-compliant-resources"></a>識別不相容的資源

選取左窗格上的 [相容性]，然後搜尋您建立的原則指派。

![原則相容性](media/assign-policy-definition/policy-compliance.png)

如果有任何現有資源與這個新的指派不相容，它們會顯示在 [不相容資源] 索引標籤下。

如果跨現有資源評估條件，而且其中某些資源的結果為 true，則這些資源都會標示為與原則不相容。 下表列出現今我們可以用來處理條件評估結果與資源相容性狀態的動作有多麼不同。

|資源  |如果原則中的條件評估為  |原則中的動作   |相容性狀態  |
|-----------|---------|---------|---------|
|exists     |True     |拒絕     |不相容 |
|exists     |False    |拒絕     |相容     |
|exists     |True     |Append   |不相容 |
|exists     |False    |Append   |相容     |
|exists     |True     |稽核    |不相容 |
|exists     |False    |稽核    |不相容 |

## <a name="clean-up-resources"></a>清除資源

此集合中的其他指南是以本快速入門為基礎。 如果您打算繼續進行後續的教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源。
1. 選取左窗格中的 [指派]。
2. 搜尋您剛才建立的指派。

   ![刪除指派](media/assign-policy-definition/delete-assignment.png)

3.  選取 [刪除指派]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您可以將原則定義指派至範圍，以確保該範圍中的所有資源都是相容的，並且識別有哪些資源不相容。

若要深入了解指派原則，以確保所建立的**未來**資源是相容的，請繼續進行教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./create-manage-policy.md)
