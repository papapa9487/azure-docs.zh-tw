---
title: "Azure AD v2 Windows Desktop 快速入門 - 設定 | Microsoft Docs"
description: "Windows Desktop .NET (XAML) 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4065727aef04d7969d438c6ef79127bb44568be1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
## <a name="set-up-your-project"></a>設定專案

本節提供逐步指示，說明如何建立新的專案來示範整合 Windows Desktop .NET 應用程式 (XAML) 與*使用 Microsoft 登入*，以便它可以查詢需要權杖的 Web API。

本指南建立的應用程式會顯示一個圖表按鈕，並在畫面上顯示結果和一個登出按鈕。

> 想要改為下載此範例的 Visual Studio 專案嗎？ [下載專案](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)並跳至[設定步驟](#create-an-application-express)，以在執行之前先設定程式碼範例。


### <a name="create-your-application"></a>建立您的應用程式
1. 在 Visual Studio 中：`File` > `New` > `Project`<br/>
2. 在 [範本] 底下，選取 `Visual C#`
3. 選取 `WPF App` (或選取 [WPF 應用程式]，需視您的 Visual Studio 版本而定)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>將 Microsoft Authentication Library (MSAL) 新增至您的專案
1. 在 Visual Studio 中：`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. 在 [套件管理器主控台] 視窗中，複製/貼上下列內容：

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> 上面的套件會安裝 Microsoft Authentication Library (MSAL)。 MSAL 會處理使用者權杖的取得、快取及重新整理作業，這些權杖是用來存取受 Azure Active Directory v2 保護的 API。

## <a name="add-the-code-to-initialize-msal"></a>新增程式碼以初始化 MSAL
這個步驟將能協助您建立類別來處理和 MSAL 程式庫的互動，例如處理權杖。

1. 開啟 `App.xaml.cs` 檔案，然後將 MSAL 程式庫參考新增到類別：

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
將應用程式類別更新至下面這樣：
</li>
</ol>

```csharp
public partial class App : Application
{
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";

    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

}
```

## <a name="create-your-applications-ui"></a>建立您應用程式的 UI
下面的小節會說明應用程式如何查詢受保護的後端伺服器 (例如 Microsoft Graph)。 系統應會自動建立 MainWindow.xaml 檔案，作為專案範本的一部分。 請開啟這個檔案，然後依照下面的指示操作：

用以下內容取代您應用程式的 `<Grid>`：

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```
