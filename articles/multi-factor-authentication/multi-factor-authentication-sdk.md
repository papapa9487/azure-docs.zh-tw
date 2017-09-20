---
title: "自訂應用程式的 MFA 軟體開發套件 | Microsoft Docs"
description: "本文示範如何下載和使用 Azure MFA SDK 來啟用自訂應用程式的雙步驟驗證。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 1c152f67-be02-42a5-a0c7-246fb6b34377
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 281f9c61a30a20027f69808600373aa272255ef6
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>在自訂應用程式中建置 Multi-Factor Authentication (SDK)

Azure Multi-Factor Authentication 軟體開發套件 (SDK) 可讓您將雙步驟驗證直接內建到 Azure AD 租用戶中的應用程式登入或交易程序。

Multi-Factor Authentication SDK 適用於 C#、Visual Basic (.NET)、Java、Perl、PHP 和 Ruby。 SDK 為雙步驟驗證加上一層精簡包裝函式。 它包含您撰寫程式碼所需要的一切，包括加註的原始程式碼檔案、範例檔案，以及詳細的 ReadMe 檔案。 每套 SDK 也會包含憑證和私密金鑰，用以加密 Multi-Factor Authentication 提供者獨有的交易。 只要您有提供者，您就可以視需要下載許多種語言和格式的 SDK。

Multi-Factor Authentication SDK 中的 API 結構十分簡單。 使用多因素選項參數 (例如驗證模式) 和使用者資料 (例如要撥打的電話號碼或要驗證的 PIN 碼)，透過單一函式來呼叫 API。 API 會將函式呼叫轉換成 Web 服務要求，再提交到以雲端為基礎的 Azure Multi-Factor Authentication Service。 所有呼叫都必須參考每個 SDK 所包含的私人憑證。

因為 API 無權存取 Azure Active Directory 中註冊的使用者，所以您必須在檔案或資料庫中提供使用者資訊。 API 也沒有提供註冊或使用者管理功能，所以您需要將這些程序內建到您的應用程式中。

> [!IMPORTANT]
> 若要下載 SDK，即使您有 Azure MFA、AAD Premium 或 EMS 授權，還是需要建立 Azure 多因素驗證提供者。 如果您針對此用途建立 Azure 多因素驗證提供者，且已有授權，請務必使用**每個啟用的使用者**模型建立提供者。 然後，將提供者連結至包含 Azure MFA、Azure AD Premium 或 EMS 授權的目錄。 這個組態可確保您只會在使用 SDK 的唯一使用者超過您所擁有的授權數目時收到帳單。


## <a name="download-the-sdk"></a>下載 SDK
下載 Azure Multi-factor SDK 需要 [Azure Multi-Factor Auth Provider](multi-factor-authentication-get-started-auth-provider.md)。  即使您擁有 Azure MFA、Azure AD Premium 或 Enterprise Mobility Suite 授權，這還是需要完整的 Azure 訂用帳戶。  若要下載 SDK，請瀏覽至 Multi-Factor 管理入口網站。 連接入口網站的方式是直接管理多因素驗證提供者，或按一下 MFA 服務設定頁面上的 [移至入口網站] 連結。

### <a name="download-from-the-azure-classic-portal"></a>從 Azure 傳統入口網站下載
1. 以系統管理員身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 選取左邊的 [Active Directory] 。
3. 在 [Active Directory] 頁面頂端，選取 [多因素驗證提供者]
4. 選取底部的 [管理]。 新的頁面隨即開啟。
5. 在左下方按一下 [SDK]。
   <center>![下載](./media/multi-factor-authentication-sdk/download.png)</center>
6. 選取您想要的語言，然後按一下其中一個相關聯的下載連結。
7. 儲存下載內容。

### <a name="download-from-the-service-settings"></a>從服務設定下載
1. 以系統管理員身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 選取左邊的 [Active Directory] 。
3. 按兩下您的 Azure AD 執行個體。
4. 在頂端按一下  **設定**
5. 在多因素驗證底下選取 **管理服務設定**
   ![下載](./media/multi-factor-authentication-sdk/download2.png)
