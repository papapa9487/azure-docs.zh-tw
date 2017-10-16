---
title: "開始使用私人範本 | Microsoft Docs"
description: "使用 Azure 入口網站、Azure CLI 或 PowerShell 新增、管理及共用私人範本。"
services: marketplace-customer
documentationcenter: 
author: VybavaRamadoss
manager: asimm
editor: 
tags: marketplace, azure-resource-manager
keywords: 
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar
ms.openlocfilehash: 01657619cbe579c6818a790cc3ab95a33936a565
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>開始在 Azure 入口網站上使用私人範本
[Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) 範本是用來定義您的部署的宣告式範本。 您可以定義要對解決方案部署的資源，並指定可讓您針對不同環境輸入值的參數和變數。 範本由 JSON 與運算式所組成，可讓您用來為部署建構值。

您可以在 [Azure 入口網站](https://portal.azure.com)中使用的新 [範本] 功能，並以 **Microsoft.Gallery** 資源提供者作為 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 的擴充功能，讓使用者得以從個人程式庫建立、管理和部署私人範本。

本文將逐步引導您使用 Azure 入口網站來新增、管理及共用私人 **範本** 。

## <a name="guidance"></a>指引
下列建議將協助您在使用您的方案時充分利用 **範本** ：

* **範本** 是一項封裝資源，其中包含 Resource Manager 範本和其他中繼資料。 其操作方式非常類似 Marketplace 中的項目。 主要差別在於它是私人項目 (相對於公用 Marketplace 項目)。
* **範本** 程式庫適用於需要自訂其部署的使用者。
* **範本** 適用於在 Azure 中需要簡單儲存機制的使用者。
* 開始使用現有的 Resource Manager 範本。 在 [github](https://github.com/Azure/azure-quickstart-templates) 中尋找範本，或從現有資源群組[匯出範本](../azure-resource-manager/resource-manager-export-template.md)。
* **範本** 會繫結至發佈它們的使用者。 每個具有範本讀取權限的使用者都可以看到發佈者名稱。
* **範本** 是 Resource Manager 資源，一旦發佈便無法重新命名。

## <a name="add-a-template-resource"></a>新增範本資源
在 Azure 入口網站中建立 **範本** 資源的方法有兩種。

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>方法 1︰從執行中的資源群組建立新的範本資源
1. 瀏覽至 Azure 入口網站上的現有資源群組。 在 [設定] 中選取 [匯出範本]。
2. 匯出 Resource Manager 範本後，使用 [儲存範本] 按鈕將它儲存到 [範本] 儲存機制。 在 [這裡](../azure-resource-manager/resource-manager-export-template.md)尋找匯出範本的完整詳細資料。
   <br /><br />
   ![資源群組匯出](media/rg-export-portal1.PNG)  <br />
3. 選取 [儲存至範本]  命令按鈕。
   <br /><br />
4. 輸入以下資訊：
   
   * 名稱 – 範本物件的名稱 (注意︰這是以 Azure Resource Manager 為基礎的名稱。 適用所有命名限制，一旦建立便無法變更)。
   * 說明 – 關於此範本的簡短摘要。
     
     ![儲存範本](media/save-template-portal1.PNG)  <br />
5. 按一下 [儲存] 。
   
   > [!NOTE]
   > [匯出範本] 刀鋒視窗會在匯出的 Resource Manager 範本有誤時顯示通知，但您仍可將此 Resource Manager 範本儲存至 [範本]。 請確保在重新部署已匯出的 Resource Manager 範本之前，先檢查並修正所有的 Resource Manager 範本問題。
   > 
   > 

### <a name="method-2--add-a-new-template-resource-from-browse"></a>方法 2 ︰從瀏覽加入新的範本資源
您也可以在 [瀏覽] > [範本] 中使用 [+新增] 命令按鈕，從頭新增**範本**。 您必須提供 [名稱]、[說明] 和 Resource Manager 範本 JSON。

![新增範本](media/add-template-portal1.PNG)  <br />

> [!NOTE]
> Microsoft.Gallery 是以租用戶為基礎的 Azure 資源提供者。 [範本] 資源會繫結至建立它的使用者。 但不會繫結至任何特定的訂用帳戶。 只有在部署範本時才需要選擇訂用帳戶。
> 
> 

## <a name="view-template-resources"></a>檢視範本資源
在 [瀏覽 > 範本] 可以看見您可用的所有**範本**。 這包括您所建立的**範本**以及利用各種權限層級與您共用的範本。 如需詳細資料，請參閱下面的[存取控制](#access-control-for-a-tenant-resource-provider)一節。

![檢視範本](media/view-template-portal1.PNG)  <br />

按一下清單中的項目，即可檢視 **範本** 的詳細資料。

![檢視範本](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>編輯範本資源
以滑鼠右鍵按一下瀏覽清單上的項目，或選擇 [編輯] 命令按鈕，即可起始 **範本** 的編輯流程。

![編輯範本](media/edit-template-portal1a.PNG)  <br />

您可以編輯說明或 Resource Manager 範本文字。 但無法編輯名稱，因為這是 Resource Manager 資源名稱。 當您編輯 Resource Manager 範本 JSON 時，我們會進行驗證以確保它是有效的 JSON。 選擇 [確定]，然後選擇 [儲存] 以儲存更新後的範本。

![編輯範本](media/edit-template-portal2a.PNG)  <br />

儲存 **範本** 後，您就會看到確認通知。

![編輯範本](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>部署範本資源
您可以部署任何您有**讀取**權限的**範本**。 部署流程會啟動標準 Azure 範本部署刀鋒視窗。 填妥 Resource Manager 範本參數的值以繼續進行部署。

![部署範本](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>共用範本資源
可與您的同事共用 **範本** 資源。 共用行為類似於 [Azure 上任何資源的角色指派](../active-directory/role-based-access-control-configure.md)。 **範本** 擁有者會提供權限給可與範本資源互動的其他使用者。 與您共用 **範本** 的個人或一群人將可看到 Resource Manager 範本和其資源庫屬性。

### <a name="access-control-for-the-microsoftgallery-resources"></a>Microsoft.Gallery 資源的存取控制
| 角色 | 權限 |
| --- | --- |
| 擁有者 |允許完整控制範本資源 (包括共用) |
| 讀取者 |允許讀取和執行 (部署) 範本資源 |
| 參與者 |允許範本資源的編輯和刪除權限 使用者不能與其他人共用範本 |

按一下滑鼠右鍵或在特定項目的 [檢視] 刀鋒視窗上選取瀏覽項目上的 [共用]  。 這會啟動共用經驗。

![共用範本](media/share-template-portal1a.png)  <br />

 您現在可以選擇角色及使用者或群組，以提供特定 **範本**的存取權。 可用的角色為 [擁有者]、[讀取者] 和 [參與者]。 如需詳細資料，請參閱上面的 [存取控制](#access-control-for-a-tenant-resource-provider) 一節。

![共用範本](media/share-template-portal2b.png)  <br />

![共用範本](media/share-template-portal3b.png)  <br />

按一下 [選取] 和 [確定]。 您現在可以看到您加入至資源的使用者或群組。

![共用範本](media/share-template-portal4b.png)  <br />

> [!NOTE]
> 只能與相同 Azure Active Directory 租用戶中的使用者和群組共用範本。 如果您與不在您的租用戶中的電子郵件地址共用範本，將會傳送邀請來要求使用者以來賓身分加入租用戶。
> 
> 

## <a name="next-steps"></a>後續步驟
* 若要了解如何建立 Resource Manager 範本，請參閱 [撰寫範本](../azure-resource-manager/resource-group-authoring-templates.md)
* 若要了解您可用於 Resource Manager 範本中的函數，請參閱 [範本函式](../azure-resource-manager/resource-group-template-functions.md)
* 如需設計範本的指引，請參閱 [設計 Azure 資源管理員範本的最佳做法](../azure-resource-manager/best-practices-resource-manager-design-templates.md)

