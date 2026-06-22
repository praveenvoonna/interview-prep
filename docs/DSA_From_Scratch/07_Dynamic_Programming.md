# 07 — Dynamic Programming

## 🧠 The One Idea

**DP is solving a big problem by remembering the answers to smaller ones so you never solve the
same thing twice.** Naive recursion re-computes the same sub-problems exponentially; DP **caches**
each sub-answer once. If a problem has **overlapping sub-problems** and an **optimal substructure**
(the best big answer is built from best small answers), it's DP.

The one-liner: **"DP = recursion + memoisation: define the state, the transition, and the base
case, then compute each sub-problem once."**

---

## 1. The recipe (use it every time)

1. **Define the state:** what does `dp[i]` (or `dp[i][j]`) *mean*?
2. **Transition:** how does a state depend on smaller states?
3. **Base case:** the smallest known answers.
4. **Order:** top-down (memoised recursion) or bottom-up (fill a table).
5. **Answer:** which cell holds the final result.

Saying these five out loud *is* the solution.

---

## 2. Top-down (memoisation) — Fibonacci

```go
func fib(n int, memo map[int]int) int {
    if n < 2 { return n }
    if v, ok := memo[n]; ok { return v } // cached → no recompute
    memo[n] = fib(n-1, memo) + fib(n-2, memo)
    return memo[n]
}
```

Without the memo: O(2ⁿ). With it: **O(n)** — same recursion, just remembered.

---

## 3. Bottom-up (tabulation) — climbing stairs

```go
// Ways to climb n stairs taking 1 or 2 steps = Fibonacci.
func climbStairs(n int) int {
    if n < 2 { return 1 }
    a, b := 1, 1
    for i := 2; i <= n; i++ {
        a, b = b, a+b // only last two states needed → O(1) space
    }
    return b
}
```

**Space optimisation:** if `dp[i]` only needs the last few states, drop the array → O(1) space.

---

## 4. The classic patterns

| Pattern | Example | State |
|---|---|---|
| 1-D sequence | house robber, climb stairs | `dp[i]` best up to i |
| 0/1 knapsack | subset-sum, partition | `dp[i][w]` items i, capacity w |
| Two strings | edit distance, LCS | `dp[i][j]` prefixes i, j |
| Grid | min path sum, unique paths | `dp[r][c]` best to (r,c) |
| Unbounded | coin change | `dp[amount]` |

Most DP interview questions are a variation of one of these — recognising the family is the win.

---

## 5. Coin change (a full worked transition)

```go
// Fewest coins to make `amount`; -1 if impossible.
func coinChange(coins []int, amount int) int {
    dp := make([]int, amount+1)
    for i := 1; i <= amount; i++ {
        dp[i] = amount + 1 // "infinity"
        for _, c := range coins {
            if c <= i && dp[i-c]+1 < dp[i] {
                dp[i] = dp[i-c] + 1
            }
        }
    }
    if dp[amount] > amount { return -1 }
    return dp[amount]
}
```

State: `dp[x]` = fewest coins for amount x. Transition: best over each coin. O(amount × coins).

---

## 🎤 Say this in the interview

- *"This has overlapping sub-problems and optimal substructure, so it's DP. Let me define the
  state, the transition, and the base case."*
- *"I'll start top-down with memoisation to confirm the recurrence, then convert to bottom-up and
  drop the array to O(1) space if only recent states matter."*
- *"It's a 0/1-knapsack variant: `dp[i][w]` is the best using the first i items within capacity w."*

➡️ **Next:** [08 — Go patterns for interviews](./08_Go_Patterns_For_Interviews.md)
