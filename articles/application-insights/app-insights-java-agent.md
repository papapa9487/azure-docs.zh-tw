---
title: "Azure Application Insights 中 Java Web 應用程式的效能監視 | Microsoft Docs"
description: "使用 Application Insights 延伸 Java 網站的效能和使用量監視。"
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: a481c7c62383c92a5dfab0e3f2b1b4f4f0e5ddf5
ms.contentlocale: zh-tw
ms.lasthandoff: 01/25/2017

---
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>監視 Java Web 應用程式中的相依性、例外狀況和執行時間


如果您已[使用 Application Insights 檢測您的 Java Web 應用程式][java]，您可以使用 Java 代理程式獲得更深入的見解，而不需變更任何程式碼：

* **相依性** ：您的應用程式對其他元件呼叫的相關資料，包括：
  * **REST 呼叫** ：透過 HttpClient、OkHttp 和 RestTemplate (Spring) 進行。
  * **Redis 呼叫** ：透過 Jedis 用戶端進行。 如果呼叫時間長於 10 秒，代理程式也會擷取呼叫引數。
  * **[JDBC 呼叫](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - MySQL、SQL Server、PostgreSQL、SQLite、Oracle DB 或 Apache Derby DB。 支援 "executeBatch" 呼叫。 MySQL 與 PostgreSQL 的呼叫時間如果長於 10 秒，代理程式會回報查詢計劃。
* **攔截到例外狀況** ：由程式碼處理的例外狀況相關資料。
* **方法執行時間** ：執行特定的方法所花費的時間相關資料。

若要使用 Java 代理程式，您要在伺服器上安裝它。 您必須使用 [Application Insights Java SDK][java] 檢測您的 Web 應用程式。 

## <a name="install-the-application-insights-agent-for-java"></a>安裝 Java 的 Application Insights 代理程式
1. 在執行 Java 伺服器的電腦上[下載代理程式](https://aka.ms/aijavasdk)。
2. 編輯應用程式伺服器啟動指令碼，並加入下列 JVM：
   
    `javaagent:`*代理程式 JAR 檔案的完整路徑*
   
    例如，在 Linux 機器上的 Tomcat 中：
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. 重新啟動您的應用程式伺服器。

## <a name="configure-the-agent"></a>設定代理程式
建立名為 `AI-Agent.xml` 的檔案，並將它放在代理程式 JAR 檔案所在的同一資料夾中。

設定 XML 檔案的內容。 編輯下列範例以包含或省略您要的功能。

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

您必須啟用報告例外狀況和個別方法的方法執行時間。

根據預設，`reportExecutionTime` 為 true，而 `reportCaughtExceptions` 為 false。

## <a name="view-the-data"></a>檢視資料
在 Application Insights 資源中，彙總的遠端相依性和方法執行時間會出現[在效能圖格下][metrics]。

若要搜尋相依性、例外狀況及方法報告的個別執行個體，請開啟[搜尋][diagnostic]。

[診斷相依性問題 - 深入了解](app-insights-asp-net-dependencies.md#diagnosis)。

## <a name="questions-problems"></a>有疑問嗎？ 有問題嗎？
* 沒有資料？ [設定防火牆例外狀況](app-insights-ip-addresses.md)
* [疑難排解 Java](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md

