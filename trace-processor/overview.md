---
title: What is .NET TraceProcessing - .NET TraceProcessing
description: In this overview, learn what .NET TraceProcessing is.
author: davidmatson
manager: maiak
ms.author: davidmatson
ms.date: 02/23/2020
ms.topic: overview
ms.prod: traceprocessor

---

# .NET TraceProcessing Overview

## Process ETW traces in .NET

[Event Tracing for Windows (ETW)](https://docs.microsoft.com/windows/win32/etw/event-tracing-portal) is a powerful trace collection system built-in to the Windows operating system. Windows has deep integration with ETW, including data on system behavior all the way down to the kernel for events like context switches, memory allocation, process create and exit, and many more. The system-wide data available from ETW makes it a good fit for end-to-end performance analysis or other questions that require looking at the interaction between by many components throughout the system.

Unlike text logging, ETW provides structured events designed for automated data processing. Microsoft has built powerful tools on top of these structured events, including [Windows Performance Analyzer](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) (WPA), which provides a graphical interface for visualizing and exploring the trace data captured in a ETW trace file (.etl).

Inside Microsoft, we heavily use ETW traces to measure the performance of new builds of Windows. Given the volume of data produced the Windows engineering system, automated analysis is essential. For our automated trace analysis, we heavily use C# and .NET, so we created a .NET API for accessing many kinds of ETW trace data. This technology is also used inside Windows Performance Analyzer to power several of its tables. The .NET TraceProcessing NuGet packages allow you to analyze your own applications and systems with the tooling that Microsoft uses to analyze Windows.

## Next Steps

In this overview, you learned what .NET TraceProcessing is.

The next step is to [process your first trace](quickstart.md).
