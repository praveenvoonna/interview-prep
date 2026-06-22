# 01 — Arrays, Strings & Two-Pointer

## 🧠 The One Idea

**Instead of checking every pair (slow), walk two fingers along the data and let them do the
thinking.** Brute force compares everything with everything — O(n²). The **two-pointer** and
**sliding-window** patterns keep one or two indices moving in a single pass, turning many O(n²)
problems into O(n).

The one-liner: **"Two pointers / sliding window replace nested loops with a single coordinated
pass."**

---

## 1. Two-pointer (opposite ends)

Use when the array is **sorted** or you compare from both ends.

```go
// Two-sum on a SORTED array: find indices summing to target.
func twoSumSorted(a []int, target int) (int, int) {
    i, j := 0, len(a)-1
    for i < j {
        s := a[i] + a[j]
        switch {
        case s == target:
            return i, j
        case s < target:
            i++ // need bigger → move left pointer up
        default:
            j-- // need smaller → move right pointer down
        }
    }
    return -1, -1
}
```

O(n) time, O(1) space — versus O(n²) brute force.

---

## 2. Sliding window (same direction)

Use for **contiguous subarray/substring** problems ("longest/shortest/at most k…").

```go
// Longest substring without repeating characters.
func lengthOfLongestSubstring(s string) int {
    seen := map[byte]int{} // char -> last index
    best, start := 0, 0
    for i := 0; i < len(s); i++ {
        if j, ok := seen[s[i]]; ok && j >= start {
            start = j + 1 // shrink window past the duplicate
        }
        seen[s[i]] = i
        if i-start+1 > best {
            best = i - start + 1
        }
    }
    return best
}
```

The window `[start, i]` only ever moves right → O(n).

---

## 3. Fast & slow pointers

Two pointers at **different speeds** — cycle detection, finding the middle.

```go
// Detect a cycle in a linked list (Floyd's tortoise & hare).
func hasCycle(head *Node) bool {
    slow, fast := head, head
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
        if slow == fast {
            return true
        }
    }
    return false
}
```

---

## 4. In-place tricks

Modify the array without extra space (O(1) space):

- **Reverse:** swap ends moving inward.
- **Remove duplicates from sorted array:** a "write pointer" lags behind a "read pointer".
- **Dutch national flag:** three pointers to sort 0/1/2 in one pass.

---

## 5. Recognising the pattern

| Clue in the prompt | Reach for |
|---|---|
| "sorted array", "pair/triple summing to…" | two pointers from ends |
| "contiguous", "substring/subarray", "at most k" | sliding window |
| "linked list cycle / middle" | fast & slow |
| "in place, O(1) space" | read/write pointers |

---

## 🎤 Say this in the interview

- *"Brute force is O(n²); since the array is sorted I'll use two pointers from both ends for O(n)
  time, O(1) space."*
- *"This is a contiguous-substring problem, so I'll slide a window and track the last-seen index
  in a map — one pass, O(n)."*
- *"For cycle detection I'll use fast and slow pointers; they meet inside a cycle, O(1) space."*

➡️ **Next:** [02 — Hashing](./02_Hashing.md)
