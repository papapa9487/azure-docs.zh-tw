---
title: "Marketplace 中 Azure 受管理的應用程式 | Microsoft Docs"
description: "描述可透過 Marketplace 取得的 Azure 受管理應用程式。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: 58ac7665abf7e75a43bb0b92bdf6f41005c3efe8
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Marketplace 中 Azure 受管理的應用程式

 MSP、ISV 和系統整合業者 (SI) 可以使用 Azure 受管理的應用程式，將其解決方案提供給所有 Azure Marketplace 客戶。 這類解決方案能減少客戶的維護與服務額外負荷。 發行者可以透過 Marketplace 銷售基礎結構和軟體。 他們可以將服務和操作支援附加至受管的應用程式。 如需詳細資訊，請參閱[受管理的應用程式概觀](managed-application-overview.md)。

這篇文章說明 MSP、ISV 或 SI 如何將應用程式發行至 Marketplace，並將它廣泛提供給客戶。

## <a name="prerequisites-for-publishing-a-managed-application"></a>發行受管理應用程式的必要條件

列於 Marketplace 的必要條件：

* 技術需求

    *  如需 Azure Resource Manager 範本基本結構和語法的詳細資訊，請參閱 [Azure Resource Manager 範本](resource-group-authoring-templates.md)。
    *  若要檢視完整範本解決方案，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/en-us/documentation/templates/)或[快速入門範本存放庫](https://github.com/azure/azure-quickstart-templates)。
    *  如需如何建立介面讓客戶透過 Marketplace 部署應用程式的詳細資訊，請參閱[建立使用者介面定義檔](managed-application-createuidefinition-overview.md)。

* 非技術性 (商務需求)

    *   您的公司或其子公司必須位於 Marketplace 支援銷售的國家/地區。
    *   您的產品授權，必須與 Marketplace 支援的計費模式相容。
    *   您必須以合乎商業行為的方式，負責為客戶提供技術支援。 支援可以為免費、付費，或透過社群支援。
    *   您必須負責為您的軟體和任何第三方廠商相依性進行授權。
    *   為了使您的供應項目可列於 Marketplace 和 Azure 入口網站中，您提供的內容必須符合標準。
    *   您必須同意 Azure Marketplace 參與原則和發行者合約中的條款。
    *   您必須同意遵守使用條款、Microsoft 隱私權聲明以及 Microsoft Azure 認證方案合約。

## <a name="create-a-new-azure-application-offer"></a>建立新的 Azure 應用程式供應項目

符合先決條件之後，您就可以建立受管理的應用程式供應項目。 讓我們先快速概觀供應項目和 SKU。

### <a name="offer"></a>提供項目

受管理應用程式的供應項目對應至發行者提供的產品分類供應項目。 如果您有想要在 Marketplace 中提供的新解決方案/應用程式類型，可以將它設定為新的供應項目。 優惠 SKU 的集合。 每個供應項目會以個別實體出現在 Marketplace。

### <a name="sku"></a>SKU

SKU 是優惠的最小購買單位。 您可以使用相同產品類別 (供應項目) 內的 SKU 來區分：

* 所支援的不同功能。
* 供應項目為受管理或不受管理。
* 支援的計費模型。

SKU 會顯示在 Marketplace 中的父供應項目底下。 它會在 Azure 入口網站中顯示為自己的可購買實體。

### <a name="set-up-an-offer"></a>設定供應項目

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2. 在左側瀏覽窗格中，選取 [+ 新增優惠] > **[Azure 應用程式]**。

    ![新增供應項目](./media/managed-application-author-marketplace/newOffer.png)

3. 填寫顯示在 [編輯器] 檢視左側的表單。 必填欄位會標示紅色星號 (*)。

    ![供應項目](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

    有四個主要表單用來建立受管理的應用程式︰

    a. 優惠設定

    b. SKU

    c. Marketplace

    d. 支援

這些表單將於下列各節中詳細說明。

## <a name="offer-settings-form"></a>供應項目設定表單
使用此基本表單來指定供應項目設定。

1. 填寫 [供應項目設定] 表單。 下列說明各個不同欄位：

    a. **供應項目 ID** - 此唯一識別碼可識別發行者設定檔內的供應項目。 此識別碼會顯示在產品的 URL，Resource Manager 範本和計費報告中。 此識別碼只能包含小寫英數字元或連字號 (-)。 識別碼不能以連字號結尾。 最多不能超過 50 個字元。 供應項目上架後，此欄位便會鎖住。

    b. **發行者 ID**：使用此下拉式表單，選擇您想要在哪個發行者設定檔之下發佈此供應項目。 供應項目上架後，此欄位便會鎖住。

    c. **名稱**：供應項目的這個顯示名稱會出現在 Marketplace 和入口網站中。 它最多不能超過 50 個字元。 包含產品的可辨識品牌名稱。 請勿在此包含您的公司名稱，除非這是行銷方式。 如果您在自己的網站行銷此供應項目，請確認名稱與您網站顯示的名稱相同。

2. 選取 [儲存] 儲存您的進度。 

## <a name="skus-form"></a>SKU 表單
下一個步驟是為您的供應項目新增 SKU。

1. 選取 [SKU] > [新增 SKU]。 

    ![選取新的 SKU](./media/managed-application-author-marketplace/newOffer_skus.png)

2. 輸入 [SKU 識別碼]。 SKU 識別碼是在供應項目內 SKU 的唯一識別碼。 此識別碼會顯示在產品的 URL，Resource Manager 範本和計費報告中。 此識別碼只能包含小寫英數字元或連字號 (-)。 此識別碼不能以連字號結尾，且最多不能超過 50 個字元。 供應項目上架後，此欄位便會鎖住。 您可以在一個優惠內建立多個 SKU。 您預計發佈的每個映像都需要 SKU。

3. 填寫下列表單上的 [SKU 詳細資料] 區段：

    ![提供新的 SKU](./media/managed-application-author-marketplace/newOffer_newsku.png)

    填寫下列欄位：
    
    a. **標題**：輸入此 SKU 的標題。 此標題顯示在此項目的資源庫中。

    b. **摘要**：輸入此 SKU 的簡短摘要。 此文字會出現在標題底下。

    c. **描述**：提供有關 SKU 的詳細描述。

    d. **SKU 類型**：允許的值包括 [受管理的應用程式] 和 [解決方案範本]。 此案例中，選取 [受管理的應用程式]。

4. 填寫下列表單上的 [套件詳細資料] 區段：

    ![Package](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

    填寫下列欄位：

    a. **目前的版本**：輸入您上傳的套件版本。 其格式應該是 `{number}.{number}.{number}{number}`。

    b. **選取套件檔案**：此套件包含壓縮成 .zip 檔案的下列檔案：
    * **applianceMainTemplate.json**：用來部署解決方案/應用程式的部署範本檔案。 如需有關如何建立部署範本檔案的詳細資訊，請參閱[建立第一個 Azure Resource Manager 範本](resource-manager-create-first-template.md)。
    * **appliancecreateUIDefinition.json**：Azure 入口網站會使用這個檔案來產生使用者介面，用來佈建此解決方案/應用程式。 如需詳細資訊，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
    * **mainTemplate.json**：此範本檔案只包含 Microsoft.Solution/appliances 資源。 mainTemplate 檔案包含下列屬性：

        *  **kind**：針對 Marketplace 中受管理的應用程式使用 [Marketplace]。
        *  **ManagedResourceGroupId**：客戶訂用帳戶中的資源群組，當中會部署 applianceMainTemplate.json 中所定義的所有資源。
        *  **PublisherPackageId**：此字串可唯一識別套件。 請以 `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}` 格式提供值。

從發佈入口網站取得 [供應項目識別碼] 和 [發行者識別碼]，如下圖所示：

![優惠識別碼](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
取得 [SKU 識別碼]，如下圖所示：

![SKU 識別碼](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
取得套件**版本**，如下圖所示：

![套件版本](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  根據前述範例，**PublisherPackageId** 的值是 `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`。

  範例 mainTemplate.json：

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountNamePrefix": {
        "type": "string",
        "metadata": {
          "description": "Specify the name of the storage account"
        }
      },
      "storageAccountType": {
        "type": "string"
      }
    },
    "variables": {
      "managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
    },
    "resources": [{
      "type": "Microsoft.Solutions/appliances",
      "apiVersion": "2016-09-01-preview",
      "name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
      "location": "[resourceGroup().location]",
      "kind": "marketplace",
      "properties": {
        "managedResourceGroupId": "[variables('managedResourceGroup')]",
        "PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0",
        "parameters": {
          "storageAccountName": {
            "value": "[parameters('storageAccountNamePrefix')]"
          },
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }],
    "outputs": {

    }
  }
  ```

此套件應該包含要成功佈建此應用程式所需的任何其他巢狀範本或指令碼。 mainTemplate.json、applianceMainTemplate.json 和 applianceCreateUIDefinition.json 檔案必須存在於根資料夾。

* **Authorizations**：這個屬性會定義誰可以存取客戶訂用帳戶中的資源以及其存取層級。 發行者可以使用它來代表客戶管理應用程式。
* **PrincipalId**：這個屬性是使用者、使用者群組或應用程式的 Azure Active Directory (Azure AD) 識別碼，其擁有者被授與客戶訂用帳戶中資源的特定權限。 角色定義描述權限。 
* **角色定義**：這個屬性是 Azure AD 提供的所有內建角色型存取控制 (RBAC) 角色清單。 您可以選取最適合用於代表客戶管理資源的角色。

您可以新增多個授權。 建議您建立 AD 使用者群組並且在 **PrincipalId** 中指定其識別碼。 如此一來，您可以將更多使用者新增至使用者群組，而不需要更新 SKU。

如需 RBAC 的詳細資訊，請參閱[在 Azure 入口網站中開始使用 RBAC](../active-directory/role-based-access-control-what-is.md)。

## <a name="marketplace-form"></a>Marketplace 表單

Marketplace 表單會要求 [Azure Marketplace](https://azuremarketplace.microsoft.com) 以及 [Azure 入口網站](https://portal.azure.com/)上顯示的欄位。

### <a name="preview-subscription-ids"></a>預覽訂用帳戶識別碼

輸入 Azure 訂用帳戶識別碼清單，這些訂用帳戶可以在供應項目發佈之後予以存取。 您可以使用這些允許的訂用帳戶，在供應項目上架前測試預覽的供應項目。 您可以在合作夥伴入口網站中將多達 100 個訂用帳戶編入允許清單中。

### <a name="suggested-categories"></a>建議的類別

從清單中選擇與您的供應項目最有關聯的五種類別。 這些類別用於將您的供應項目對應至 [Azure Marketplace](https://azuremarketplace.microsoft.com) 和 [Azure 入口網站](https://portal.azure.com/)中提供的產品類別。

#### <a name="azure-marketplace"></a>Azure Marketplace

受管理的應用程式摘要會顯示下列欄位：

![Marketplace 摘要](./media/managed-application-author-marketplace/publishvm10.png)

受管理應用程式的 [概觀] 索引標籤會顯示下列欄位：

![Marketplace 概觀](./media/managed-application-author-marketplace/publishvm11.png)

受管理應用程式的 [方案 + 價格] 索引標籤會顯示下列欄位：

![Marketplace 方案](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Azure 入口網站

受管理的應用程式摘要會顯示下列欄位：

![入口網站摘要](./media/managed-application-author-marketplace/publishvm12.png)

受管理應用程式的概觀會顯示下列欄位：

![入口網站概觀](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>標誌指導方針

您在 Cloud Partner 入口網站中上傳的所有標誌都應該遵循下列指導方針︰

*   Azure 設計具有簡單的調色盤。 限制標誌上的主要和次要色彩數目。
*   入口網站的佈景主題色彩是白色與黑色。 請勿使用這些色彩作為標誌的背景色彩。 請使用可讓標誌在入口網站突顯出來的色彩。 建議您使用簡單的主要色彩。 如果您使用透明背景，請確定標誌和文字不是白色、黑色或藍色。
*   請不要在標誌上使用漸層背景。
*   請勿在標誌上放置文字 (甚至是公司或品牌名稱)。 標誌的外觀與風格應該是「一般」，且避免使用漸層。
*   確定標誌不會自動縮放。

#### <a name="hero-logo"></a>主圖標誌

主圖標誌是選擇性的。 發行者可以選擇不上傳主圖標誌。 上傳主圖圖示之後，便無法刪除。 屆時合作夥伴必須遵循主圖圖示的 Marketplace 指導方針。

主圖標誌圖示應該遵循下列指導方針：

*   發行者顯示名稱、方案標題和供應項目完整摘要會以白色顯示。 因此，請勿使用淺色作為主圖圖示的背景。 主圖圖示不得使用黑色、白色和透明背景。
*   在供應項目列出之後，發行者顯示名稱、方案標題、供應項目完整摘要和 [建立] 按鈕會以程式設計方式內嵌在主圖標誌內。 因此，請勿在設計主圖標誌時輸入任何文字。 在右邊留下空白空間，因為系統會以程式設計方式文字放入該空間。 右側的文字空白空間應該是 415 x 100 像素。 它是從左邊位移 370 像素。

    ![主圖標誌範例](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>支援表單

在 [支援] 表單中填寫貴公司的支援連絡人。 此資訊可能是工程連絡人和客戶支援連絡人。

## <a name="publish-an-offer"></a>發佈提供項目

填妥所有區段之後，請選取 [發佈] 開始讓客戶可使用供應項目的程序。

## <a name="next-steps"></a>後續步驟

* 如需受管理應用程式的簡介，請參閱[受管理的應用程式概觀](managed-application-overview.md)。
* 如需從 Marketplace 使用受管理應用程式的詳細資訊，請參閱[在 Marketplace 中使用 Azure 受管理的應用程式](managed-application-consume-marketplace.md)。
* 如需發佈 Service Catalog 受管理應用程式的資訊，請參閱[建立和發佈 Service Catalog 受管理的應用程式](managed-application-publishing.md)。
* 如需使用 Service Catalog 受管理應用程式的詳細資訊，請參閱[使用 Service Catalog 受管理的應用程式](managed-application-consumption.md)。
