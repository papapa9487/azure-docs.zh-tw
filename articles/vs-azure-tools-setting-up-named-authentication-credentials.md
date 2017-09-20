---
title: "設定具名的驗證認證 | Microsoft Docs"
description: "了解如何提供 Visual Studio 可用來向 Azure 驗證要求的認證，以便您可以將應用程式從 Visual Studio 發佈至 Azure，或用來監視現有的雲端服務。"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/22/2017
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: b75d190724da284324b0337e95a50a64902e19d8
ms.contentlocale: zh-tw
ms.lasthandoff: 09/06/2017

---
# <a name="set-up-named-authentication-credentials"></a>設定具名的驗證認證
若要將應用程式從 Visual Studio 發佈至 Azure，或若要監視現有的雲端服務，您必須提供 Visual Studio 可用來向 Azure 驗證要求的認證。 您可以從 Visual Studio 中的幾個位置登入並提供這些認證。 例如，從 [伺服器總管] 中，您可以開啟 [Azure] 節點的捷徑功能表，並選取 [連線至 Microsoft Azure 訂用帳戶]。 登入時，您可以在 Visual Studio 中取得與 Azure 帳戶相關聯的訂用帳戶資訊。 而且接下來您無需執行任何動作。

Azure Tools 也支援舊式的認證提供方法：使用訂用帳戶檔案 (.publishsettings 檔案)。 本文會說明此方法，Azure SDK 2.2 中仍支援此方法。

向 Azure 驗證需要下列項目：

* 訂用帳戶識別碼
* 有效的 X.509 v3 憑證

> [!NOTE]
> X.509 v3 憑證的金鑰長度必須至少為 2,048 位元。 Azure 會拒絕任何不符合此需求或無效的憑證。
>
>

Visual Studio 會使用您的訂用帳戶識別碼連同憑證資料做為認證。 包含憑證公開金鑰的訂用帳戶檔案 (.publishsettings 檔案) 會參考適當的認證。 訂用帳戶檔案可以包含多個訂用帳戶的認證。

您可以在 [新增訂用帳戶] 或 [編輯訂用帳戶] 對話方塊中編輯訂用帳戶資訊，本文稍後會有說明。

如果您想要自行建立憑證，您可以參考[建立及上傳 Azure 的管理憑證](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx)中的指示，然後手動將憑證上傳至 [Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)。

> [!NOTE]
> Visual Studio 為管理雲端服務所需的這些認證，與針對 Azure 儲存體服務驗證要求所需的認證是不同的。
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>在 Visual Studio 中設匯入、設定或編輯驗證認證

1. 在 [伺服器總管] 中，開啟 [Azure] 節點的捷徑功能表，然後選取 [管理及篩選訂閱]。
2. 選取 [憑證] 索引標籤，然後使用下列任何方法：

    * 選取 [匯入] 以開啟 [匯入 Microsoft Azure 訂用帳戶] 對話方塊。 您可以從該對話方塊為目前載入的訂用帳戶下載訂用帳戶檔案、瀏覽至其下載位置，再加以匯入以用於驗證。
    * 選取 [新增] 以開啟 [新增訂用帳戶] 對話方塊。 您可以從該對話方塊設定用於驗證的新訂用帳戶。
    * 選取 [編輯] \(在選擇作用中的訂用帳戶之後) 以開啟 [編輯訂用帳戶] 對話方塊。 您可以從該對話方塊編輯用於驗證的現有訂用帳戶。 

下列程序假設 [新增訂用帳戶]  對話方塊已開啟。

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>在 Visual Studio 中設定驗證認證
1. 在驗證清單的 [選取現有憑證] 中選擇憑證。
2. 選取 [複製完整路徑] 連結。 憑證 (.cer 檔案) 的路徑便會複製到剪貼簿。

   > [!IMPORTANT]
   > 若要從 Visual Studio 發佈 Azure 應用程式，您必須將此憑證上傳至 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
   >
   >
3. 將憑證上傳到 Azure 入口網站：

   a. 開啟 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
   
   b. 如果出現提示，請登入入口網站，然後瀏覽至 [設定] > [管理憑證]。
   
   c. 在 [管理憑證] 窗格中，選取 [上傳]。
   
   d. 選取您的 Azure 訂用帳戶，貼上您剛剛建立之 .cer 檔案的完整路徑，並選取 [上傳]。

## <a name="next-steps"></a>後續步驟
* [Web Apps 的一般概觀](https://docs.microsoft.com/azure/app-service-web/)
* [將您的應用程式部署至 Azure App Service](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy?toc=%2fazure%2fapp-service-api%2ftoc.json) 
* [使用 Visual Studio 部署 WebJob](https://docs.microsoft.com/en-us/azure/app-service-web/websites-dotnet-deploy-webjobs)
* [建立及部署雲端服務](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)
