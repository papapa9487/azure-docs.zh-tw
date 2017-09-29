---
title: "在 Azure Stack 上部署 App Service 之前 | Microsoft Docs"
description: "在 Azure Stack 上部署 App Service 之前必須完成的步驟"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 3cba11acc6279f24d0a47af8978610180724c0a2
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>開始使用 Azure Stack 上的 App Service 之前

您需要一些項目，才能在 Azure Stack 上安裝 Azure App Service：

- 完成部署 [Azure Stack 開發套件](azure-stack-run-powershell-script.md)。
- 您的 Azure Stack 系統中有足夠空間可供 Azure Stack 上之 App Service 的小型部署使用。  所需空間大約是 20 GB 的磁碟空間。
- 當您針對 Azure Stack 上的 App Service 建立虛擬機器時要使用的 Windows Server VM 映像。
- [執行 SQL Server 的伺服器](#SQL-Server)。

>[!NOTE] 
> 下列步驟*全*都會在 Azure Stack 主機電腦上進行。

若要部署資源提供者，您必須以系統管理員身分執行 PowerShell 整合式指令碼環境 (ISE)。 基於這個理由，您需要在用於登入 Azure Active Directory 的 Internet Explorer 設定檔允許 Cookie 和 JavaScript。

## <a name="turn-off-internet-explorer-enhanced-security"></a>關閉 Internet Explorer 增強式安全性

1.  以 **AzureStack/administrator** 身分登入 Azure Stack 開發套件機器，然後開啟 [伺服器管理員]。

2.  針對系統管理員和使用者關閉 [Internet Explorer 增強式安全性設定]。

3.  以系統管理員身分登入 Azure Stack 開發套件機器，然後開啟 [伺服器管理員]。

4.  針對系統管理員和使用者關閉 [Internet Explorer 增強式安全性設定]。

## <a name="enable-cookies"></a>啟用 Cookie

1.  選取 [開始] > [所有應用程式] > [Windows 附屬應用程式]。 以滑鼠右鍵按一下 [Internet Explorer] > [更多] > [以系統管理員身分執行]。

2.  出現提示時，選取 [使用建議的安全性]，然後選取 [確定]。

3.  在 Internet Explorer 中，選取 [工具] \(齒輪圖示) > [網際網路選項] > [隱私權] > [進階]。

4.  選取 [進階]。 確定已選取兩個 [接受] 核取方塊。 選取 [確定] 兩次。

5.  關閉 Internet Explorer，然後以系統管理員身分重新啟動 PowerShell ISE。

## <a name="install-powershell-for-azure-stack"></a>安裝適用於 Azure Stack 的 PowerShell

若要安裝適用於 Azure Stack 的 PowerShell，請依照[安裝 PowerShell](azure-stack-powershell-install.md) 中的步驟執行。

## <a name="use-visual-studio-with-azure-stack"></a>搭配 Azure Stack 使用 Visual Studio

若要搭配 Azure Stack 使用 Visual Studio，請依照[安裝 Visual Studio](azure-stack-install-visual-studio.md) 中的步驟執行。

## <a name="add-a-windows-server-2016-vm-image-to-azure-stack"></a>將 Windows Server 2016 VM 映像新增到 Azure Stack

由於 App Service 會部署一些虛擬機器，因此，它需要 Azure Stack 中的 Windows Server 2016 VM 映像。 若要安裝 VM 映像，請依照[新增預設虛擬機器映像](azure-stack-add-default-image.md)中的步驟執行。

## <a name="SQL-Server"></a>SQL Server

Azure Stack 上的 App Service 需要存取 SQL Server 執行個體，以建立及裝載兩個資料庫來執行 App Service 資源提供者。  如果您選擇在 Azure Stack 上部署 SQL Server VM，則它必須將 SQL 連接性層級設定為 [公用]。  您可以選擇在完成 Azure Stack 上之 App Service 安裝程式中的選項時要使用的 SQL Server 執行個體。

## <a name="next-steps"></a>後續步驟

- [安裝 App Service 資源提供者](azure-stack-app-service-deploy.md)。

<!--Image references-->
[1]: ./media/azure-stack-app-service-before-you-get-started/PSGallery.png
[2]: ./media/azure-stack-app-service-before-you-get-started/WebPI_InstalledProducts.png

