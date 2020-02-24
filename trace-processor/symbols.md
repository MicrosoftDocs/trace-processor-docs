---
title: Symbols - .NET TraceProcessing
description: In this tutorial, learn how load symbols when processing traces.
author: davidmatson
manager: maiak
ms.author: davidmatson
ms.date: 02/23/2020
ms.topic: tutorial
ms.prod: traceprocessor

---

# Using Symbols in .NET TraceProcessing

## Loading symbols

[TraceProcessor](xref:Microsoft.Windows.EventTracing.TraceProcessor) supports loading symbols and getting stacks from several data sources. The following console application looks at CPU samples and outputs the estimated duration that a specific function was running (based on the trace’s statistical sampling of CPU usage):

```csharp
using Microsoft.Windows.EventTracing;
using Microsoft.Windows.EventTracing.Cpu;
using Microsoft.Windows.EventTracing.Symbols;
using System;
using System.Collections.Generic;

class Program
{
    static void Main(string[] args)
    {
        if (args.Length != 3)
        {
            Console.Error.WriteLine("Usage: GetCpuSampleDuration.exe <trace.etl> <imageName> <functionName>");
            return;
        }

        string tracePath = args[0];
        string imageName = args[1];
        string functionName = args[2];
        Dictionary<string, Duration> matchDurationByCommandLine = new Dictionary<string, Duration>();

        using (ITraceProcessor trace = TraceProcessor.Create(tracePath))
        {
            IPendingResult<ISymbolDataSource> pendingSymbolData = trace.UseSymbols();
            IPendingResult<ICpuSampleDataSource> pendingCpuSamplingData = trace.UseCpuSamplingData();

            trace.Process();

            ISymbolDataSource symbolData = pendingSymbolData.Result;
            ICpuSampleDataSource cpuSamplingData = pendingCpuSamplingData.Result;

            symbolData.LoadSymbolsForConsoleAsync(SymCachePath.Automatic, SymbolPath.Automatic).GetAwaiter().GetResult();

            Console.WriteLine();
            IThreadStackPattern pattern = AnalyzerThreadStackPattern.Parse($"{imageName}!{functionName}");

            foreach (ICpuSample sample in cpuSamplingData.Samples)
            {
                if (sample.Stack != null && sample.Stack.Matches(pattern))
                {
                    string commandLine = sample.Process.CommandLine;

                    if (!matchDurationByCommandLine.ContainsKey(commandLine))
                    {
                        matchDurationByCommandLine.Add(commandLine, Duration.Zero);
                    }

                    matchDurationByCommandLine[commandLine] += sample.Weight;
                }
            }

            foreach (string commandLine in matchDurationByCommandLine.Keys)
            {
                Console.WriteLine($"{commandLine}: {matchDurationByCommandLine[commandLine]}");
            }
        }
    }
}
```

Running this program produces output similar to the following:

```shell
C:\GetCpuSampleDuration\bin\Debug\> GetCpuSampleDuration.exe C:\boot.etl user32.dll LoadImageInternal
0.0% (0 of 1165; 0 loaded)
<snip>
100.0% (1165 of 1165; 791 loaded)
wininit.exe: 15.99 ms
C:\Windows\Explorer.EXE: 5 ms
winlogon.exe: 20.15 ms
"C:\Users\AdminUAC\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background: 2.09 ms
```

(Output details will vary depending on the trace).

## Symbols format

Internally, TraceProcessor uses the [SymCache](https://docs.microsoft.com/windows-hardware/test/wpt/loading-symbols#symcache-path) format, which is a cache of some of the data stored in a PDB. When loading symbols, TraceProcessor requires specifying a location to use for these SymCache files (a SymCache path) and supports optionally specifying a SymbolPath to access PDBs. When a SymbolPath is provided, TraceProcessor will create SymCache files out of PDB files as needed, and subsequent processing of the same data can use the SymCache files directly for better performance.

## Next Steps

In this tutorial, you learned how to load symbols when processing traces.

The next step is to examine other built-in data available from TraceProcessor. Look at the [samples](https://github.com/microsoft/eventtracing-processing-samples) for some ideas. See the extension methods on [ITraceSource](xref:Microsoft.Windows.EventTracing.ITraceSource) for a list of supported trace data, or examine the method available from "trace." shown by IntelliSense. Note that not all traces include all supported types of data.
