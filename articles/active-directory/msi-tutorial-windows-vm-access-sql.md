---
title: "使用 Windows VM MSI 存取 Azure SQL"
description: "此教學課程引導您使用 Windows VM 受管理的服務身分識別 (MSI) 來存取 Azure SQL 的程序。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: skwan
ms.openlocfilehash: 1b13255c2bbb0d97c33851b89d071036c47e9cfa
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>使用 Windows VM 受管理的服務身分識別 (MSI) 來存取 Azure SQL

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教學課程示範如何使用 Windows 虛擬機器 (VM) 的受管理服務身分識別 (MSI) 來存取 Azure SQL 伺服器。 受管理的服務識別由 Azure 自動管理，並可讓您驗證支援 Azure AD 驗證的服務，而不需要將認證插入程式碼中。 您會了解如何：

> [!div class="checklist"]
> * 在 Windows VM 上啟用 MSI 
> * 將您的 VM 存取權授與 Azure SQL 伺服器
> * 使用 VM 身分識別取得存取權杖，並使用它查詢 Azure SQL 伺服器


如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>在新的資源群組中建立 Windows 虛擬機器

此教學課程中，我們會建立新的 Windows VM。  您也可以在現有的 VM 中啟用 MSI。

1.  按一下 Azure 入口網站左上角的 [新增] 按鈕。
2.  選取 [計算]，然後選取 [Windows Server 2016 Datacenter]。 
3.  輸入虛擬機器資訊。 在此建立的**使用者名稱**和**密碼**是您登入虛擬機器要使用的認證。
4.  在下拉式清單中選擇適用於虛擬機器的適當**訂用帳戶**。
5.  若要選取要在其中建立虛擬機器的新 [資源群組]，請選擇 [新建]。 完成時，按一下 [確定]。
6.  選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 在 [設定] 頁面上，保留預設值並按一下 [確定]。

    ![替代映像文字](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>在您的 VM 上啟用 MSI 

VM MSI 可讓您從 Azure AD 取得存取權杖，而不需要將憑證放入您的程式碼。 啟用 MSI 會告訴 Azure 為您的 VM 建立受管理的身分識別。 實際上，啟用 MSI 會執行兩項工作：在您的 VM 上安裝 MSI VM 延伸模組，並在 Azure Resource Manager 中啟用 MSI。

1.  選取您想要在其中啟用 MSI 的 [虛擬機器]。  
2.  在左側的導覽列上，按一下 [設定] 。 
3.  您會看到**受管理的服務識別**。 若要註冊並啟用 MSI，請選取 [是]，如果您想要將它停用，則請選擇 [否]。 
4.  確認按一下 [儲存] 儲存設定。  
    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 如果您想要檢查並確認哪些延伸模組會在此 VM 上，請按一下 [延伸模組]。 如果 MSI 已啟用，則 **ManagedIdentityExtensionforWindows** 會出現在清單中。

    ![替代映像文字](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>將您的 VM 存取權授與 Azure SQL 伺服器中的資料庫

現，在您可以將 VM 存取權授與 Azure SQL 伺服器中的資料庫。  在這個步驟，您可以使用現有的 SQL 伺服器或建立新的。  若要使用 Azure 入口網站建立新的伺服器和資料庫，請遵循此 [Azure SQL 快速入門](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)的作法。 在 [Azure SQL 文件](https://docs.microsoft.com/azure/sql-database/)中也有使用 Azure CLI 和 Azure PowerShell 的快速入門作法。

將 VM 存取權授與資料庫有三個步驟：
1.  在 Azure AD 中建立群組，並讓 VM MSI 成為此群組的成員。
2.  啟用 SQL Server 的 Azure AD 驗證。
3.  在代表 Azure AD 群組的資料庫中建立**包含的使用者**。

> [!NOTE]
> 通常您會建立直接對應到 VM MSI 的「包含的使用者」。  現階段，Azure SQL 不允許代表 VM MSI 的 Azure AD 服務主體對應到包含的使用者。  支援的因應做法是，讓 VM 的 MSI 成為 Azure AD 群組的成員，然後在代表該 群組的資料庫中建立包含的使用者。


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>在 Azure AD 中建立群組，並讓 VM MSI 成為此群組的成員

您可以使用現有的 Azure AD 群組，或使用 Azure AD PowerShell 建立一個新群組。  

首先，安裝 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 模組。 然後使用 `Connect-AzureAD` 登入，執行下列命令來建立群組，並將它儲存在變數中：

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

輸出看起來像下面這樣，它也會檢查變數的值：

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

接下來，將 VM 的 MSI 新增至群組。  您需要的 MSI 的 **ObjectId**，可以使用 Azure PowerShell 取得此值。  首先，下載 [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 然後使用 `Login-AzureRmAccount` 登入，執行下列命令來達成這些事項︰
- 確定工作階段內容設為所需的 Azure 訂用帳戶 (如果您有多個訂用帳戶)。
- 列出 Azure 訂用帳戶中的可用資源，確認資源群組和 VM 名稱正確。
- 使用適當的 `<RESOURCE-GROUP>`和 `<VM-NAME>` 值取得 MSI VM 的屬性。

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

輸出看起來像下面這樣，它也會檢查VM MSI 的服務主體物件識別碼：
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

現在，將 VM MSI 新增至群組。  使用 Azure AD PowerShell，可以只將一個服務主體新增至群組。  請執行這個命令：
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

如果您在之後也檢查群組成員資格，輸出看起來會像這樣：

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>啟用 SQL Server 的 Azure AD 驗證

現在，您已建立群組並將 VM MSI 新增為成員，可以使用下列步驟[設定 SQL Server 的 Azure AD 驗證](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server)：

1.  在 Azure 入口網站中，選取左側導覽中的 [SQL 伺服器]。
2.  按一下要啟用 Azure AD 驗證的 SQL 伺服器。
3.  在刀鋒視窗的 [設定] 區段中，按一下 [Active Directory 系統管理員]。
4.  在命令列中，按一下 [設定系統管理員]。
5.  選取要設為伺服器系統管理員的 Azure AD 使用者帳戶，然後按一下 [選取]。
6.  在命令列中，按一下 [儲存]。

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>在代表 Azure AD 群組的資料庫中建立包含的使用者

在這一個步驟中，您需要 [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。 在開始之前，先閱讀以下文章了解 Azure AD 整合的背景會很有幫助：

- [SQL Database 和 SQL 資料倉儲的通用驗證 (MFA 的 SSMS 支援)](/azure/sql-database/sql-database-ssms-mfa-authentication.md)
- [使用 SQL Database 或 SQL 資料倉儲設定和管理 Azure Active Directory 驗證](/azure/sql-database/sql-database-aad-authentication-configure.md)

1.  啟動 SQL Server Management Studio。
2.  在 [連線到伺服器] 對話方塊中，在 [伺服器名稱] 欄位中輸入您的 SQL 伺服器名稱。
3.  在 [驗證] 欄位中，選取 [具 MFA 支援的 Active Directory - 通用]。
4.  在 [使用者名稱] 欄位中，輸入您設為伺服器系統管理員的 Azure AD 帳戶名稱，例如 helen@woodgroveonline.com。
5.  按一下 [選項] 。
6.  在 [連 線至資料庫] 欄位中，輸入您想要設定的非系統資料庫的名稱。
7.  按一下 [ **連接**]。  完成登入程序。
8.  在 [物件總管] 中展開 [資料庫] 資料夾。
9.  在使用者資料庫上按一下滑鼠右鍵，然後按一下 [新增查詢]。
10.  在查詢視窗中，輸入下列這一行，然後按一下工具列中的 [執行]︰
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     命令應該會順利完成，為群組建立包含的使用者。
11.  清除查詢視窗，輸入下列這一行，然後按一下工具列中的 [執行]︰
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     命令應該會順利完成，將讀取整個資料庫的能力授與包含的使用者。

在 VM 中執行的程式碼現在可以從 MSI 取得權杖，並使用權杖來向 SQL 伺服器進行驗證。

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>使用 VM 身分識別取得存取權杖，並使用它來呼叫 Azure SQL 

Azure SQL 原生支援 Azure AD 驗證，因此可以直接接受使用 MSI 取得的存取權杖。  您使用 **access token** 方法來建立 SQL 連線。  這是 Azure AD 與 Azure SQL 整合的一部分，與在連接字串上提供認證不同。

以下是使用存取權杖開啟 SQL 連線的 .Net 程式碼範例。  此程式碼必須在 VM 上執行，才能夠存取 VM MSI 端點。  需使用 **.Net framework 4.6** 或更高版本，才可使用的 access token 方法。  將 AZURE-SQL-SERVERNAME 和 DATABASE 的值取代為實際值。  請注意，Azure SQL 的資源識別碼是 "https://database.windows.net/"。

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call MSI endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

或者，另一個測試端對端安裝程式，而不需要在 VM 上撰寫和部署應用程式的快速方法，是使用 PowerShell。

1.  在入口網站中，瀏覽至 [虛擬機器] 並移至您的 Windows 虛擬機器，在 [概觀] 中按一下 [連線]。 
2.  輸入您建立 Windows VM 時新增的**使用者名稱**和**密碼**。 
3.  現在您已經建立虛擬機器的**遠端桌面連線**，請在遠端工作階段中開啟 **PowerShell**。 
4.  使用 Powershell 的 `Invoke-WebRequest`，向本機 MSI 端點提出要求取得 Azure SQL 的存取權杖。

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://database.windows.net/"} -Headers @{Metadata="true"}
    ```
    
    將來自 JSON 物件的回應轉換為 PowerShell 物件。 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    擷取回應中的存取權杖。
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  開啟 SQL伺服器連線。 記得取代 AZURE-SQL-SERVERNAME 和 DATABASE 的值。
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    接下來，建立查詢並傳送至伺服器。  記得取代 TABLE 的值。

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

檢查 `$DataSet.Tables[0]` 的值以檢視查詢的結果。  恭喜，您已成功使用 VM MSI 查詢 資料庫，而且不需要提供認證 ！

## <a name="related-content"></a>相關內容

- 如需 MSI 的概觀，請參閱[受管理的服務識別概觀](../active-directory/msi-overview.md)。
- 深入了解 [Azure AD 驗證的 Azure SQL 支援](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)。
- 深入了解[設定 Azure AD 驗證的 Azure SQL 支援](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)。
- 深入了解 [SQL 伺服器的驗證和存取](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
