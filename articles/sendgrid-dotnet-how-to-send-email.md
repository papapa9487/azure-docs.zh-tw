---
title: "如何使用 SendGrid 電子郵件服務 (.NET) (.NET) | Microsoft Docs"
description: "了解如何在 Azure 使用 SendGrid 電子郵件服務傳送電子郵件。 程式碼範例是以 C# 撰寫並使用 .NET API。"
services: 
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: 
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 1ba8debc1ccc827bc242cdc7a7ab484a85d3c0ae
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>如何在 Azure 上使用 SendGrid 傳送電子郵件
## <a name="overview"></a>概觀
本指南示範如何在 Azure 上透過 SendGrid 電子郵件服務執行常見程式設計工作。 這些範例均以 C\# 撰寫，且支援 .NET Standard 1.3。 涵蓋的案例包括建構電子郵件、傳送電子郵件、新增附件以及啟用各種郵件和追蹤設定。 如需有關 SendGrid 及傳送電子郵件的詳細資訊，請參閱[後續步驟][Next steps]一節。

## <a name="what-is-the-sendgrid-email-service"></a>什麼是 SendGrid 電子郵件服務？
SendGrid 是 [雲端架構電子郵件服務]，能提供可靠的 [交易式電子郵件傳遞]、擴充性和即時分析，以及有彈性的 API 來輕鬆進行自訂整合。 常見的 SendGrid 使用案例包括︰

* 自動將收據或採購確認傳送給客戶。
* 管理通訊群組清單，以便將每月傳單和促銷傳送給客戶。
* 收集封鎖的電子郵件和客戶參與情形等項目的即時計量。
* 轉寄客戶查詢。
* 處理內送電子郵件。

如需詳細資訊，請造訪 [https://sendgrid.com](https://sendgrid.com) 或 SendGrid 的 [C# 程式庫][sendgrid-csharp] GitHub 儲存機制。

## <a name="create-a-sendgrid-account"></a>建立 SendGrid 帳戶
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>參考 SendGrid .NET 類別庫
[SendGrid NuGet 封裝](https://www.nuget.org/packages/Sendgrid) 是取得 SendGrid API 及透過所有相依性設定應用程式的最簡單方式。 NuGet 是 Microsoft Visual Studio 2015 和更新版本隨附的 Visual Studio 擴充，能輕鬆地安裝及更新程式庫和工具。

> [!NOTE]
> 如果您是執行 Visual Studio 2015 之前的 Visual Studio 版本，若要安裝 NuGet，請造訪 [http://www.nuget.org](http://www.nuget.org)，然後按一下 **安裝 NuGet** 按鈕。
>
>

若要在應用程式中安裝 SendGrid NuGet 封裝，請執行下列動作：

1. 按一下 [新增專案]，然後選取 [範本]。

   ![建立新專案][create-new-project]
2. 在 [方案總管] 中，以滑鼠右鍵按一下 [參考]，然後按一下 [管理 NuGet 套件]。

   ![SendGrid NuGet 封裝][SendGrid-NuGet-package]
3. 搜尋 **SendGrid**，然後選取結果清單中的 [SendGrid] 項目。
4. 從版本下拉式清單中選取 Nuget 套件的最新穩定版本，以搭配本文示範的物件模型和 API 共同使用。

   ![SendGrid 套件][sendgrid-package]
5. 按一下 [安裝]  完成安裝，然後關閉此對話方塊。

SendGrid 的 .NET 類別庫稱為 **SendGrid**。 其中包含下列命名空間：

* **SendGrid** 用以與 SendGrid 的 API 進行通訊。
* **SendGrid.Helpers.Mail** 讓協助程式方法可以輕鬆建立 SendGridMessage 物件，以指定如何傳送電子郵件。

將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 SendGrid 電子郵件服務之任何 C# 檔案內的頂端。

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>如何：建立電子郵件
使用 **SendGridMessage** 物件來建立電子郵件訊息。 建立訊息物件後，即可設定屬性和方法，包括電子郵件寄件者、電子郵件收件者以及電子郵件的主旨和本文。

下列範例示範如何建立完全填入的電子郵件物件：

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

如需 **SendGrid** 類型支援的所有屬性和方法的詳細資訊，請參閱 GitHub 上的 [sendgrid-csharp][sendgrid-csharp]。

## <a name="how-to-send-an-email"></a>如何：傳送電子郵件
建立電子郵件訊息之後，您可以使用 SendGrid 的 API 進行傳送。 或者，您也可以使用 [.NET 的內建程式庫][NET-library]。

傳送電子郵件需要您提供 SendGrid API 金鑰。 如需有關如何設定 API 金鑰的詳細資訊，請參閱 SendGrid 的 API 金鑰[文件][documentation]。

您可以透過 Azure 入口網站儲存這些認證，只要按一下 [應用程式設定]，並在 [應用程式設定] 下新增金鑰/值組。

 ![Azure 應用程式設定][azure_app_settings]

 然後，您可以使用下列方式進行存取：

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

下列範例顯示如何使用 Web API 傳送郵件。

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }

## <a name="how-to-add-an-attachment"></a>如何：新增附件
呼叫 **AddAttachment** 方法並最小指定您要附加的檔案名稱和 Base64 編碼內容，即可將附件新增至訊息。 您可以對想要附加的每個檔案呼叫一次此方法，或是使用 **AddAttachments** 方法，即可包含多個附件。 下列範例示範如何將附件新增至郵件：

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>如何：使用郵件設定來啟用頁尾、追蹤和分析
透過使用郵件設定和追蹤設定，SendGrid 提供了其他電子郵件功能。 這些設定可新增至電子郵件訊息以啟用特定功能，例如點選追蹤、Google 分析、訂用帳戶追蹤等等。 如需應用程式的完整清單，請參閱[設定文件][settings-documentation]。

使用與 **SendGridMessage** 類別一起實作的方法，即可將應用程式套用至 **SendGrid** 電子郵件訊息。 下列範例示範頁尾和點選追蹤篩選器：

下列範例示範頁尾和點選追蹤篩選器：

### <a name="footer-settings"></a>頁尾設定
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>點選追蹤
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>如何：使用其他 SendGrid 服務
SendGrid 提供多個 API 與 Webhook，可供您在 Azure 應用程式中運用其他功能。 如需詳細資訊，請參閱 [SendGrid API 參考][SendGrid API documentation]。

## <a name="next-steps"></a>後續步驟
了解 SendGrid 電子郵件服務的基本概念後，請參考下列連結以取得更多資訊。

* SendGrid C\# 程式庫儲存機制：[sendgrid-csharp][sendgrid-csharp]
* SendGrid API 文件︰<https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[雲端架構電子郵件服務]: https://sendgrid.com/solutions
[交易式電子郵件傳遞]: https://sendgrid.com/use-cases/transactional-email


