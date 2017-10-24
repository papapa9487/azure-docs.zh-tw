---
title: "如何將 Power BI 工作區集合內容移轉至 Power BI Embedded | Microsoft Docs"
description: "了解如何從 Power BI 工作區集合移轉至 Power BI Embedded，以及利用進階功能內嵌於應用程式中。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 069f31c8213bd0d8586f7ca50e543acfdad8a2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>如何將 Power BI 工作區集合內容移轉至 Power BI Embedded

了解如何從 Power BI 工作區集合移轉至 Power BI Embedded。 本文提供從 Azure Power BI 工作區集合移轉至 Power BI Embedded 的指引。 我們也將討論預期會有哪些應用程式變更。

在 Power BI Premium 版本公開上市之後，Power BI 工作區集合資源仍能在一段有限時間內繼續使用。 具有 Enterprise 合約的客戶可以繼續存取他們現有的工作區集合，直到其現有合約到期為止。 透過直接通路或 CSP 通路取得 Power BI 工作區集合的客戶，則可在 Power BI Premium 公開上市後一年內享有存取權。

> [!IMPORTANT]
> 儘管移轉相依於 Power BI 服務，但在使用**內嵌權杖**時，對於您應用程式的使用者來說，與 Power BI 之間並無任何相依性。 它們不需要註冊 Power BI，就能檢視您應用程式中內嵌的內容。 您可以為客戶使用這個內嵌 Power BI 的內嵌方法。

