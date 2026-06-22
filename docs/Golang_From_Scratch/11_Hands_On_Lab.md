# 11 — Hands-On Lab (see the internals with your own eyes)

## 🧠 The One Idea

**The Go concepts that sound abstract — escape analysis, data races, the scheduler, GC — are all
things you can *make the toolchain show you*.** This lab runs short programs that *demonstrate*
each internal. Type them, run them, read the tool output, then explain it aloud. Budget ~45 min.

> Setup: install Go 1.21+ (`brew install go` / official installer). Make a scratch dir:
> `mkdir golab && cd golab && go mod init golab`. Put each snippet in `main.go` and `go run`.

---

## 1. Pass-by-value vs pointer (Lesson 02)

```go
package main
import "fmt"
func addVal(n int)  { n++ }
func addPtr(n *int) { *n++ }
func main() {
    x := 5
    addVal(x);  fmt.Println("after addVal:", x)   // 5  (copy)
    addPtr(&x); fmt.Println("after addPtr:", x)   // 6  (via address)
}
```
**See:** a value copy can't change the caller; a pointer can.

---

## 2. Escape analysis — watch the compiler decide stack vs heap (Lessons 02, 08)

```go
package main
func stayOnStack() int  { x := 42; return x }   // value copied out
func escapeToHeap() *int { x := 42; return &x } // address escapes
func main() { _ = stayOnStack(); _ = escapeToHeap() }
```
Run:
```bash
go build -gcflags=-m main.go
```
**See:** output like `./main.go: moved to heap: x` for `escapeToHeap`, and nothing for
`stayOnStack`. **That's escape analysis choosing heap vs stack — visible to you.**

---

## 3. The slice/append gotcha (Lesson 03)

```go
package main
import "fmt"
func main() {
    s := []int{1, 2, 3}
    t := s[:2]            // shares the same backing array
    t[0] = 99
    fmt.Println(s)        // [99 2 3]  — aliasing! same array
    t = append(t, 1000)   // cap allowed in-place write → overwrites s[2]
    fmt.Println(s)        // [99 2 1000] — surprise from shared capacity
}
```
**See:** slices are windows over a shared array; `append` can mutate a "different" slice.

---

## 4. A data race — then catch it with `-race` (Lesson 07)

```go
package main
import ("fmt"; "sync")
func main() {
    var wg sync.WaitGroup
    count := 0
    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go func() { defer wg.Done(); count++ }()   // RACE: unsynchronized write
    }
    wg.Wait()
    fmt.Println("count =", count)                   // often < 1000, nondeterministic
}
```
Run twice:
```bash
go run main.go            # prints a wrong, varying number
go run -race main.go      # DATA RACE report with the exact goroutines & lines
```
**Fix** it with a `sync.Mutex` around `count++` (or `sync/atomic`) and re-run with `-race` — the
report disappears. **This is the single best demo of why `-race` matters.**

---

## 5. Goroutines + WaitGroup + a worker pool (Lessons 05, 07)

```go
package main
import ("fmt"; "sync")
func main() {
    jobs := make(chan int, 10)
    results := make(chan int, 10)
    var wg sync.WaitGroup
    for w := 0; w < 3; w++ {              // 3 workers
        wg.Add(1)
        go func() {
            defer wg.Done()
            for j := range jobs { results <- j * j }
        }()
    }
    for i := 1; i <= 5; i++ { jobs <- i }
    close(jobs)                           // tell workers no more jobs
    go func() { wg.Wait(); close(results) }()  // close results when workers done
    for r := range results { fmt.Println(r) }  // 1 4 9 16 25 (any order)
}
```
**See:** bounded concurrency; channels + WaitGroup coordinate cleanly; closing signals "done."

---

## 6. select: timeout & cancellation (Lessons 06, 07)

```go
package main
import ("context"; "fmt"; "time")
func main() {
    ctx, cancel := context.WithTimeout(context.Background(), 100*time.Millisecond)
    defer cancel()
    slow := make(chan int)
    go func() { time.Sleep(time.Second); slow <- 1 }()  // too slow
    select {
    case v := <-slow:        fmt.Println("got", v)
    case <-ctx.Done():       fmt.Println("cancelled:", ctx.Err())  // this fires
    }
}
```
**See:** `ctx.Done()` (a channel that closes on timeout) wins the `select` — how Go does
deadlines/cancellation.

---

## 7. The nil-interface trap (Lesson 04)

```go
package main
import "fmt"
type MyErr struct{}
func (e *MyErr) Error() string { return "boom" }
func bad() error { var p *MyErr; return p }   // typed nil → non-nil interface
func main() {
    err := bad()
    fmt.Println(err == nil)   // false! (type word is set) — the famous gotcha
}
```
**See:** an interface holding a typed nil pointer is **not** `nil`. Return literal `nil` instead.

---

## 8. Benchmark + escape: prove pre-sizing helps (Lessons 03, 08)

```go
// bench_test.go  — run:  go test -bench=. -benchmem
package main
import "testing"
func BenchmarkNoPrealloc(b *testing.B) {
    for i := 0; i < b.N; i++ {
        s := []int{}
        for j := 0; j < 1000; j++ { s = append(s, j) }   // repeated regrows
    }
}
func BenchmarkPrealloc(b *testing.B) {
    for i := 0; i < b.N; i++ {
        s := make([]int, 0, 1000)                        // one allocation
        for j := 0; j < 1000; j++ { s = append(s, j) }
    }
}
```
**See:** the `-benchmem` column shows **fewer allocs/op and B/op** for the pre-sized version —
concrete proof that pre-allocation cuts GC pressure.

---

## ✅ What to be able to explain after this lab
- Why a value copy can't mutate the caller but a pointer can.
- How `-gcflags=-m` reveals **stack vs heap** (escape analysis).
- Why slices alias and how `append` can surprise you.
- What a **data race** is and how `-race` pinpoints it.
- How **goroutines + channels + WaitGroup + context** make correct concurrent code.
- Why a typed nil in an interface isn't `nil`.

If you can narrate each while running it, your Go internals are solid.

➡️ **Next:** [12 — Interview Q&A flashcards](./12_Interview_QA_Flashcards.md)
