---
title: "在 Azure Application Insights 中探索 Java 追蹤記錄 | Microsoft Docs"
description: "在 Application Insights 中搜尋 Log4J 或 Logback 追蹤"
services: application-insights
documentationcenter: java
author: CFreemanwa
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 086091bb3be6659436ec8e371acbe4b8e86bfacb
ms.openlocfilehash: 3ef5f6240d7e4704118b86fb0cfee8cf502859ff
ms.contentlocale: zh-tw
ms.lasthandoff: 12/13/2016

---
# <a name="explore-java-trace-logs-in-application-insights"></a>在 Application Insights 中探索 Java 追蹤記錄
如果您使用 Logback 或 Log4J (v1.2 或 v2.0) 進行追蹤，您可以將追蹤記錄自動傳送到 Application Insights，您可以在其中探索及搜尋記錄。

## <a name="install-the-java-sdk"></a>安裝 Java SDK

安裝 [Java 適用的 Application Insights SDK][java]，如果您還未完成的話。

(如果您不想要追蹤 HTTP 要求，您可以省略大多數 .xml 組態檔，但必須至少包含 `InstrumentationKey` 元素。 您還應該呼叫 `new TelemetryClient()` 來將 SDK 初始化)。


## <a name="add-logging-libraries-to-your-project"></a>將記錄程式庫加入至專案
*選擇適合您的專案的方式。*

#### <a name="if-youre-using-maven"></a>如果您使用 Maven...
如果您的專案已設定為使用 Maven 來建置，請將下列其中一個程式碼片段合併至 pom.xml 檔案。

然後重新整理專案相依性，以下載程式庫。

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>如果您使用 Gradle...
如果您的專案已設定為使用 Gradle 來建置，請將下列其中一行加入至 build.gradle 檔案中的 `dependencies` 群組：

然後重新整理專案相依性，以下載程式庫。

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '1.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '1.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '1.0.+'
```

#### <a name="otherwise-"></a>否則...
下載並擷取適當的附加器，然後加入適當的程式庫至您的專案：

| 記錄器 | 下載 | 程式庫 |
| --- | --- | --- |
| Logback |[具有 Logback 附加器的 SDK](https://aka.ms/xt62a4) |applicationinsights-logging-logback |
| Log4J v2.0 |[具有 Log4J v2 附加器的 SDK](https://aka.ms/qypznq) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[具有 Log4J v1.2 附加器的 SDK](https://aka.ms/ky9cbo) |applicationinsights-logging-log4j1_2 |

## <a name="add-the-appender-to-your-logging-framework"></a>將附加器加入至記錄架構
若要開始進行追蹤，請將相關的程式碼片段合併到 Log4J 或 Logback 組態檔案： 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.Log4j">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Application Insights 附加器可由任何設定的記錄器參考，而不一定是根記錄器 (如以上程式碼範例所示)。

## <a name="explore-your-traces-in-the-application-insights-portal"></a>在 Application Insights 入口網站中探索您的追蹤
既然已將專案設定為傳送追蹤至 Application Insights，您可以在 Application Insights 入口網站的[搜尋][diagnostic]刀鋒視窗中檢視及搜尋這些追蹤。

![在 Application Insights 入口網站中，開啟 [搜尋]](./media/app-insights-java-trace-logs/10-diagnostics.png)

## <a name="next-steps"></a>後續步驟
[診斷搜尋][diagnostic]

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md



