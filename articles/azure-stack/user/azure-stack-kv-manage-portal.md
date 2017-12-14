---
title: "使用入口網站管理 Azure Stack 中的 Key Vault | Microsoft Docs"
description: "了解如何使用入口網站管理 Azure Stack 中的 Key Vault"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: mabrigg
ms.openlocfilehash: d76a1e188c5a5bf008ac2fba9b43741a6a8d97b1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>使用入口網站管理 Azure Stack 中的 Key Vault

您可以使用 Azure Stack 入口網站管理 Azure Stack 中的金鑰保存庫。 此文章可協助您開始建立和管理 Azure Stack 中的金鑰保存庫。 

## <a name="prerequisites"></a>必要條件  

您必須訂閱包含 Azure Key Vault 服務的供應項目。
 
## <a name="create-a-key-vault"></a>建立金鑰保存庫 

1. 登入[使用者入口網站](https://portal.local.azurestack.external)。  

2. 從儀表板選取 [新增] > [安全性 + 身分識別] > [Key Vault]。  

    ![金鑰保存庫畫面](media/azure-stack-kv-manage-portal/image1.png)  

3. 在 [建立金鑰保存庫] 窗格中，為您的保存庫指派 [名稱]。 保存庫名稱只能包含英數字元和特殊字元連字號 (-)。 不得以數字開頭。  

4. 從可用的訂用帳戶清單中選擇 [訂用帳戶]。 所有提供 Key Vault 服務的訂用帳戶皆會顯示在下拉式清單中。  

5. 選取現有的 [資源群組] 或建立新群組。  

6. 選取 [定價層]。  
    >[!NOTE]
    > Azure Stack 開發套件中的金鑰保存庫僅支援**標準** SKU。

7. 選擇其中一個現有的 [存取原則] 或建立新原則。 存取原則可讓您授與使用者、應用程式或安全性群組權限，來執行此保存庫的作業。  

8. (選用) 選擇 [進階存取原則] 來啟用功能，像是存取虛擬機器 (VM) 進行部署、存取 Resource Manager 進行範本部署，以及存取 Azure 磁碟加密進行磁碟區加密。 
  
9.  在您進行設定之後，請選取 [確定]，然後選取 [建立]。 如此一來，就將會啟動金鑰保存庫部署。 

## <a name="manage-keys-and-secrets"></a>管理金鑰和祕密

在您建立保存庫之後，請使用下列逐步執行來建立並管理保存庫內的金鑰和祕密。

### <a name="create-a-key"></a>建立金鑰

1. 登入[使用者入口網站](https://portal.local.azurestack.external)。  

2. 從儀表板中選取 [所有資源]，選取您稍早建立的金鑰保存庫，然後選取 [金鑰] 圖格。  

3. 在 [金鑰] 窗格中，選取 [新增]。 

4. 在 [建立金鑰] 窗格中，從 [選項] 清單中選擇您要用來建立金鑰的方法。 您可以 [產生] 新的金鑰、[上傳] 現有金鑰，或使用 [還原備份] 來選取金鑰的備份。  

5. 在 [名稱] 中輸入金鑰的名稱。 金鑰名稱只能包含英數字元和特殊字元連字號 (-)。  

6. (選用) 為金鑰設定 [設定啟用日期] 和 [設定到期日期]。  

7. 選取 [建立] 以開始部署。  

成功建立金鑰之後，您可以在 [金鑰] 之下選取該金鑰，並檢視或修改其屬性。 properties 區段包含**金鑰識別碼**，其為外部應用程式可以存取此金鑰的統一資源識別項 (URI)。 若要限制此金鑰的作業，請設定 [允許的作業] 下的設定。

![URI 金鑰](media/azure-stack-kv-manage-portal/image4.png)  

### <a name="create-a-secret"></a>建立祕密 

1. 登入[使用者入口網站](https://portal.local.azurestack.external)。  
2. 從儀表板中選取 [所有資源]，選取您稍早建立的金鑰保存庫，然後選取 [密鑰] 圖格。  

3. 在 [密鑰] 底下，選取 [新增]。  

4. 在 [建立密鑰] 之下，從[上傳選項] 清單中選擇您想要建立密鑰的選項。 如果您輸入密鑰的值或從本機上傳**憑證**，即可**手動**建立密鑰。  

5. 輸入祕密的 [名稱]。 祕密名稱只能包含英數字元和特殊字元連字號 (-)。  

6. (選用) 指定 [內容型別]、為 [設定啟用日期] 設定值，以及為密鑰 [設定到期日期]。  

7. 選取 [建立] 以開始部署。  

成功建立密鑰之後，您可以在 [密鑰] 之下選取該密鑰，並檢視或修改其屬性。 屬性區段包含 [密鑰識別碼]，其為外部應用程式可以存取此密鑰的 URI。 

![URI 祕密](media/azure-stack-kv-manage-portal/image5.png) 


## <a name="next-steps"></a>後續步驟
* [擷取存放在金鑰保存庫中的密碼來部署 VM](azure-stack-kv-deploy-vm-with-secret.md) 
* [使用存放在 Key Vault 中的憑證來部署 VM](azure-stack-kv-push-secret-into-vm.md)     