6. 在 [服務設定] 頁面上，於畫面底部按一下 [ **移至入口網站**]。 新的頁面隨即開啟。
   ![下載](./media/multi-factor-authentication-sdk/download3a.png)
7. 在左下方按一下 [SDK]。
8. 選取您想要的語言，然後按一下其中一個相關聯的下載連結。
9. 儲存下載內容。

## <a name="whats-in-the-sdk"></a>SDK 的內容
SDK 包含下列各項：

* **讀我檔案**。 說明如何在新的或現有的應用程式中使用 Multi-Factor Authentication API。
* **原始程式檔**，用於 Multi-Factor Authentication
* **用戶端憑證** 
* **私密金鑰** 
* **呼叫結果。** 呼叫結果碼的清單。 若要開啟此檔案，請使用可設定文字格式的應用程式，例如 WordPad。 使用呼叫結果碼來測試及疑難排解您在應用程式中的 Multi-Factor Authentication 實作。 它們不是驗證狀態碼。
* **範例。** Multi-Factor Authentication 基本工作實作的範例程式碼。

> [!WARNING]
> 用戶端憑證是特別為您產生的唯一私人憑證。 請勿分享或遺失此檔案。 它是您與 Multi-Factor Authentication 通訊時確保安全性的關鍵。

## <a name="code-sample"></a>程式碼範例
此程式碼範例示範如何使用 Azure Multi-Factor Authentication SDK 中的 API，將標準模式語音通話驗證加入至您的應用程式。 標準模式是指使用者按下 # 鍵來回應通話。

此範例在含有 C# 伺服器端邏輯的基本 ASP.NET 應用程式中使用 C# .NET 2.0 Multi-Factor Authentication SDK，但程序與其他語言類似。 因為 SDK 包含原始程式檔，而不是可執行檔，您可以建置檔案並參考它們，或直接將它們包含在您的應用程式中。

> [!NOTE]
> 實作 Multi-Factor Authentication 時，請使用其他方法 (通話或簡訊) 做為第二或第三驗證，以補充您的主要驗證方法 (使用者名稱和密碼)。 這些方法並非設計為主要驗證方法。

### <a name="code-sample-overview"></a>程式碼範例概觀
這是簡單 Web 示範應用程式的範例程式碼，使用 # 鍵回應通話來驗證使用者驗證。 此通話因素在 Multi-Factor Authentication 中稱為標準模式。

用戶端程式碼不包含任何 Multi-Factor Authentication 特定項目。 因為其他驗證因素與主要驗證無關，您可以新增它們而不必變更現有登入介面。 Multi-Factor SDK 中的 API 可讓您自訂使用者經驗，但您可能完全不需要變更任何項目。

伺服器端程式碼在步驟 2 新增標準模式驗證。 它使用標準模式驗證所需的參數建立 PfAuthParams 物件：使用者名稱、電話號碼、模式，以及每個呼叫都需要的用戶端憑證的路徑 (CertFilePath)。 如需 PfAuthParams 中的所有參數的示範，請參閱 SDK 中的範例檔案。

接下來，程式碼將 PfAuthParams 物件傳遞至 pf_authenticate() 函式。 傳回值指出驗證成功或失敗。 Out 參數 callStatus 和 errorID 包含其他通話結果資訊。 通話結果碼會記錄在 SDK 中的通話結果檔案。

只需要幾行就能撰寫這個最小實作。 不過，在實際執行的程式碼中，將會包含更複雜的錯誤處理、其他資料庫程式碼和強化的使用者經驗。

### <a name="web-client-code"></a>Web 用戶端程式碼
以下是示範頁面的 Web 用戶端程式碼。

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>伺服器端程式碼
在下列伺服器端程式碼中，步驟 2 中設定並執行 Multi-Factor Authentication。 標準模式 (MODE_STANDARD) 是指使用者按下 # 鍵來回應通話。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class \_Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "5555555555";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = "Multi-Factor Authentication failed.";
                }
            }

        }
    }

