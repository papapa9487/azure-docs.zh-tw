---
title: "Azure Stack 開發套件部署快速入門| Microsoft Docs"
description: "了解如何部署 Azure Stack 開發套件"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/10/2017
ms.author: erikje
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 81b6282addd1e88e4146367c4dd9a2ee7b8c84bf
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="azure-stack-development-kit-deployment-quickstart"></a>Azure Stack 開發套件部署快速入門

[Azure Stack 開發套件](azure-stack-poc.md)是一個測試和部署環境，可供您部署以評估及示範 Azure Stack 的功能和服務。 若要讓它啟動並執行，您將需要準備環境硬體並執行一些指令碼 (這將需要數小時的時間)。 之後，您可以登入系統管理員和租用戶入口網站，以管理 Azure Stack 和測試供應項目。 

1. [**規劃您的硬體、軟體及網路**](azure-stack-deploy.md)。 裝載開發套件的電腦 (開發套件主機) 必須符合硬體、軟體及網路需求。 此外，您還必須在使用 Azure Active Directory 或「Active Directory 同盟服務」之間做選擇。 開始部署之前，請務必符合這些先決條件，如此安裝程序才能順暢執行。 

2. [**下載部署套件並解壓縮**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit)。 您可以將部署套件下載到開發套件主機或另一部電腦。 解壓縮後的部署檔案會佔用 60 GB 的可用磁碟空間，因此使用另一部電腦可協助降低開發套件主機的硬體需求。

3. 使用安裝程式來[**準備開發套件主機**](azure-stack-run-powershell-script.md#prepare-the-development-kit-host)。 在此步驟之後，開發套件主機會開機進入 Cloudbuilder.vhdx (一個包含可開機作業系統和 Azure Stack 安裝檔的虛擬硬碟)。

4. 在開發套件主機上[**部署開發套件**](azure-stack-run-powershell-script.md#deploy-the-development-kit)。

5. 如果您的 Azure Stack 部署使用 Azure Active Directory，您就必須[向 Azure 註冊 Azure Stack](azure-stack-register.md)，如此您才能夠[將 Azure Marketplace 項目下載](azure-stack-download-azure-marketplace-item.md)到 Azure Stack。

完成這些步驟之後，您將會擁有一個同時具有系統管理員和租用戶入口網站的開發套件環境。 接著，您可以[連線並登入](azure-stack-connect-azure-stack.md)入口網站。 然後，您可以開始部署資源提供者、建立[供應項目](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions)，以及填入 Azure Stack [市集](azure-stack-marketplace.md)。

