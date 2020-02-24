---
title: Quickstart Process a  trace - .NET TraceProcessing
description: In this quickstart, learn how to access data in an ETW trace.
author: davidmatson
manager: maiak
ms.author: davidmatson
ms.date: 02/20/2020
ms.topic: quickstart
ms.prod: traceprocessor

---

# Quickstart: Process your first trace

Try out TraceProcessor to access data in an Event Tracing for Windows (ETW) trace. TraceProcessor allows you to access ETW trace data as .NET objects.

In this quickstart you learn how to:

1. Install the TraceProcessing NuGet package.
2. Create a TraceProcessor.
3. Use TraceProcessor to access process command lines contained in the trace.

## Prerequisites

Visual Studio 2019

## Install the TraceProcessing NuGet package

.NET TraceProcessing is available from [NuGet](https://www.nuget.org/packages/Microsoft.Windows.EventTracing.Processing.All) with the following package ID:

Microsoft.Windows.EventTracing.Processing.All

You can use this package in a console app to list the process command lines contained in an ETW trace (.etl file).

1. Create a new .NET Core Console App. In Visual Studio, select File, New, Project..., and choose the Console App (.NET Core) template for C#.

    Enter a Project name, for example, TraceProcessorQuickstart, and choose Create.

2. In Solution Explorer, right-click on Dependencies and choose Manage NuGet Packages... and switch to the Browse tab.

3. In the Search box, enter Microsoft.Windows.EventTracing.Processing.All and search.

    Select Install on the NuGet package with that name, and close the NuGet window.

## Create a TraceProcessor

1. Change Program.cs to the following contents:

    ```csharp
    using Microsoft.Windows.EventTracing;
    using Microsoft.Windows.EventTracing.Processes;
    using System;

    class Program
    {
        static void Main(string[] args)
        {
            if (args.Length != 1)
            {
                Console.Error.WriteLine("Usage: <trace.etl>");
                return;
            }

            using (ITraceProcessor trace = TraceProcessor.Create(args[0]))
            {
                // TODO: call trace.Use...

                trace.Process();

                Console.WriteLine("TODO: Access data from the trace");
            }
        }
    }
    ```

2. Provide a trace name to use when running the project.

    In Solution Explorer, right-click on the project and choose Properties. Switch to the Debug tab and enter the path to a trace (.etl file) in Application arguments.

    If you do not already have a trace file, you can use [Windows Performance Recorder](https://docs.microsoft.com/windows-hardware/test/wpt/start-a-recording) to create one.

3. Run the application.

    Choose Debug, Start Without Debugging to run your code.

## Use TraceProcessor to access process command lines contained in the trace

1. Change Program.cs to the following contents:

    ```csharp
    using Microsoft.Windows.EventTracing;
    using Microsoft.Windows.EventTracing.Processes;
    using System;

    class Program
    {
        static void Main(string[] args)
        {
            if (args.Length != 1)
            {
                Console.Error.WriteLine("Usage: <trace.etl>");
                return;
            }

            using (ITraceProcessor trace = TraceProcessor.Create(args[0]))
            {
                IPendingResult<IProcessDataSource> pendingProcessData = trace.UseProcesses();

                trace.Process();

                IProcessDataSource processData = pendingProcessData.Result;

                foreach (IProcess process in processData.Processes)
                {
                    Console.WriteLine(process.CommandLine);
                }
            }
        }
    }
    ```

2. Run the application again.

    This time, you should see a list command lines from all processes that were executing while the trace was being recorded.

## Using TraceProcessor

To process a trace, call [TraceProcessor.Create](xref:Microsoft.Windows.EventTracing.TraceProcessor.Create%2A). The core interface is [ITraceProcessor](xref:Microsoft.Windows.EventTracing.ITraceProcessor), and using this interface involves the following pattern:

1. First, tell the processor what data you want to use from a trace
2. Second, process the trace; and
3. Finally, access the results.

Telling the processor what kinds of data you want up front means you do not need to spend time processing large volumes of all possible kinds of trace data. Instead, [TraceProcessor](xref:Microsoft.Windows.EventTracing.TraceProcessor) just does the work needed to provide the specific kinds of data you request.

## Next Steps

In this quickstart, you accessed process command lines from an ETW trace. Now, you have an application that can access data from traces.

 Process information is just one of many kinds of data that can be stored in an ETW trace.

The next step is to learn about the other [data sources](data-sources.md) TraceProcessor can access.
