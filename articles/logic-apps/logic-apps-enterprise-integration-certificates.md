---
title: "使用憑證搭配企業整合套件 | Microsoft Docs"
description: "了解如何使用憑證搭配企業整合套件 | Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 0570aab14283b38f9efcc50636f0c0c1c8e3ed13
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>了解憑證與企業整合套件
## <a name="overview"></a>Overview
企業整合使用憑證來保護 B2B 通訊的安全。 您可以在企業整合應用程式中使用兩種憑證類型：

* 公開憑證，必須先向憑證授權單位 (CA) 購買。
* 私人憑證，您可以自行核發。 這些憑證有時也稱為自我簽署憑證。

## <a name="what-are-certificates"></a>什麼是憑證？
憑證是數位文件，可驗證電子通訊參與者的身分識別，同時還能保護電子通訊的安全。

## <a name="why-use-certificates"></a>為什麼要使用憑證？
B2B 通訊有時必須確保機密。 企業整合透過下列兩種方式，使用憑證來保護這些通訊的安全：

* 將訊息的內容加密
* 以數位方式簽署訊息  

## <a name="upload-a-public-certificate"></a>上傳公開憑證

若要在具備 B2B 功能的 Logic Apps 中使用「公開憑證」  ，您必須先將憑證上傳到整合帳戶。  

上傳憑證之後，當您在自己建立的 [合約](logic-apps-enterprise-integration-agreements.md) 中定義 B2B 訊息的屬性時，就能使用它來保護這些訊息的安全。  

以下是您登入 Azure 入口網站後，將公開憑證上傳到整合帳戶的詳細步驟：

1. 選取 [更多服務]，然後在篩選搜尋方塊中輸入**整合**。 從結果清單中選取 [整合帳戶]     
![選取瀏覽](media/logic-apps-enterprise-integration-certificates/overview-1.png)  
2. 選取要將憑證新增到的整合帳戶。  
![選取要將憑證新增到的整合帳戶](media/logic-apps-enterprise-integration-certificates/overview-3.png)  
3. 選取 [憑證]  圖格。  
![選取憑證圖格](media/logic-apps-enterprise-integration-certificates/certificate-1.png)
4. 在開啟的 [憑證] 刀鋒視窗中選取 [新增] 按鈕。   
![選取新增按鈕](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
5. 輸入憑證的**名稱**，然後從下拉式清單中選取 [公用] 憑證類型。  
6. 選取 [憑證] 文字方塊右邊的資料夾圖示。 當檔案選擇器開啟時，尋找並選取您要上傳到整合帳戶的憑證檔案。
7. 選取憑證，然後在檔案選擇器中選取 [確定]  。 這會驗證憑證並上傳到您的整合帳戶。
8. 最後，返回 [新增憑證] 刀鋒視窗，然後選取 [確定] 按鈕。  
![選取確定按鈕](media/logic-apps-enterprise-integration-certificates/certificate-3.png)  
9. 選取 [憑證]  圖格。 您應該會看到新增的憑證。  
![查看新憑證](media/logic-apps-enterprise-integration-certificates/certificate-4.png)  

## <a name="upload-a-private-certificate"></a>上傳私人憑證

若要在具有 B2B 功能的邏輯應用程式中使用「私人憑證」，您可以執行下列步驟以將私人憑證上傳到您的整合帳戶

1. [將您的私人金鑰上傳到 Key Vault](../key-vault/key-vault-get-started.md "了解 Key Vault") 並提供**金鑰名稱** 
   
   > [!TIP]
   > 您必須授權 Logic Apps 以便對 Key Vault 執行作業。 您可以使用下列 PowerShell 命令授與 Logic Apps 服務主體的存取權︰ `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 

在您執行上一個步驟之後，請將私人憑證新增到整合帳戶。

以下是您登入 Azure 入口網站後，將私人憑證上傳到整合帳戶的詳細步驟：  
 
1. 選取要將憑證新增到其中的整合帳戶，然後選取 [憑證] 圖格。  
![選取憑證圖格](media/logic-apps-enterprise-integration-certificates/certificate-1.png)  
2. 在開啟的 [憑證] 刀鋒視窗中選取 [新增] 按鈕。   
![選取新增按鈕](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
3. 輸入憑證的**名稱**，然後從下拉式清單中選取 [私人] 憑證類型。   
4. 選取 [憑證] 文字方塊右邊的資料夾圖示。 當檔案選擇器開啟時，尋找您要上傳到整合帳戶的對應公開憑證。   
   
   > [!Note]
   > 新增私人憑證時，請務必新增要在 [AS2 合約](logic-apps-enterprise-integration-as2.md)接收和傳送設定中顯示的對應公開憑證，以用來簽署與加密訊息。
   > 
   >   

5. 選取 [資源群組]、[Key Vault]、[金鑰名稱]，然後選取 [確定] 按鈕。  
![新增憑證](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)  
6. 選取 [憑證]  圖格。 您應該會看到新增的憑證。
![查看新憑證](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)  



* [建立 B2B 合約](logic-apps-enterprise-integration-agreements.md)  
* [深入了解金鑰保存庫](../key-vault/key-vault-get-started.md "了解金鑰保存庫")  

