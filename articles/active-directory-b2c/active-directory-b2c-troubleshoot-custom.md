---
title: "用以針對自訂原則進行疑難排解的 Application Insights - Azure AD B2C | Microsoft Docs"
description: "如何設定 Application Insights 以追蹤自訂原則的執行"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeedakhter-msft
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ad31e5f4ef3be78d8d2dd6b9c7d83e447d9ef776
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---

# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C︰收集記錄

本文提供從 Azure AD B2C 收集記錄的步驟，讓您可以診斷自訂原則的問題。

## <a name="use-application-insights"></a>使用 Application Insights

Azure AD B2C 支援將資料傳送至 Application Insights 的功能。  Application Insights 提供方法來診斷例外狀況和將應用程式效能問題視覺化。

### <a name="setup-application-insights"></a>設定 Application Insights

1. 移至 [Azure 入口網站](https://portal.azure.com)。 確定您所在的租用戶具有您的 Azure 訂用帳戶 (不是 Azure AD B2C 租用戶)。
1. 在左側導覽功能表中按一下 [+新增]。
1. 搜尋並選取 [Application Insights]，然後按一下 [建立]。
1. 完成表單，然後按一下 [建立]。 在 [應用程式類型] 中，選取 [一般]。
1. 建立資源後，開啟 Application Insights 資源。
1. 在左邊功能表中尋找 [屬性]並按一下它。
1. 複製 [檢測金鑰]，將它儲存起來供下一節使用。

### <a name="set-up-the-custom-policy"></a>設定自訂原則

1. 開啟 RP 檔案 (例如 SignUpOrSignin.xml)。
1. 將下列屬性新增至 `<TrustFrameworkPolicy>` 元素：

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. 將子節點 `<UserJourneyBehaviors>` (如果尚未存在) 新增至 `<RelyingParty>` 節點。 它必須緊接在 `<DefaultUserJourney ReferenceId="YourPolicyName" />` 之後
2. 新增下列節點作為 `<UserJourneyBehaviors>` 元素的子節點。 務必以您在上一節中從 Application Insights 取得的 [檢測金鑰] 取代 `{Your Application Insights Key}`。

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` 會指示 ApplicationInsights 透過處理管線加速遙測，雖然有助於開發，但數量龐大時會受限。
  * `ClientEnabled="true"` 會將用戶端指令碼傳送至 ApplicationInsights，以追蹤頁面檢視和用戶端錯誤 (不需要)。
  * `ServerEnabled="true"` 會將現有的 UserJourneyRecorder JSON 當作自訂事件傳送至 Application Insights。
  最後的 XML 如下所示︰

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="YourPolicyName" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. 上傳原則。

### <a name="see-the-logs-in-application-insights"></a>查看 Application Insights 中的記錄

>[!NOTE]
> 短暫延遲之後 (5 分鐘之內)，您在 Application Insights 中才會看到新的記錄。

1. 開啟您在 [Azure 入口網站](https://portal.azure.com)中建立的 Application Insights 資源。
1. 在 [概觀] 功能表中，按一下 [分析]。
1. 在 Application Insights 入口網站中開啟新的索引標籤。
1. 以下是您可以用來檢視記錄的查詢清單

| 查詢 | 說明 |
|---------------------|--------------------|
traces | 查看 Azure AD B2C 產生的所有記錄 |
traces \| where timestamp > ago(1d) | 查看 Azure AD B2C 在最後一天產生的所有記錄

此項目可能很長。  請將它匯出至 CSV 以仔細查看。

您可以在[這裡](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)深入了解分析工具。

>[!NOTE]
>該社群已開發了使用者旅程檢視器來協助身分識別開發人員。  此檢視器不受 Microsoft 支援，僅依原狀提供使用。  它會讀取您的 Application Insights 執行個體，並提供結構良好的使用者旅程事件檢視。  請取得原始碼，並將它部署在您自己的解決方案中。

[Github 用來存放不受支援之自訂原則範例和相關工具的存放庫](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)





