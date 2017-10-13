---
title: "Azure Service Fabric 應用程式層級監視 | Microsoft Docs"
description: "了解用來監視和診斷 Azure Service Fabric 叢集的應用程式與服務層級事件和記錄。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.openlocfilehash: 3c472904641108b7383cd0f1416c47460f8de11a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="application-and-service-level-event-and-log-generation"></a>產生應用程式與服務層級事件和記錄

## <a name="instrumenting-the-code-with-custom-events"></a>使用自訂事件檢測程式碼

檢測程式碼是其他監視服務層面的基礎。 為了讓您知道有問題發生，並診斷有什麼需要修正，檢測是唯一方式。 雖然技術上可以將偵錯工具連線至生產服務，但這不是常見的做法。 因此，取得詳細的檢測資料很重要。

某些產品會自動檢測您的程式碼。 雖然這些產品的效果不錯，但幾乎都還是需要手動檢測。 最後您還是要有足夠資訊，才能以抽絲剝繭的方式對應用程式進行偵錯。 本文件說明檢測程式碼的不同方法，以及在何種情況下應選擇什麼方法。

## <a name="eventsource"></a>EventSource
當您在 Visual Studio 中從範本建立 Service Fabric 解決方案時，將會產生 **EventSource** 衍生類別 (**ServiceEventSource** 或 **ActorEventSource**)。 建立的範本可讓您為應用程式或服務新增事件。 **EventSource** 名稱**必須**是獨一無二，並應該將預設範本字串 MyCompany-&lt;solution&gt;-&lt;project&gt; 重新命名。 多個同名的 **EventSource** 定義會導致執行階段發生問題。 每個已定義事件都必須有獨一無二的識別碼。 如果識別碼並非獨一無二，會發生執行階段失敗。 有些組織會預先指派識別碼的值範圍，以避免不同開發小組之間用法不一致。 如需詳細資訊，請參閱 [Vance 的部落格](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)或 [MSDN 文件](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)。

### <a name="using-structured-eventsource-events"></a>使用結構化 EventSource 事件

在本節的程式碼範例中，每個事件都是針對特定案例而定義，例如註冊服務類型。 當您依使用案例定義訊息時，資料可以和錯誤文字一起封裝，讓您依據指定屬性的名稱或值，更輕鬆地搜尋和篩選。 建構檢測輸出可讓您更容易取用，但需要更多考量和時間為每個使用案例定義新事件。 某些事件定義可以在整個應用程式內共用。 例如，應用程式內的許多不同服務會重複使用方法啟動或停止事件。 特定網域服務有自己的獨特事件，例如，訂單系統可能有 **CreateOrder** 事件。 此方法可能產生很多事件，專案小組之間可能需要協調識別碼。 

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The instance constructor is private to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```

### <a name="using-eventsource-generically"></a>以一般方式使用 EventSource

因為定義特定事件可能很困難，許多人會以一組常用的參數定義少量事件，且通常會將資訊輸出為字串。 大部分的結構層面會遺失，因此更難搜尋和篩選結果。 此方法定義的少量事件通常對應至記錄層級。 下列程式碼片段定義偵錯和錯誤訊息：

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The Instance constructor is private, to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        private const int DebugEventId = 10;
        [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
        public void Debug(string msg)
        {
            WriteEvent(DebugEventId, msg);
        }

        private const int ErrorEventId = 11;
        [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
        public void Error(string error, string msg)
        {
            WriteEvent(ErrorEventId, error, msg);
        }
```

使用混合式的結構化和泛型檢測也可行。 結構化檢測用於報告錯誤和計量。 泛型事件可用來產生詳細記錄，讓工程師用於疑難排解。

## <a name="aspnet-core-logging"></a>ASP.NET Core 記錄

