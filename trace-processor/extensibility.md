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

[Process your first trace](quickstart.md) with TraceProcessor.