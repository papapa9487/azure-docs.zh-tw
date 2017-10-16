---
title: "設定 Azure 函數應用程式的設定 | Microsoft Docs"
description: "了解如何設定 Azure Functions 應用程式設定。"
services: 
documentationcenter: .net
author: rachelappel
manager: cfowler
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 04/23/2017
ms.author: glenga
ms.openlocfilehash: 3229fe0b5aacbbc716039a4a6abfd235dbc4c4f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>如何在 Azure 入口網站中管理函數應用程式 

在 Azure Functions 中，函數應用程式會提供個別函數的執行內容。 函數應用程式行為會套用至指定之函數應用程式所裝載的所有函數。 本主題說明如何在 Azure 入口網站中，設定和管理您的函數應用程式。

若要開始，請移至 [Azure 入口網站](http://portal.azure.com)，然後登入您的 Azure 帳戶。 在入口網站頂端的搜尋列中，輸入函數應用程式的名稱，然後從清單中選取它。 選取函數應用程式之後，您會看到下列頁面：

![Azure 入口網站中的函數應用程式概觀](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="manage-app-service-settings"></a>函數應用程式設定索引標籤

![Azure 入口網站中的函數應用程式概觀。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

[設定] 索引標籤可供您更新函數應用程式所使用的 Functions 執行階段版本。 您也可以在此處管理主機金鑰，這些金鑰可用來限制對函數應用程式所裝載之所有函數的 HTTP 存取。

Functions 支援「取用」主控方案和 App Service 主控方案。 如需詳細資訊，請參閱[選擇正確的 Azure Functions 服務方案](functions-scale.md)。 為了在「取用」方案中提升可預測性，Functions 可讓您設定每日使用量配額 (以 GB-秒為單位) 來限制平台使用量。 一旦達到每日使用量配額，系統就會停止函數應用程式。 針對因達到花費配額而停止運作的函數應用程式，您可以從與建立每日花費配額相同的內容中予以重新啟用。 如需有關計費的詳細資料，請參閱 [Azure Functions 定價頁面](http://azure.microsoft.com/pricing/details/functions/)。   

## <a name="platform-features-tab"></a>平台功能索引標籤

![函數應用程式平台功能索引標籤。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

函數應用程式是在 Azure App Service 平台中執行並由此平台維護。 因此，您的函數應用程式可以存取 Azure 核心虛擬主機平台的大多數功能。 [平台功能] 索引標籤可供您存取許多可在函數應用程式中使用的 App Service 平台功能。 

> [!NOTE]
> 當函數應用程式在「取用」主控方案上執行時，並非所有 App Service 功能都可供使用。

本主題的其餘部分將把焦點放在 Azure 入口網站中對 Functions 實用的下列 App Service 功能：

+ [App Service 編輯器](#editor)
+ [應用程式設定](#settings) 
+ [Console](#console)
+ [進階工具 (Kudu)](#kudu)
+ [部署選項](#deployment)
+ [CORS](#cors)
+ [驗證](#auth)
+ [API 定義](#swagger)

如需有關如何使用 App Service 設定的詳細資訊，請參閱[設定 Azure App Service 設定](../app-service/web-sites-configure.md)。

### <a name="editor"></a>App Service 編輯器

| | |
|-|-|
| ![函數應用程式 App Service 編輯器。](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | App Service 編輯器是一個進階的入口網站內編輯器，可供您用來修改 JSON 組態檔和類似的程式碼檔案。 選擇此選項會啟動一個含有基本編輯器的個別瀏覽器索引標籤。 這可讓您與 Git 存放庫整合、執行程式碼和進行偵錯，以及修改函數應用程式設定。 與預設的函數應用程式刀鋒視窗相比，此編輯器為您的函數提供一個強化的開發環境。    |

![App Service 編輯器](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings"></a>應用程式設定

| | |
|-|-|
| ![函數應用程式的應用程式設定。](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | App Service [應用程式設定] 刀鋒視窗可供您設定和管理架構版本、遠端偵錯、應用程式設定及連接字串。 當您將函數應用程式與其他 Azure 和協力廠商服務整合時，可以在這裡修改那些設定值。 |

![設定應用程式設定](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="console"></a>主控台

| | |
|-|-|
| ![Azure 入口網站中的函數應用程式主控台](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | 當您偏好從命令列與函數應用程式進行互動時，入口網站內主控台是一個理想的開發人員工具。 常用命令包含目錄和檔案建立與瀏覽，以及執行批次檔和指令碼。 |

![函數應用程式主控台](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>進階工具 (Kudu)

| | |
|-|-|
| ![Azure 入口網站中的函數應用程式 Kudu](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | App Service 的進階工具 (也稱為 Kudu) 可讓您存取函數應用程式的進階系統管理功能。 從 Kudu，您可以管理系統資訊、應用程式設定、環境變數、網站擴充功能、HTTP 標頭，以及伺服器變數。 您也可以透過瀏覽至函數應用程式的 SCM 端點 (例如 `https://<myfunctionapp>.scm.azurewebsites.net/`) 來啟動 **Kudu** |

![設定 Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">部署選項

| | |
|-|-|
| ![Azure 入口網站中的函數應用程式部署選項](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Functions 可讓您在本機電腦上開發函數程式碼。 您可以接著將本機函數應用程式專案上傳到 Azure。 除了傳統 FTP 上傳之外，Functions 還可讓您使用常用的持續整合解決方案 (例如 GitHub、VSTS、Dropbox、Bitbucket 等) 來部署函數應用程式。 如需詳細資訊，請參閱 [Azure Functions 的持續部署](functions-continuous-deployment.md)。 若要使用 FTP 或本機 Git 來手動上傳，您還必須[設定您的部署認證](functions-continuous-deployment.md#credentials)。 |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Azure 入口網站中的函數應用程式 CORS](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | 為了防止惡意程式碼在您的服務中執行，App Service 會封鎖外部來源對您函數應用程式的呼叫。 Functions 支援跨原始來源資源共用 (CORS)，可讓您定義您函數可從中接受遠端要求的允許原始來源「白名單」。  |

![設定函數應用程式的 CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>驗證

| | |
|-|-|
| ![Azure 入口網站中的函數應用程式驗證](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | 當函數使用 HTTP 觸發程序時，您可以要求呼叫必須先經過驗證。 App Service 支援 Azure Active Directory 驗證，以及使用社交提供者 (例如 Facebook、Microsoft 及 Twitter) 來登入。 如需設定特定驗證提供者的詳細資訊，請參閱 [Azure App Service 驗證概觀](../app-service/app-service-authentication-overview.md)。 |

![設定函數應用程式的驗證](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>API 定義

| | |
|-|-|
| ![Azure 入口網站中的函數應用程式 API Swagger](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Functions 支援 Swagger，可讓用戶端更容易使用 HTTP 觸發的函數。 如需有關如何使用 Swagger 建立 API 定義的詳細資訊，請瀏覽[在 Azure 中開始使用 API Apps 和 Swagger](../app-service/app-service-web-tutorial-rest-api.md)。 您也可以使用 Functions Proxy 來為多個函數定義一個單一的 API 介面。 如需詳細資訊，請參閱[使用 Azure Functions Proxy](functions-proxies.md)。 |

![設定函數應用程式的 API](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>後續步驟

+ [設定 Azure App Service 設定](../app-service/web-sites-configure.md)
+ [Azure Functions 的持續部署](functions-continuous-deployment.md)



