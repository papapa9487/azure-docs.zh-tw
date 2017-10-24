---
title: "存取 Azure Logic Apps 的內部部署資料來源 | Microsoft Docs"
description: "設定內部部署資料閘道，以便從邏輯應用程式存取內部部署資料來源"
keywords: "存取資料, 內部部署, 資料傳輸, 加密, 資料來源"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: f385d832deed2eaf8ea21eb75d62944cbbf3d13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-data-sources-on-premises-from-logic-apps-with-on-premises-data-gateway"></a>使用內部部署資料閘道從邏輯應用程式連線至內部部署資料來源

若要從邏輯應用程式存取內部部署資料來源，請使用支援的連接器來設定可供邏輯應用程式使用的內部部署資料閘道。 此閘道可作為橋樑，讓內部部署資料來源與邏輯應用程式之間快速地傳輸和加密資料。 閘道會在加密通道上經過 Azure 服務匯流排轉送來自內部部署來源的資料。 源自閘道代理程式的所有流量都是安全輸出流量。 深入了解[資料閘道的運作方式](logic-apps-gateway-install.md#gateway-cloud-service)。 

閘道可連線到下列內部部署資料來源︰

*   BizTalk Server 2016
*   DB2  
*   檔案系統
*   Informix
*   MQ
*   MySQL
*   Oracle 資料庫
*   PostgreSQL
*   SAP 應用程式伺服器 
*   SAP 訊息伺服器
*   SharePoint
*   SQL Server
*   Teradata

下列步驟示範如何設定內部部署資料閘道，使其與邏輯應用程式搭配使用。 如需所支援連接器的詳細資訊，請參閱 [Azure Logic Apps 的連接器](../connectors/apis-list.md)。 

如需如何使用閘道與其他服務的資訊，請參閱下列文章：

*   [Microsoft Power BI 內部部署資料閘道](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure Analysis Services 內部部署資料閘道](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow 內部部署資料閘道](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps 內部部署資料閘道](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>需求

* 您必須[已在本機電腦上安裝資料閘道](logic-apps-gateway-install.md)。

* 登入 Azure 入口網站時，您必須使用之前用來[安裝內部部署資料閘道](logic-apps-gateway-install.md#requirements)的同一個公司或學校帳戶。 為閘道安裝在 Azure 入口網站建立閘道資源時，登入的帳戶也必須擁有 Azure 訂用帳戶以供使用。

* 您的閘道安裝不能已由 Azure 閘道資源加以宣告。 您只能將閘道安裝關聯至一個 Azure 閘道資源。 當您建立閘道資源時便已完成宣告，因此該安裝無法再供其他資源使用。

* 內部部署資料閘道會以 Windows 服務身分執行，並且設定成使用 `NT SERVICE\PBIEgwService` 作為 Windows 服務登入認證。 若要在 Azure 入口網站中建立和維護閘道資源，[Windows 服務帳戶](../logic-apps/logic-apps-gateway-install.md)必須至少具備**參與者**權限。 

  > [!NOTE]
  > Windows 服務帳戶與用來連線至內部部署資料來源的帳戶不同，也與用來登入雲端服務的公司或學校 Azure 帳戶不同。

## <a name="set-up-the-data-gateway-connection"></a>設定資料閘道連線

### <a name="1-install-the-on-premises-data-gateway"></a>1.安裝內部部署資料閘道

如果您還沒有這麼做，請依照[步驟來安裝內部部署資料閘道](logic-apps-gateway-install.md)。 在繼續進行其他步驟前，請先確定您已在本機電腦上安裝資料閘道。

<a name="create-gateway-resource"></a>

### <a name="2-create-an-azure-resource-for-the-on-premises-data-gateway"></a>2.建立內部部署資料閘道的 Azure 資源

在本機電腦上安裝閘道之後，您必須將資料閘道建立為 Azure 資源。 此步驟也會讓閘道資源與 Azure 訂用帳戶產生關聯。

1. 登入 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")。 請務必使用您在安裝閘道時所使用的同一個 Azure 公司或學校電子郵件地址。

2. 在主要 Azure 功能表中，選擇 [新增] > [企業整合] > [內部部署資料閘道]，如以下所示︰

   ![尋找 [內部部署資料閘道]](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. 在 [建立連線閘道] 頁面上，提供下列詳細資料以建立資料閘道資源︰

    * **名稱**：輸入閘道資源的名稱。 

    * **訂用帳戶**︰選取要與閘道資源關聯的 Azure 訂用帳戶。 
    此訂用帳戶必須是和邏輯應用程式相同的訂用帳戶。
   
      預設的訂用帳戶會由您用來登入的 Azure 帳戶來決定。

    * **資源群組**：建立資源群組或選取現有資源群組來部署閘道資源。 
    資源群組可協助您集合管理相關的 Azure 資產。

    * **位置**：Azure 對此位置有所限制，它的所在區域必須和[閘道安裝](logic-apps-gateway-install.md)期間為閘道雲端服務所選取的區域相同。 

      > [!NOTE]
      > 請確定閘道資源位置符合閘道雲端服務的位置。 否則，已安裝的閘道清單中可能不會出現您的閘道安裝，從而供您在下一個步驟中選取。
      > 
      > 閘道資源和邏輯應用程式可使用不同的區域。

    * **安裝名稱**︰如果您的閘道安裝還不是選取狀態，請選取您先前安裝的閘道。 

    若要在 Azure 儀表板中新增閘道資源，請選擇 [釘選到儀表板]。 
    完成之後，請選擇 [建立]。

    例如：

    ![提供詳細資料以建立內部部署資料閘道](./media/logic-apps-gateway-connection/createblade.png)

    在任何時候，若要尋找或檢視資料閘道，請從主要 Azure 功能表，移至 [更多服務] > [企業整合] > [內部部署資料閘道]。

    ![移至 [更多服務] > [企業整合] > [內部部署資料閘道]](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

### <a name="3-connect-your-logic-app-to-the-on-premises-data-gateway"></a>3.將邏輯應用程式連線到內部部署資料閘道

您已建立資料閘道資源並讓 Azure 訂用帳戶與該資源相關聯，接下來請建立邏輯應用程式與資料閘道之間的連線。

> [!NOTE]
> 閘道連線位置必須和邏輯應用程式位於相同區域，但您可以使用位於不同區域的資料閘道。

1. 在 Azure 入口網站中，於邏輯應用程式設計工具中建立或開啟邏輯應用程式。

2. 新增支援內部部署連線的連接器 (例如 SQL Server)。

3. 按照所示順序，選取 [透過內部部署資料閘道連線]、提供唯一的連線名稱和必要資訊，然後選取您要使用的資料閘道資源。 完成之後，請選擇 [建立]。

   > [!TIP]
   > 唯一的連線名稱可在之後協助您輕鬆識別該連線，尤其是當您建立了多個連線時。 如果情況允許，也請在使用者名稱中包含完整網域。 

   ![建立邏輯應用程式與資料閘道之間的連線](./media/logic-apps-gateway-connection/blankconnection.png)

恭喜，閘道連線現已可供邏輯應用程式使用。

## <a name="edit-your-gateway-connection-settings"></a>編輯閘道連線設定

在為邏輯應用程式建立閘道連線之後，您之後或許會想更新該特定連線的設定。

1. 若要尋找閘道連線︰

   * 在邏輯應用程式功能表上的 [開發工具] 底下，選取 [API 連線]。 
   
     [API 連線] 窗格會顯示與邏輯應用程式相關聯的所有 API 連線，包括閘道連線。

     ![移至邏輯應用程式，選取 [API 連線]](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * 或者，從主要 Azure 功能表中，移至 [更多服務] > [Web + 行動] > [API 連線]，來尋找與您 Azure 訂用帳戶關聯的所有 API 連線，包括閘道連線。 

   * 或者，在主要 Azure 功能表上，移至 [所有資源]，來尋找與您 Azure 訂用帳戶關聯的所有 API 連線，包括閘道連線。

2. 選取您要檢視或編輯的閘道連線，然後選擇 [編輯 API 連線]。

   > [!TIP]
   > 如果您的更新未生效，請嘗試[先停止再重新啟動閘道 Windows 服務](./logic-apps-gateway-install.md#restart-gateway)。

<a name="change-delete-gateway-resource"></a>
## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>切換或刪除內部部署資料閘道資源

若要建立不同的閘道資源、讓閘道與其他資源相關連，或是要移除閘道資源，您可以直接刪除閘道資源，這並不會影響閘道安裝。 

1. 從主要 Azure 功能表，移至 [所有資源]。 
2. 尋找並選取資料閘道資源。
3. 選擇 [內部部署資料閘道]，然後在 [資源] 工具列上選擇 [刪除]。

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>常見問題集

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>後續步驟

* [保護邏輯應用程式](./logic-apps-securing-a-logic-app.md)
* [Logic Apps 範例和常見案例](./logic-apps-examples-and-scenarios.md)
