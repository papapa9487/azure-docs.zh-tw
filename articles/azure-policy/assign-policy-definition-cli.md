---
title: "使用 Azure CLI 建立原則指派，以識別 Azure 環境中的不相容資源 | Microsoft Docs"
description: "使用 PowerShell 建立 Azure 原則指派，以識別不相容資源。"
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 92b532691986e72eca68d9bc3033e20ff8ffef3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>使用 Azure CLI 建立原則指派，以識別 Azure 環境中的不相容資源

了解 Azure 中相容性的第一個步驟是了解您與目前資源所處的位置。 本快速入門步驟會引導您完成建立原則指派以使用原則定義識別不相容資源的程序 – 需要 SQL Server 12.0 版。 在此程序結束時，您就會成功識別哪些伺服器屬於不同版本，基本上不相容。

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本指南會詳細說明使用 Azure CLI 建立原則指派，以識別 Azure 環境中的不相容資源。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。
 
## <a name="opt-in-to-azure-policy"></a>加入 Azure 原則

Azure 原則目前僅供有限預覽，因此您必須註冊以要求存取權。

1. 移至 Azure 原則，位置是：https://aka.ms/getpolicy，然後選取左窗格中的 [註冊]。

   ![搜尋原則](media/assign-policy-definition/sign-up.png)

2. 藉由選取您想要使用之 [訂用帳戶] 清單中的訂用帳戶，加入 Azure 原則。 接著，選取 [註冊]。

   ![加入以使用 Azure 原則](media/assign-policy-definition/preview-opt-in.png)

   根據需求，我們可能需要數天的時間接受您的註冊要求。 一旦您的要求被接受，系統會透過電子郵件通知您，您可以開始使用服務。

## <a name="create-a-policy-assignment"></a>建立原則指派

在本快速入門中，我們會建立原則指派並且指派「需要 SQL Server 12.0 版」定義。 此原則定義會識別與原則定義中設定之條件不相容的資源。

依照下列步驟即可建立新的原則指派。

檢視所有原則定義，並且尋找「需要 SQL Server 12.0 版」原則定義：

```azurecli
az policy definition list
```

Azure 原則隨附您可以使用的內建原則定義。 內建原則定義如下：

- 強制執行標籤和其值
- 套用標籤和其值
- 需要 SQL Server 12.0 版

接著，提供下列資訊，然後執行下列命令來指派原則定義：

- 顯示原則指派的**名稱**。 在此情況下，讓我們使用「需要 SQL Server 12.0 版指派」。
- **原則** – 這是原則定義，這是您用來建立指派的根基。 在此情況下，它是原則定義 – 需要 SQL Server 12.0 版
- **範圍** – 範圍會決定在哪些資源或資源群組上強制執行原則指派。 範圍從訂用帳戶到資源群組。

  使用您先前加入 Azure 原則時註冊的訂用帳戶 (或資源群組)，在此範例中我們使用此訂用帳戶識別碼 - **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** 和資源群組名稱 - **FabrikamOMS**。 請務必將這些項目變更為您使用之訂用帳戶識別碼和資源群組名稱。 

命令外觀應該如下所示：

```azurecli
az policy assignment create --name Require SQL Server version 12.0 Assignment --policy Require SQL Server version 12.0 --scope /subscriptions/ 
bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

原則指派是已指派在特定範圍內發生的原則。 此範圍也可從管理群組到資源群組。

## <a name="identify-non-compliant-resources"></a>識別不相容的資源

若要檢視在此新的指派下不相容的資源：

1. 瀏覽回 Azure 原則分頁。
2. 選取左窗格上的 [相容性]，然後搜尋您建立的 [原則指派]。

   ![原則相容性](media/assign-policy-definition/policy-compliance.png)

   如果有任何現有資源與這個新的指派不相容，它們會顯示在 [不相容資源] 索引標籤下，如上所示。

## <a name="clean-up-resources"></a>清除資源

此集合中的其他指南是以本快速入門為基礎。 如果您打算繼續進行後續的教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請藉由執行以下命令來刪除建立的作業：

```azurecli
az policy assignment delete –name Require SQL Server version 12.0 Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您指派原則定義以識別 Azure 環境中的不相容資源。

若要深入了解指派原則，以確保您在**未來**建立的資源是相容的，請繼續進行教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./create-manage-policy.md)

