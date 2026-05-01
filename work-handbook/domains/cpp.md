# Domain: C++

Modern C++ idioms (C++17/20/23 where available), performance, and numerical
traps. Reference doc — grows with use.

---

## Resource management

- **RAII for everything.** Acquisition is initialisation; release is the
  destructor. No `new`/`delete` in modern code.
- `std::unique_ptr` for owned single objects.
- `std::shared_ptr` only when ownership genuinely needs to be shared.
  Default to `unique_ptr`.
- `std::span` (C++20) for non-owning views into contiguous data — replaces
  `(pointer, size)` pairs.
- `std::string_view` for non-owning string parameters. Be aware of lifetime.

## Move semantics

- Pass by value and `std::move` into the member, OR pass by `const&`,
  depending on whether the function will keep a copy.
- Don't `std::move` a return value of a local — copy elision (NRVO/RVO)
  handles it and `move` can disable elision.
- `std::move` is just a cast. It doesn't move anything; the destination's
  move constructor does.

## Common modern features

- Structured bindings: `auto [key, value] = *map.begin();`
- `if constexpr` for compile-time branches in templates.
- Concepts (C++20) for constrained templates. Better error messages than SFINAE.
- Ranges (C++20) for composable algorithms.
- `std::optional` for "value or not" — no more sentinel returns.
- `std::variant` for type-safe unions.
- `std::expected` (C++23) for fallible operations — Result type pattern.

---

## Performance

### Cache and memory

- Cache misses dominate cost in numerical code more often than instruction
  count. Layout matters.
- `std::vector<T>` (contiguous) over `std::list<T>` (pointers) almost
  always.
- For 2D data, prefer flat `std::vector<T>` with manual indexing or
  `std::mdspan` (C++23) over `vector<vector<T>>`.
- AoS vs SoA: array-of-structs is intuitive; struct-of-arrays often
  faster for vectorisation. Profile before deciding.

### Inlining and templates

- Compilers inline aggressively in optimised builds. Don't manually inline
  small functions.
- Templates in headers: standard. Templates in `.cpp`: only if explicit
  instantiations cover all needed types.

### Allocations

- Allocations are expensive. Hot loops should not allocate.
- `reserve` on vectors when size is known.
- Reuse allocated buffers across iterations rather than reconstructing.

---

## Numerical traps

- `int` overflow is *undefined behaviour* in C++ (unlike Python or numpy).
  Compiler may exploit it. Use unsigned with care, or `int64_t` when in doubt.
- `0.1 + 0.2 != 0.3` applies as in any IEEE 754 language.
- `<cmath>` functions vary in accuracy across compilers and architectures.
  For reproducibility, document and pin the toolchain.
- `-ffast-math` (gcc/clang) and `/fp:fast` (MSVC) enable optimisations that
  break IEEE compliance. NaN handling, associativity, denormals — all
  affected. Rarely worth it for financial numerics.
- Beware mixed precision in libraries. Eigen, BLAS, others have float and
  double versions; calling the wrong one silently degrades accuracy.

---

## Concurrency

- `std::thread` is low-level. Prefer `std::async` for fire-and-forget,
  thread pools for repeated work.
- `std::atomic` for shared scalars. Memory ordering matters; default
  (`memory_order_seq_cst`) is safest if slow.
- Locks: `std::lock_guard` for simple, `std::unique_lock` when you need
  flexibility, `std::scoped_lock` (C++17) for multi-mutex.
- Data races are undefined behaviour. The compiler can do anything if a
  race exists. Use ThreadSanitizer.

---

## Build and tooling

- CMake is the de facto build system. Modern target-based CMake
  (`target_link_libraries(target PRIVATE/PUBLIC/INTERFACE ...)`).
- Sanitizers in debug builds: `-fsanitize=address,undefined` (gcc/clang).
  Catches bugs you wouldn't otherwise see.
- `clang-tidy` and `clang-format` for static analysis and formatting.
- Compiler warnings: at minimum `-Wall -Wextra -Wpedantic`. Treat warnings
  as errors in CI.

---

## Common idioms

- Rule of zero: classes that don't manage resources don't define copy/move/destructor.
- Rule of five: if you define one of {copy ctor, copy assign, move ctor,
  move assign, destructor}, you usually need all five.
- Don't return references to local objects.
- Mark `override` on overriding member functions. Compiler catches mistakes.
- Mark `noexcept` on functions that don't throw — enables optimisations
  and matters for move construction.
- `const` correctness throughout. `const` on member functions, parameters,
  local variables. Default to `const`.

---

## Testing

- GoogleTest and Catch2 are the standard frameworks.
- For numerical code: tolerance-based comparisons, `EXPECT_NEAR` not
  `EXPECT_EQ`.
- Property-based testing in C++ via `rapidcheck` — smaller ecosystem
  than Hypothesis but the same idea.

---

<!-- expand from practice -->

Add C++ patterns, gotchas, and library notes as you encounter them.
