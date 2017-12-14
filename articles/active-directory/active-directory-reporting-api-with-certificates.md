---
title: "使用 Azure AD 報告 API 搭配憑證來取得資料 | Microsoft Docs"
description: "說明如何使用 Azure AD 報告 API 搭配憑證認證來取得目錄中的資料，而不需使用者介入。"
services: active-directory
documentationcenter: 
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/08/2017
ms.author: ramical
ms.openlocfilehash: 4900e47084256ad6c85886f7ba363399678da9aa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2017
---
# <a name="get-data-using-the-azure-ad-reporting-api-with-certificates"></a>使用 Azure AD 報告 API 搭配憑證來取得資料
本文討論如何使用 Azure AD 報告 API 搭配憑證認證來取得目錄中的資料，而不需使用者介入。 

## <a name="use-the-azure-ad-reporting-api"></a>使用 Azure AD 報告 API 
Azure AD Reporting API 會要求您完成下列步驟：
 *  安裝必要條件
 *  在您的應用程式中設定憑證
 *  取得存取權杖
 *  使用存取權杖來呼叫圖形 API

如需原始程式碼的相關資訊，請參閱[運用報告 API 模組](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils)。 

### <a name="install-prerequisites"></a>安裝必要條件
您必須已安裝 Azure AD PowerShell V2 和 AzureADUtils 模組。

1. 遵循 [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md) 的指示，下載並安裝 Azure AD Powershell V2。
2. 從 [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1) 下載 Azure AD Utils 模組。 
  此模組會提供數個公用程式 Cmdlet，包括︰
   * 最新版的 ADAL (使用 Nuget)
   * 從使用者、應用程式金鑰和憑證存取權杖 (使用 ADAL)
   * 處理分頁結果的圖形 API

**若要安裝 Azure AD Utils 模組：**

1. 建立目錄來儲存公用程式模組 (例如 c:\azureAD) 並從 GitHub 下載此模組。
2. 開啟 PowerShell 工作階段，然後移至您剛建立的目錄。 
3. 匯入模組，並使用 Install-AzureADUtilsModule Cmdlet 將它安裝於 PowerShell 模組路徑中。 

此工作階段看起來應類似這個畫面：

  ![Windows Powershell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

### <a name="set-the-certificate-in-your-app"></a>在您的應用程式中設定憑證
1. 如果您已經有應用程式，請從 Azure 入口網站取得其物件識別碼。 

  ![Azure 入口網站](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. 開啟 PowerShell 工作階段並使用 Connect-AzureAD Cmdlet 連線至 Azure AD。

  ![Azure 入口網站](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. 使用 AzureADUtils 中的 New-AzureADApplicationCertificateCredential Cmdlet，將憑證認證新增至該模組。 

>[!Note]
>您必須提供先前擷取的應用程式物件識別碼，以及憑證物件 (使用 Cert: 磁碟機取得此物件)。
>


  ![Azure 入口網站](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
### <a name="get-an-access-token"></a>取得存取權杖

若要取得存取權杖，請使用 AzureADUtils 中的 Get-AzureADGraphAPIAccessTokenFromCert Cmdlet。 

>[!NOTE]
>您必須使用應用程式識別碼，而不是您在上一節中使用的物件識別碼。
>

 ![Azure 入口網站](./media/active-directory-report-api-with-certificates/application-id.png)

### <a name="use-the-access-token-to-call-the-graph-api"></a>使用存取權杖來呼叫圖形 API

您現在可以建立指令碼。 以下是使用 AzureADUtils 中 Invoke-AzureADGraphAPIQuery Cmdlet 的範例。 這個 Cmdlet 可處理多個分頁結果，然後再將這些結果傳送至 PowerShell 管線。 

 ![Azure 入口網站](./media/active-directory-report-api-with-certificates/script-completed.png)

您現在已準備好匯出至 CSV 並儲存至 SIEM 系統。 您也可以在排定的工作中包裝您的指令碼，以便定期從租用戶取得 Azure AD 資料，而不必將應用程式金鑰儲存在原始程式碼中。 

## <a name="next-steps"></a>後續步驟
[Azure 身分識別管理的基本概念](https://docs.microsoft.com/azure/active-directory/fundamentals-identity)<br>



