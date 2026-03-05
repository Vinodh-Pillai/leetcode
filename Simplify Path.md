# [Simplify Path](https://leetcode.com/problems/simplify-path/)

While solving the **Simplify Path** problem, I ended up learning something interesting about **string parsing performance in Java** and how small implementation choices can matter.

The problem itself is straightforward: normalize a Unix-style path.

Example:

```
/home//foo/      → /home/foo
/home/a/../b     → /home/b
/../             → /
```

Unix rules:

- `.` → current directory (ignore)
- `..` → parent directory
- multiple `/` → single `/`
- `...` or `....` → valid directory names

The final path must:

- start with `/`
- not end with `/` (unless root)
- not contain `.` or `..`

---

# Approach 1 — Using a Deque (Clean Directory Traversal)

Conceptually this problem behaves like navigating directories:

```
enter directory → push
go up directory → pop
ignore "." 
```

A `Deque` fits nicely because we can treat the end as the **current directory stack**.

### Implementation

```java
import java.util.*;

class Solution {

    public String simplifyPath(String path) {

        String[] parts = path.split("/");
        Deque<String> deque = new ArrayDeque<>();

        for (String part : parts) {

            if (part.isEmpty() || part.equals(".")) {
                continue;
            }

            if (part.equals("..")) {
                if (!deque.isEmpty()) {
                    deque.removeLast();
                }
            } else {
                deque.addLast(part);
            }
        }

        StringBuilder result = new StringBuilder();

        for (String dir : deque) {
            result.append("/").append(dir);
        }

        return result.length() == 0 ? "/" : result.toString();
    }
}
```

### Complexity

```
Time:  O(n)
Space: O(n)
```

This approach is clean and intuitive.

---

# My Learning — `split()` Is Convenient but Expensive

Initially I used:

```java
path.split("/")
```

But later I realized something important.

`split()` internally uses **regex**, which means:

- regex parsing overhead
- creation of multiple substring objects
- additional allocations

For a small string this doesn't matter much, but from a systems perspective it is **not the most efficient way to parse a path**.

This led me to explore a **manual substring parsing approach**, which avoids regex completely.

---

# Approach 2 — Manual Parsing Using `substring()`

Instead of splitting the string, we can **scan the path character by character** and extract directory names ourselves.

Advantages:

- avoids regex
- fewer intermediate objects
- still O(n)

### Implementation

```java
import java.util.*;

class Solution {

    public String simplifyPath(String path) {

        Deque<String> deque = new ArrayDeque<>();

        int i = 0;
        int n = path.length();

        while (i < n) {

            while (i < n && path.charAt(i) == '/') {
                i++;
            }

            int start = i;

            while (i < n && path.charAt(i) != '/') {
                i++;
            }

            String part = path.substring(start, i);

            if (part.equals("") || part.equals(".")) {
                continue;
            }

            if (part.equals("..")) {
                if (!deque.isEmpty()) {
                    deque.removeLast();
                }
            } else {
                deque.addLast(part);
            }
        }

        StringBuilder result = new StringBuilder();

        for (String dir : deque) {
            result.append("/").append(dir);
        }

        return result.length() == 0 ? "/" : result.toString();
    }
}
```

### Complexity

```
Time:  O(n)
Space: O(n)
```

But compared to `split()`, this avoids regex and unnecessary allocations.

---

# Final Takeaway

Both approaches work well and run in **O(n)**.

However, the key learning for me was this:

```
Convenience methods like split() often hide extra costs.
For performance-sensitive parsing, manual scanning can be better.
```

The algorithmic idea remains the same:

```
enter directory → push
go to parent → pop
ignore "."
```

But how we **parse the string** can make the implementation more efficient.
