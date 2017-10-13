---
title: "將 ASP.NET Core Linux Docker 容器部署到遠端 Docker 主機 | Microsoft Docs"
description: "了解如何使用 Visual Studio Tools for Docker，將 ASP.NET Core Web 應用程式部署到在 Azure Docker Host Linux VM 上執行的 Docker 容器"
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 4a87ee69f23779bf4f6f5db40bc05edbcfc7668d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>將 ASP.NET 容器部署到遠端 Docker 主機
## <a name="overview"></a>Overview
Docker 是輕量級容器引擎，與虛擬機器在某些方面類似，您可以用它來裝載應用程式和服務。
本教學課程將逐步引導您使用 [Visual Studio Tools for Docker](https://docs.microsoft.com/en-us/dotnet/articles/core/docker/visual-studio-tools-for-docker) 延伸模組，透過 PowerShell 將 ASP.NET Core 應用程式部署至 Azure 上的 Docker 主機。

## <a name="prerequisites"></a>必要條件
需要下列項目以完成此教學課程：

* 按照 [如何使用 Docker 電腦搭配 Azure](virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 中的說明建立 Azure Docker Host VM
* 安裝最新版本的 [Visual Studio](https://www.visualstudio.com/downloads/)
* 下載 [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)
* 安裝 [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1.建立 ASP.NET 核心 Web 應用程式
下列步驟會逐步引導您建立將在本教學課程中使用的基本 ASP.NET Core 應用程式。

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2.新增 Docker 支援
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3.使用 DockerTask.ps1 PowerShell 指令碼
1. 將 PowerShell 提示開啟至您的專案的根目錄。 
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. 驗證遠端主機是否正在執行。 您應該會看到狀態 = 執行中 
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
3. 使用 -Build 參數建置應用程式
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  

   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. 使用 -Run 參數執行應用程式
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   Docker 完成後，您應該會看到類似下面的結果：
   
   ![檢視您的應用程式][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
