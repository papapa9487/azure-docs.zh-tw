---
title: "Cortana Intelligence AppSource 發佈指南 | Microsoft Docs"
description: "身為 Microsoft 合作夥伴，以下是將您的 Cortana Intelligence 解決方案發佈至 AppSource 所需遵循的所有步驟。"
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams;v-bruham;garye
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 9f867641b77c8148c1d6cbf2913da9c1f5ce5b71
ms.contentlocale: zh-tw
ms.lasthandoff: 07/08/2017

--- 
# <a name="cortana-intelligence-appsource-publishing-guide"></a>Cortana Intelligence AppSource 發佈指南

## <a name="overview"></a>概觀
AppSource 是業務決策者 (BDM) 能探索及順利嘗試由合作夥伴所建置，並由 Microsoft 所評估之業務解決方案/應用程式的單一目的地。 觀看[這段影片](https://youtu.be/hpq_Y9LuIB8) \(英文\) 以了解 AppSource 的運作方式。 

身為 Microsoft 合作夥伴，如果您符合以下項目，即可透過在 AppSource 上發佈獲得各種好處：
- 使用 [Cortana Intelligence 套件](https://azure.microsoft.com/en-us/suites/cortana-intelligence-suite/?cdn=disable)建置智慧型解決方案/應用程式。
- 您的解決方案或應用程式可解決特定業務問題。
- 您所建置的模組或智慧財產，允許客戶以可預測的方式相對快速地重複使用。

請查看 AppSource 上已經發佈的 [Cortana Intelligence 解決方案](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1)。 

本文將逐步說明將合作夥伴的 Cortana Intelligence 解決方案發佈至 AppSource 的所有步驟

## <a name="getting-started"></a>開始使用
1. 在[合作夥伴社群優點指南](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf) \(PDF\) \(英文\) 中，請參閱第 9 頁以了解被列入進階分析合作夥伴的方式。
1. 完成[提交您的應用程式](https://appsource.microsoft.com/en-us/partners/list-an-app)表單。

    針對「選擇您的應用程式為什麼產品打造」此問題，請選取 [其他] 核取方塊，並在編輯控制中列出「Cortana Intelligence」。
1. 在 Cortana Intelligence 應用程式可以上線至 AppSource 之前，它必須先取得 Cortana Intelligence 的合作夥伴解決方案技術小組的認證。 若要開始該程序，請填寫[針對 AppSource 發佈的 Cortana Intelligence 解決方案評估](https://aka.ms/cisappsrceval) \(英文\) 問卷表單，分享關於您應用程式的詳細資料。 此網站有密碼保護，並且有如何取得密碼的指示。

## <a name="solution-evaluation-criteria"></a>解決方案評估準則
下列是應用程式必須符合的準則清單
1. 應用程式必須能在特定的功能領域內，以重複的方式解決特定的商務使用案例問題，且針對預先定義的價值主張可在短時間內以最低限度的設定完成實作。
1. 解決方案應至少使用下列元件其中一種：

    - HDInsight
    - 機器學習服務
    - Data Lake Analytics
    - 串流分析
    - 辨識服務
    - Bot Framework
    - Analysis Services
    - 獨立 Microsoft R 伺服器
    - SQL 2016 上的 R 服務或 HDInsight Premium
1. 針對使用 DPOR/CSP 的每個客戶，解決方案每個月應至少產生 $1000 美金的金額。
1. 解決方案應使用 Azure Active Directory 同盟單一登入 (AAD 同盟 SSO)，並允許啟用使用者驗證和資源存取控制。 您將必須向評估小組示範您的解決方案已啟用 AAD 同盟 SSO，您的應用程式才能上架到 AppSource。

     若要了解什麼是已啟用 AAD 同盟 SSO，請在[AppSource 試用體驗逐步引導](https://aka.ms/trialexperienceforwebapps) \(英文\) 影片中移至 02:35 的位置。 如果您的應用程式尚未啟用 AAD 同盟 SSO，以下是一些相關的文件
   1. [單鍵登入](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/)。
   1. [整合應用程式與 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)。
     
1. 在解決方案中使用 Power BI：選擇性，但極為建議，因為經證實可以產生更高數量的潛在客戶。

## <a name="devcenter-account-setup"></a>DevCenter 帳戶設定
這是將您的公司註冊成為 Microsoft 發行者的程序。 如果您是已註冊的發行者且具有現有的 DevCenter 帳戶，請分享與您 DevCenter 帳戶相關聯的電子郵件識別碼。 在您的應用程式獲得發行許可之後，您將可以存取有關[雲端入口網站管理發行者設定檔](https://cloudpartner.azure.com/#documentation/manage-publisher-profile)的完整文件

如果您沒有帳戶，以下是設定 DevCenter 帳戶的重要步驟。
1. 在[這裡](https://signup.live.com/signup.aspx)建立 Microsoft 帳戶。
1. 移至 Microsoft DevCenter [註冊頁面](http://go.microsoft.com/fwlink/?LinkId=615100)，然後按一下 [註冊]。
1. 當提示付款時，使用我們所提供的代碼。 如果您沒有代碼，請連絡 [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup)。
1. 選取您所居住的[國家/地區](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees)，或您企業的所在位置。 「您未來將無法變更此選項。」
1. 選取您的[開發人員帳戶類型](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees)。針對 AppSource，請選取 [公司]。 針對公司帳戶，請務必檢閱這些[指導方針](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account)。
1. 輸入您要用於開發人員帳戶的連絡資訊。
如需如何設定 DevCenter 帳戶的詳細逐步指示，請參閱[這裡](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)的指示。

## <a name="provide-info-for-microsoft-sellers"></a>提供資訊給 Microsoft 銷售者
AppSource 對於合作夥伴的其中一個重要價值主張，是能夠與 Microsoft 銷售者共同合作，將合作夥伴應用程式呈現給潛在客戶。

填寫[提供給 Microsoft 銷售者的合作夥伴解決方案資訊](https://aka.ms/aapartnerappinfo) \(英文\) 並傳送至 [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs)。 這是讓 Cortana Intelligence 應用程式獲得發佈核准的必要步驟。

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>在 AppSource 上建置極具吸引力的客戶逐步解說
首先，請先看看 AppSource 上的 [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) \(英文\)。 除了試用體驗的進入點之外，AppSource 中的每個應用程式項目都有標題、摘要 (最多 100 個字元)、描述 (最多 1300 個字元)、影像、影片 (選擇性)、PDF 文件。 合作夥伴應充分利用這些項目，來建置極具吸引力的客戶體驗。

合作夥伴應該將要放上 AppSource 的內容，當成端對端銷售協調流程。 客戶會閱讀標題和描述以了解價值主張，然後檢閱影像和影片以了解解決方案的用途。 個案研究可協助潛在客戶了解現有客戶獲取價值的方式。 

這一切都應讓客戶感到興趣並想深入了解。 想像這是一個優秀技術銷售人員逐步向新客戶展示內容的簡短簡報。 建議的描述格式是將文字根據價值主張細分為子章節，而每節都有醒目提示的子標題。 

訪客通常會快速瀏覽 [產品摘要] 欄位和子標題，以快速了解該應用程式能處理之內容的重點，以及為何他們應考慮使用該應用程式。 因此，請務必夠抓住使用者的注意力，來使他們願意繼續閱讀以了解細節。

請查看下列合作夥伴的作法。
- [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview) \(英文\)
- [Plexure Retail Personalization](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview) \(英文\)
- [AvePoint Citizen Services](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview) \(英文\)

銷售的最後一個動作是示範應用程式/解決方案，展示價值主張的傳遞方式。 這正是 AppSource 上客戶試用體驗的意義所在。 良好的示範能做到下列項目：
- 以簡短的方式重新彙總應用程式的價值主張，並列出客戶公司內會與該解決方案互動的角色
- 述說案例並設定範例客戶處理特定問題的相關內容
- 說明情況 (在使用前) 通常會如何變壞並影響客戶，並比較使用解決方案後會發生的情況。 這可使用 PowerBI 儀表板等來顯示。
- 摘要說明解決方案如何使用特定的機器學習服務演算法等來達成此目的。

加入至 AppSource 的內容應該為高品質且足以促成下列事項：
- 合作夥伴的技術銷售人員應能針對銷售協調流程使用它。 若您的銷售小組也正在使用它，您便能預期 MS 銷售部門也同樣能這麼做。 這將會讓客戶連絡點能夠一致地對其小組組員和高層傳播相同案例，以在購買完成前取得預算和認同。
- 自發性地造訪網站的客戶可以自行探索所有內容，並樂於回應合作夥伴通訊以進行後續步驟。

這就是為何合作夥伴應該將要放上 AppSource 的內容，當成端對端銷售協調流程。 根據要在試用體驗中示範的案例流程和功能，即可決定產品的類型。

## <a name="publish-your-app-on-the-publishing-portal"></a>在發佈入口網站上發佈應用程式
一旦我們針對您的應用程式評估上述的步驟，您就可以存取發佈入口網站，並且能夠查看[如何透過雲端合作夥伴入口網站發佈 Cortana Intelligence 產品](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app) \(英文\) 以取得後續步驟的詳細指示。

如果您需要任何欄位的相關資訊，請傳送電子郵件到 <appsourcecissupport@microsoft.com>。
## <a name="my-app-is-published-on-appsource---now-what"></a>我的應用程式已發佈到 AppSource，接下來該怎麼辦？
首先，恭喜您成功發佈應用程式。
您透過在 AppSource 上發佈應用程式所取得的收益多寡，主要取決於您對目標對象的影響程度。 請參閱[將您在 AppSource 上的 Cortana Intelligence 應用程式發揚光大](http://aka.ms/aagrowthhackguide) \(英文\)，了解如何將您的收益最大化。

如果您有任何問題或建議，請透過 <appsourcecissupport@microsoft.com> 連絡我們。