必須仔細規劃您檢測程式碼的方式。 正確規劃檢測有助於避免可能使程式碼基底不穩定，導致需要重新檢測程式碼。 為了降低風險，開發人員可以選擇檢測程式庫，例如 Microsoft ASP.NET Core 中的 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)。 ASP.NET Core 提供 [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 介面，可搭配您選擇的提供者一起使用，讓現有程式碼所受的影響降到最低。 您可以在 Windows 和 Linux 上使用 ASP.NET Core 中的程式碼，也可以使用完整 .NET Framework 中的程式碼，使檢測程式碼標準化。 下方會進一步探討這項資訊：

### <a name="using-microsoftextensionslogging-in-service-fabric"></a>使用 Service Fabric 中的 Microsoft.Extensions.Logging

1. 將 Microsoft.Extensions.Logging NuGet 套件新增至您要檢測的專案。 另外也新增任何提供者套件 (若是協力廠商套件，請參閱下列範例)。 如需詳細資訊，請參閱 [ASP.NET Core 中的記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。
2. 為 Microsoft.Extensions.Logging 將 **using** 指示詞新增至您的服務檔案。
3. 在服務類別內定義私用變數。

  ```csharp
  private ILogger _logger = null;

  ```
4. 在服務類別的建構函式中，新增此程式碼：

  ```csharp
  _logger = new LoggerFactory().CreateLogger<Stateless>();

  ```
5. 開始用您的方法檢測程式碼。 以下是幾個範例：

  ```csharp
  _logger.LogDebug("Debug-level event from Microsoft.Logging");
  _logger.LogInformation("Informational-level event from Microsoft.Logging");

  // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
  // Later in the article, we discuss why this step is useful.
  _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

  ```

## <a name="using-other-logging-providers"></a>使用其他記錄提供者

某些協力廠商提供者會使用上節所述的方法，包括 [Serilog](https://serilog.net/)、[NLog](http://nlog-project.org/) 和 [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging)。 您可以將這些全部插入 ASP.NET Core 記錄中，也可以分開使用。 Serilog 有一項功能讓記錄器傳來的所有訊息更豐富。 這項功能可用來輸出服務名稱、類型和資料分割資訊。 若要在 ASP.NET Core 基礎結構中使用這項功能，請執行下列步驟：

1. 將 Serilog、Serilog.Extensions.Logging 和 Serilog.Sinks.Observable NuGet 套件新增至專案。 下一個範例中也新增 Serilog.Sinks.Literate。 本文稍後會示範更好的方法。
2. 在 Serilog 中，建立 LoggerConfiguration 和記錄器執行個體。

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. 將 SeriLog.ILogger 引數新增至服務建構函式，並傳遞新建立的記錄器。

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. 在服務建構函式中新增下列程式碼，為**ServiceTypeName**、**ServiceName**、**PartitionId** 和 **InstanceId** 等服務屬性建立屬性豐富器。 它也會將屬性豐富器新增至 ASP.NET Core 記錄工廠，讓您在程式碼中使用 Microsoft.Extensions.Logging.ILogger。

  ```csharp
  public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
      : base(context)
  {
      PropertyEnricher[] properties = new PropertyEnricher[]
      {
          new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
          new PropertyEnricher("ServiceName", context.ServiceName),
          new PropertyEnricher("PartitionId", context.PartitionId),
          new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
      };

      serilog.ForContext(properties);

      _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
  }
  ```

5. 檢測程式碼，就如同在沒有 SeriLog 的情況下使用 ASP.NET Core 一樣。

  >[!NOTE]
  >我們建議您不要在上述範例中使用靜態 Log.Logger。 Service Fabric 可以在單一處理序內裝載相同服務類型的多個執行個體。 如果您使用靜態 Log.Logger，屬性豐富器的最後一個寫入器會顯示所有執行中執行個體的值。 這是為什麼 _logger 變數是服務類別私人成員變數的其中一個原因。 此外，您還必須使 _logger 可供各服務可能用到的通用程式碼使用。

## <a name="choosing-a-logging-provider"></a>選擇記錄提供者

如果您的應用程式仰賴高效能，採用 **EventSource** 通常是不錯的方法。 **EventSource**「通常」耗用較少資源，效能優於 ASP.NET Core 記錄或任何可用的協力廠商解決方案。  這對於許多服務而言並不是問題，但如果您的服務屬於效能導向，使用 **EventSource** 可能是更好的選擇。 不過，採用 **EventSource** 時，工程團隊需要投注大量心力，才能發揮結構化記錄的優勢。 可能的話，請建構幾個記錄選項的概略雛形，然後選擇最符合需求的方法。

## <a name="next-steps"></a>後續步驟

在您選擇好要用來檢測應用程式和服務的記錄提供者之後，還需要彙總這些記錄和事件，才能將其傳送到任何分析平台。 請閱讀 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) 和 [WAD](service-fabric-diagnostics-event-aggregation-wad.md) 以深入了解一些建議的選項。
