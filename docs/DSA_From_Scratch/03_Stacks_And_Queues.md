# 03 — Stacks & Queues

## 🧠 The One Idea

**A stack is a pile of plates (last on, first off); a queue is a line at a counter (first in,
first out).** Whenever a problem cares about **most-recent** (matching brackets, undo, "next
greater") reach for a **stack**. Whenever it's about **fairness/order of arrival** or
**level-by-level** exploration, reach for a **queue**.

The one-liner: **"Stack = LIFO for most-recent logic; queue = FIFO for order/level logic."**

---

## 1. Stack in Go (a slice)

```go
stack := []int{}
stack = append(stack, x)          // push
top := stack[len(stack)-1]        // peek
stack = stack[:len(stack)-1]      // pop
```

Push/pop are amortised O(1).

---

## 2. Classic stack problem — balanced brackets

```go
func isValid(s string) bool {
    pairs := map[byte]byte{')': '(', ']': '[', '}': '{'}
    st := []byte{}
    for i := 0; i < len(s); i++ {
        c := s[i]
        if open, ok := pairs[c]; ok { // closing bracket
            if len(st) == 0 || st[len(st)-1] != open {
                return false
            }
            st = st[:len(st)-1] // pop the match
        } else {
            st = append(st, c) // opening bracket
        }
    }
    return len(st) == 0
}
```

The stack remembers the **most recent unmatched** opener — exactly LIFO.

---

## 3. Monotonic stack — "next greater element"

Keep the stack sorted so each element is pushed/popped once → O(n):

```go
func nextGreater(nums []int) []int {
    res := make([]int, len(nums))
    for i := range res {
        res[i] = -1
    }
    st := []int{} // holds indices, values decreasing
    for i, n := range nums {
        for len(st) > 0 && nums[st[len(st)-1]] < n {
            res[st[len(st)-1]] = n // n is the next greater
            st = st[:len(st)-1]
        }
        st = append(st, i)
    }
    return res
}
```

---

## 4. Queue & BFS

```go
queue := []int{start}
for len(queue) > 0 {
    node := queue[0]
    queue = queue[1:]      // dequeue (O(n) on a slice; fine for interviews)
    // ...visit, enqueue neighbours...
}
```

A queue drives **breadth-first search** (Lesson 05) — explore level by level. For performance use
`container/list` or a head index instead of `queue[1:]`.

---

## 5. When to use which

| Clue | Structure |
|---|---|
| brackets, undo, "next greater/smaller", DFS | **stack** |
| shortest path (unweighted), level-order, scheduling | **queue** |
| both ends matter (sliding-window max) | **deque** |

A **deque** (double-ended queue) powers sliding-window-maximum in O(n).

---

## 🎤 Say this in the interview

- *"Bracket matching is LIFO — I push openers and pop on a matching closer; valid iff the stack
  ends empty."*
- *"For 'next greater element' I use a monotonic stack so each index is pushed and popped once —
  O(n)."*
- *"BFS uses a FIFO queue to explore level by level, which gives shortest paths on unweighted
  graphs."*

➡️ **Next:** [04 — Trees & heaps](./04_Trees_And_Heaps.md)