![Power BI Embedded 流程](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>移轉準備工作

準備從 Power BI 工作區集合服務移轉至 Power BI Embedded 時，您需要完成幾件事。 您需要可用的租用戶，以及具有 Power BI Pro 授權的使用者。

1. 確定您具有 Azure Active Directory (Azure AD) 租用戶的存取權。

    要使用哪個租用戶？

    * 使用現有的公司 Power BI 租用戶？
    * 針對您的應用程式使用不同的租用戶？
    * 針對每位客戶使用不同的租用戶？

    如果您決定為應用程式或每位客戶建立新的租用戶，請參閱下列其中一篇文章：

    * [建立 Azure Active Directory 租用戶](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [如何取得 Azure Active Directory 租用戶](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)。

2. 在這個新的租用戶內建立使用者，作為您應用程式的「主要」帳戶。 該帳戶必須註冊 Power BI 且需為它指派 Power BI Pro 授權。

## <a name="accounts-within-azure-ad"></a>Azure AD 中的帳戶

下列帳戶必須存在於您的租用戶中。

> [!NOTE]
> 這些帳戶需要有 Power BI Pro 授權，才能使用應用程式工作區。

1. 租用戶管理員使用者。

    建議內嵌應用程式工作區將租用戶管理員列為成員。

2. 適用於建立內容之分析師的帳戶。

    您應該視需要將這些使用者指派給應用程式工作區。

3. 應用程式的「主要」使用者帳戶或服務帳戶。

    應用程式後端會儲存此帳戶的認證。 使用「主要」帳戶來取得要與 Power BI REST API 搭配使用的 Azure AD 權杖。 此帳戶可用來產生應用程式的內嵌權杖。 「主要」帳戶必須是建立來供內嵌之應用程式工作區的管理員。

    **此帳戶只是您組織中基於內嵌目的而使用的一般使用者帳戶。**

## <a name="app-registration-and-permissions"></a>應用程式註冊和權限

若要進行 REST API 呼叫，請向 Azure AD 註冊應用程式。 新增設定會在 Microsoft Azure 入口網站以及 Power BI 應用程式註冊頁面中加以套用。 如需詳細資訊，請參閱[註冊 Azure AD 應用程式以內嵌 Power BI 內容](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/)。

建議您使用應用程式「主要」帳戶來註冊應用程式。

## <a name="create-app-workspaces-required"></a>建立應用程式工作區 (必要)

如果您的應用程式會服務多位客戶，您可以利用應用程式工作區來提供更好的隔離。 在您的客戶之間隔離儀表板和報表。 您接著可針對每個應用程式工作區使用 Power BI 帳戶，進一步隔離客戶之間的應用程式體驗，但您只能使用一個帳戶以使其保持簡單。

> [!IMPORTANT]
> 您無法對客戶使用個人工作區 (「我的工作區」) 來充分運用內嵌。

您需要具有 Pro 授權的使用者，才能在 Power BI 中建立應用程式工作區。 建立應用程式工作區的 Power BI 使用者預設是該工作區的管理員。 **應用程式「主要」帳戶必須是工作區的管理員。**

## <a name="content-migration"></a>內容移轉

將內容從工作區集合移轉至 Power BI Embedded，可在您目前的方案中以平行方式完成，而且不需任何停機時間。

**移轉工具**可供協助將 Power BI 工作區集合的內容複製到 Power BI Embedded。 當您擁有許多報表時特別有用。 如需詳細資訊，請參閱 [Power BI Embedded 移轉工具](migrate-tool.md)。

內容移轉主要依賴兩個 API。

1. 下載 PBIX：此 API 可以下載在 2016 年 10 月之後上傳至 Power BI 的 PBIX 檔案。
2. 匯入 PBIX：此 API 可將任何 PBIX 上傳至 Power BI。

如需一些相關程式碼片段，請參閱[從 Power BI Embedded 移轉內容的程式碼片段](migrate-code-snippets.md)。

### <a name="report-types"></a>報表類型

目前提供數種類型的報表，每種都需要不同的移轉流程。

#### <a name="cached-dataset-and-report"></a>快取的資料集和報表

快取資料集是指已匯入資料，而不是即時連線或 DirectQuery 連線的 PBIX 檔案。

**流程**

1. 從您的 Power BI 工作區集合工作區呼叫「下載 PBIX」API。
2. 儲存 PBIX。
3. 為您的 Power BI Embedded 工作區呼叫「匯入 PBIX」。

#### <a name="directquery-dataset-and-report"></a>DirectQuery 資料集和報表

**流程**

1. 呼叫 GET https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources 並儲存接收到的連接字串。
2. 從您的 Power BI 工作區集合工作區呼叫「下載 PBIX」API。
3. 儲存 PBIX。
4. 為您的 Power BI Embedded 工作區呼叫「匯入 PBIX」。
5. 呼叫 POST https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections 來更新連接字串
6. 呼叫 GET https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources 來取得 GW 識別碼和資料來源識別碼
7. 呼叫 PATCH https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id} 來更新使用者的認證

#### <a name="old-dataset-and-reports"></a>舊的資料集和報表

2016 年 10 月之前上傳的報表不支援「下載 PBIX」功能。

**流程**

1. 從您的開發環境 (您的內部原始檔控制) 取得 PBIX。
2. 為您的 Power BI Embedded 工作區呼叫「匯入 PBIX」。

#### <a name="push-dataset-and-report"></a>推送資料集和報表

「下載 PBIX」不支援「推送 API」資料集。 「推送 API」資料集資料無法從 Power BI 工作區集合移植到 Power BI Embedded。

**流程**

1. 呼叫「建立資料集」API 搭配資料集 Json，來為您的 Power BI Embedded 工作區建立資料集。
2. 為已建立的資料集重建報表*。

您可以使用一些因應措施，藉由嘗試下列動作，將推送 API 報表從 Power BI 工作區集合移轉至 Power BI Embedded：

1. 將一些虛擬 PBIX 上傳至您的 Power BI 工作區集合工作區。
2. 複製推送 API 報表，並將它繫結至步驟 1 中的虛擬 PBIX。
3. 下載含虛擬 PBIX 的推送 API 報表。
4. 將虛擬 PBIX 上傳至您的 Power BI Embedded 工作區。
5. 在 Power BI Embedded 工作區中建立推送資料集。
6. 將報表重新繫結至推送 API 資料集。

## <a name="create-and-upload-new-reports"></a>建立和上傳新的報表

除了從 Power BI 工作區集合移轉的內容，您還可以使用 Power BI Desktop 建立報表和資料集，然後將這些報表發佈至應用程式工作區。 發佈報表的使用者需要具有 Power BI Pro 授權，才能發佈至應用程式工作區。

## <a name="rebuild-your-application"></a>重建您的應用程式

1. 修改應用程式以使用 Power BI REST API 和 powerbi.com 內的報表位置。

2. 使用應用程式的「主要」帳戶，重建您的 AuthN/AuthZ 驗證。 您可以藉由使用「內嵌權杖」[](https://msdn.microsoft.com/library/mt784614.aspx)來允許這位使用者代表其他使用者動作。

3. 將報表從 Power BI Embedded 內嵌到您的應用程式。 如需詳細資訊，請參閱[內嵌 Power BI 儀表板、報表和磚](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)。

## <a name="map-your-users-to-a-power-bi-user"></a>將您的使用者對應至 Power BI 使用者

在應用程式中，會基於應用程式用途，將您在應用程式中管理的使用者對應至「主要」Power BI 認證。 適用於此 Power BI「主要」帳戶的認證會儲存在您的應用程式中並用來建立內嵌權杖。

## <a name="what-to-do-when-you-are-ready-for-production"></a>當您準備好用於生產環境時該怎麼做

當您準備好移到生產環境時，必須執行下列作業：

- 如果您使用不同的租用戶進行開發，則需要確定您的應用程式工作區以及儀表板和報表均可在生產環境中使用。 請確定您已在適用於生產租用戶的 Azure AD 中建立應用程式，並指派適當的應用程式權限 (如步驟 1 所示)。

- 購買符合您需求的容量。 您可以使用[內嵌分析容量規劃白皮書](https://aka.ms/pbiewhitepaper) \(英文\)，協助了解您的潛在需求。 當您準備好要購買時，您可以在 Azure 入口網站內購買 Power BI Embedded 資源。

- 編輯應用程式工作區，並在 [進階] 下方將它指派給容量。

    ![將應用程式工作區指派給 powerbi.com 中的容量](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- 將您更新的應用程式部署到生產環境，然後開始從 Power BI Embedded 內嵌報表。

## <a name="after-migration"></a>移轉之後

您需要在 Power BI 工作區集合內進行某些清理作業。

- 在 Power BI 工作區集合的 Azure 服務中，移除已部署方案的所有工作區。
- 刪除任何存在於 Azure 中的工作區集合。

## <a name="next-steps"></a>後續步驟

恭喜！ 您的應用程式會立即移轉至 Power BI Embedded。 如需如何內嵌 Power BI 儀表板、報表和資料集的相關資訊，請參閱[如何內嵌 Power BI 儀表板、報表和磚](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)。

有其他疑問？ [嘗試在 Power BI 社群中提問](http://community.powerbi.com/) \(英文\)