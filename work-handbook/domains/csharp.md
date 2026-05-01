# Domain: C# / .NET

Lighter scaffold — secondary language. Grows with use as you encounter
C#-specific things at work.

---

## Modern C# essentials

- Records (C# 9+) for immutable value types. `record Point(double X, double Y);`
- Nullable reference types: enable in csproj (`<Nullable>enable</Nullable>`).
  Compiler enforces null safety like Kotlin / TypeScript.
- Pattern matching: `switch` expressions, deconstruction, type patterns.
- Top-level statements for scripts and small programs.
- File-scoped namespaces (`namespace X;`) reduce indentation.
- `using` declarations: `using var stream = ...;` — disposes at scope end.
- Async/await everywhere I/O happens. Don't block on async (`task.Result`
  in async context = deadlock risk).

---

## Numerical work in C#

- `double` is IEEE 754 double, same as everywhere else. Same precision rules.
- `decimal` for money — base-10, no rounding error in financial arithmetic,
  but slow and limited range. Use for accounting; `double` for math.
- `Math.NET Numerics` for linear algebra, statistics, optimisation.
  The numpy/scipy of .NET, smaller ecosystem.
- For high-perf numerics: `System.Numerics` for SIMD types, `Span<T>` for
  zero-copy buffer access, `Memory<T>` for async-safe ranges.

---

## Performance

- `Span<T>` and `ReadOnlySpan<T>` for stack-allocated views. Avoids
  allocations in hot paths.
- `ArrayPool<T>.Shared` for reusable buffers.
- Struct vs class: structs are value types, no GC pressure, but copied on
  pass — careful with size.
- `ref` returns and locals (C# 7+) for true zero-copy in some cases.
- BenchmarkDotNet for serious benchmarking. Don't trust ad-hoc timing.

---

## Concurrency

- `Task` and `async/await` for I/O-bound concurrency.
- `Parallel.For` and `Parallel.ForEach` for CPU-bound parallelism over
  collections.
- `Channel<T>` for producer-consumer patterns.
- `lock` for simple mutual exclusion. `SemaphoreSlim` when you need async
  acquisition.
- Avoid `Thread.Sleep` in async code — use `Task.Delay`.

---

## Common pitfalls

- LINQ is lazy. `.Where(...)` doesn't execute until enumerated. Multiple
  enumerations recompute. Materialise with `.ToList()` if you'll iterate
  twice.
- `IEnumerable<T>` doesn't promise multiple enumeration is safe — could
  re-execute a query against a database, throw, etc.
- `string` operations create new strings. For heavy concatenation, use
  `StringBuilder`.
- `DateTime` vs `DateTimeOffset` — prefer `DateTimeOffset` when timezone
  matters. `DateTime` ambiguity bites in financial calendars.

---

## Tooling

- `dotnet` CLI for everything. `dotnet build`, `dotnet test`, `dotnet run`.
- `xUnit` is the dominant test framework; `NUnit` and `MSTest` exist.
- `dotnet format` for formatting.
- Roslyn analyzers for static analysis.

---

<!-- expand from practice -->

This file is deliberately thin. Add C#-specific patterns and gotchas as
you encounter them at work.
