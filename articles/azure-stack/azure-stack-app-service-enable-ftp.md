---
title: "在 Azure Stack 上的 App Service 中啟用 FTP | Microsoft Docs"
description: "在 Azure Stack 上的 App Service 中啟用 FTP 必須完成的步驟"
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
ms.date: 4/6/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: bab69a9ff7e101f22713ba7f1ac2cbd0add7fdbd
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>在 Azure Stack 上的 App Service 中啟用 FTP

順利在 Azure Stack 上部署 App Service 之後，若要啟用 FTP 發佈，以便讓您的租用戶可以上傳其應用程式檔案與內容，則必須完成一些其他步驟。  在未來的版本中，這些步驟將自動執行。

> [!NOTE]
> 這些步驟適用於在 Azure Stack 資源提供者上設定 App Service 的服務或企業系統管理員。

## <a name="enable-ftp"></a>啟用 FTP

1.  以服務系統管理員身分登入 Azure Stack 入口網站。
2.  瀏覽到 [網路介面] 並選取 [資源群組 - **AppService-LOCAL**] 下的 [FTP-NIC]。 ![Azure Stack 網路介面][1]
3.  請注意 **FTP-NIC** 的 [公用 IP 位址]。 
![Azure Stack 網路介面詳細資料][2]
4.  接著，瀏覽到 [虛擬機器] 並選取 [FTP0-VM]。 ![Azure Stack 虛擬機器][3]
5.  使用 [連線] 按鈕開啟連線到 VM 的遠端桌面工作階段，並使用您在 App Service 部署期間設定的 Administrator 認證登入該工作階段。  
![Azure Stack 虛擬機器詳細資料][4]
6.  開啟 FTP VM (FTP0-VM) 上的 **Internet Information Service (IIS) Manager**。
7.  在 [網站] 下，選取 [裝載 FTP 網站]。
8.  開啟 [FTP 防火牆支援]。 ![App Service FTP0-VM 上的 IIS Manager][5]
9.  輸入 FTP-NIC 的公用 IP 位址，並按一下 [套用] ![IIS Manager FTP 防火牆支援][6]

## <a name="validate-the-enabling-of-ftp"></a>驗證 FTP 是否已啟用

1.  以服務系統管理員或租用戶身分登入 Azure Stack 入口網站。
2.  瀏覽到 [應用程式服務] 並選取您建立的 Web、行動裝置或 API 應用程式。 ![應用程式服務][7]
3.  在應用程式詳細資料中，記下 [FTP 主機名稱] 與 [FTP/部署使用者名稱]。 ![App Service 應用程式詳細資料][8]
> [!NOTE]
> 若未在 [FTP/部署使用者名稱] 下看到任何項目，您必須先使用 [部署認證] 刀鋒視窗設定部署認證。

4.  開啟 [Windows 檔案總管]，在檔案網址列輸入 FTP 主機名稱，例如 ftp://ftp.appservice.azurestack.local
5.  當系統提示您輸入您在步驟 3 記下的 [部署認證] 時，若該功能已啟用，您將會看到 App Service 應用程式內容的目錄清單。 ![FTP 檔案清單][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png

