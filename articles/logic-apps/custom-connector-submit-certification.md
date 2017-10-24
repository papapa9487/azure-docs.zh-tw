---
title: "認證您的自訂連接器 - Azure Logic Apps | Microsoft Docs"
description: "將連接器提供給 Azure Logic Apps、Microsoft Flow 及 Microsoft PowerApps 中的所有使用者使用"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9bcb138c181df0c12548cc5dc721542fd9c9ba68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="submit-your-connectors-for-microsoft-certification"></a>提交您的連接器以進行 Microsoft 認證

若要將自訂連接器公開提供給 Azure Logic Apps、Microsoft Flow 及 Microsoft PowerApps 中的所有使用者使用，請向 Microsoft 提交您的連接器來進行檢閱、驗證及核准，以供發佈。 

## <a name="certification-criteria"></a>認證準則

| 功能 | 詳細資料 | 必要或建議 |
|------------|---------|-------------------------|
| 軟體即服務 (SaaS) 應用程式 | 符合與 Logic Apps、Flow 及 PowerApps 良好搭配運作的使用者案例。 | 必要 |
| 驗證類型 | 您的 API 必須支援 OAuth2、「API 金鑰」或「基本驗證」。 | 必要 | 
| 支援 | 您必須提供可讓客戶取得協助的支援合約。 | 必要 | 
| 可用性與執行時間 | 您應用程式的執行時間至少達到 99.9%。 | 建議 | 
|||| 

此外，如果您不是 Microsoft 合作夥伴或「獨立軟體廠商」(ISV)，但想要認證和公開發行連接器，您就必須擁有基礎服務或出示明確的權限，才能使用 API。

## <a name="validation-phases"></a>驗證階段

Microsoft 會使用下列驗證階段來評估您的連接器：

| 驗證階段 | 說明 | 
| ----- | ----------- |
| 功能 | Microsoft 會使用 Logic Apps、Flow 及 PowerApps 來測試您的連接器是否有下列錯誤： <p>- 無效的 OpenAPI (Swagger) 或 JSON 錯誤：出現在自訂連接器精靈的 [定義] 區段中 <p>- 執行階段和結構描述驗證錯誤：出現在自訂連接器精靈的 [測試] 區段中 | 
| 內容 | Microsoft 會檢查您的連接器是否針對每個實體都使用易記的名稱和描述。 請確定您連接器 Swagger 中的每個作業、輸入參數及回應屬性都具有下列元素： <p>- [摘要](../logic-apps/custom-connector-openapi-extensions.md#summary) <br>- [描述](../logic-apps/custom-connector-openapi-extensions.md#description) </br>- [可見性資訊](../logic-apps/custom-connector-openapi-extensions.md#visibility) | 
||| 

## <a name="nominate-and-submit-your-connector-to-microsoft-for-certification"></a>提名您的連接器並提交給 Microsoft 以進行認證

若要申請認證，請依照下列步驟進行操作：

1. **提名**

   1. [提交您的提名項目](https://go.microsoft.com/fwlink/?linkid=848754)。

      1. 在頁面底部，選擇 [與我們連絡]。
      2. 填寫表單，然後選取 [ISV 連接器方案和共同行銷的相關問題]。
      3. 選擇 [提交]。

   2. 簽署您收到的相互「保密合約」和「夥伴合約」。 

      Microsoft 必須要有這些已簽署的合約才能繼續進行。 
      接著，我們才可以檢查您的連接器是否符合認證準則。 
   
   3. 如果您的連接器通過核准，Microsoft 就會透過上線指示來通知您。
    
2. **檢閱**

   1. 將以下資訊傳送給您的提名連絡人以供檢閱：

      * 描述您 API 的 OpenAPI 檔案
      * 代表您連接器的圖示檔案(.png 或 .jpg) 
      
        您的圖示應該在 230 像素的正方形內有一個最大可達 160 像素的標誌。 
        最好是彩色背景搭配白色標誌。
      
      * 以十六進位格式表示的圖示商標色彩 (應該與圖示檔中的彩色背景相符)

      * 一個用於進行驗證的測試帳戶
      * 一個支援連絡人

   2. 如果我們需要更多資訊，將會與您連絡。

3. <bpt id="p1">**</bpt>Publish<ept id="p1">**</ept>

    在我們驗證您連接器的功能和內容之後，我們會暫存該連接器以供部署至所有產品和區域。
    
    根據預設，所有連接器都會發佈為「進階」連接器。 
    如果您使用 Azure 來建置您的應用程式，便可申請將您的連接器列為「標準」連接器，這將可供所有採用「Office 365 企業版」方案的使用者使用。 
    如需詳細資訊，請詢問您的提名連絡人。

## <a name="next-steps"></a>後續步驟

* [自訂連接器常見問題集](../logic-apps/custom-connector-faq.md)
* [自訂連接器概觀](../logic-apps/custom-connector-overview.md)