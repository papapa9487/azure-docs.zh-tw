---
title: "使用 FTP/S 將您的應用程式部署至 Azure App Service | Microsoft Docs"
description: "了解如何使用 FTP 或 FTPS 將您的應用程式部署至 Azure App Service。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: cephalin;dariac
ms.openlocfilehash: e3ac2f2156719ad975049b0c2b4cbca81d88e779
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>使用 FTP/S 將您的應用程式部署至 Azure App Service

這篇文章說明如何使用 FTP 或 FTPS 將您的 Web 應用程式、行動裝置應用程式後端或 API 應用程式部署到 [Azure App Service (英文)](http://go.microsoft.com/fwlink/?LinkId=529714)。

您應用程式的 FTP/S 端點已經啟動。 啟用 FTP/S 部署不需要任何組態。

> [!IMPORTANT]
> 我們將持續逐步改善 Microsoft Azure 平台安全性。 在此持續努力的過程中，我們規劃升級德國中部和德國東北部地區的 Web 應用程式。 在此 Web Apps 期間，將會停止使用純文字 FTP 通訊協定進行部署。 建議客戶切換至 FTPS 進行部署。 此升級預定在 9/5 進行，在這段期間預期不會對您的服務造成任何中斷。 感謝您支援這項工作。

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>步驟 1：設定部署認證

若要存取您應用程式的 FTP 伺服器，您必須先部署認證。 

若要設定或重設部署認證，請參閱 [Azure App Service 部署認證](app-service-deployment-credentials.md)。 本教學課程示範如何使用使用者層級的認證。

## <a name="step-2-get-ftp-connection-information"></a>步驟 2：取得 FTP 連線資訊

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟應用程式的[資源刀鋒視窗](../azure-resource-manager/resource-group-portal.md#manage-resources)。
2. 在左側功能表中選取 [概觀]，然後記下 [FTP/部署使用者]、[FTP 主機名稱] 和 [FTPS 主機名稱] 的值。 

    ![FTP 連線資訊](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Azure 入口網站顯示的 **FTP/部署使用者** 的使用者值，包括應用程式名稱，以針對 FTP 伺服器提供適當的內容。
    > 當您在左側功能表中選取 [屬性] 時，可以找到相同資訊。 
    >
    > 此外，絕對不會顯示部署密碼。 如果您忘記您的部署密碼，請回到[步驟 1](#step1) 並重設您的部署密碼。
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>步驟 3︰將檔案部署至 Azure

1. 從您的 FTP 用戶端 ([Visual Studio](https://www.visualstudio.com/vs/community/)、[FileZilla](https://filezilla-project.org/download.php?type=client) 等等)，使用您所蒐集的連接資訊以連接到您的應用程式。
3. 將您的檔案和其個別的目錄結構複製到 Azure 中的 [**/site/wwwroot** 目錄](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (或 WebJobs 的 **/site/wwwroot/App_Data/Jobs/** 目錄)。
4. 瀏覽至您的應用程式 URL，以確認應用程式運作正常。 

> [!NOTE] 
> 與 [Git 型部署](app-service-deploy-local-git.md)不同，FTP 部署不支援下列的部署自動化︰ 
>
> - 相依性還原 (例如 NuGet、NPM、PIP 和編輯器自動化)
> - .NET 二進位檔的編譯
> - web.config 的產生 (此處是 [Node.js 範例](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> 您必須在您的本機電腦上手動還原、建置及產生這些必要的檔案，並與您的應用程式一起部署它們。
>
>

## <a name="next-steps"></a>後續步驟

如需更多的進階部署案例，請嘗試[使用 Git 部署至 Azure ](app-service-deploy-local-git.md)。 Git 型部署至 Azure 可啟用版本控制、封裝還原、MSBuild 等等。

## <a name="more-resources"></a>其他資源

* [Azure App Service 部署認證](app-service-deploy-ftp.md)
