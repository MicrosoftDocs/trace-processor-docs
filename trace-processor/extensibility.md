---
title: Extensibility - .NET TraceProcessing
description: In this overview, learn how to extend .NET TraceProcessing.
author: davidmatson
manager: maiak
ms.author: davidmatson
ms.date: 02/23/2020
ms.topic: overview
ms.prod: traceprocessor

---

# TraceProcessor Extensibility

## Extend TraceProcessor

Many kinds of trace data have built-in support in [TraceProcessor](xref:Microsoft.Windows.EventTracing.TraceProcessor), but if you have your other providers that you would like to analyze (including your own custom providers), that data is also available from the trace live while processing occurs.

For example, here is a simple way to get the list of providers IDs in a trace:

```csharp
// Open a trace with TraceProcessor.Create() and call Run...

static void Run(ITraceProcessor trace)
{
    HashSet<Guid> providerIds = new HashSet<Guid>();
    trace.Use((e) => providerIds.Add(e.ProviderId));
    trace.Process();

    foreach (Guid providerId in providerIds)
    {
        Console.WriteLine(providerId);
    }
}
```

The following example shows a simplified custom data source:

```csharp
// Open a trace with TraceProcessor.Create() and call Run...

static void Run(ITraceProcessor trace)
{
    CustomDataSource customDataSource = new CustomDataSource();
    trace.Use(customDataSource);

    trace.Process();

    Console.WriteLine(customDataSource.Count);
}

class CustomDataSource : IFilteredEventConsumer
{
    public IReadOnlyList<Guid> ProviderIds { get; } = new Guid[] { new Guid("your provider ID") };

    public int Count { get; private set; }

    public void Process(EventContext eventContext)
    {
        ++Count;
    }
}
```

## Next Steps

In this overview, you learned how to extend TraceProcessor.

The next step is to examine other built-in data available from TraceProcessor. Look at the [samples](https://github.com/microsoft/eventtracing-processing-samples) for some ideas. See the extension methods on [ITraceSource](xref:Microsoft.Windows.EventTracing.ITraceSource) for a list of supported trace data, or examine the method available from "trace." shown by IntelliSense. Note that not all traces include all supported types of data.
