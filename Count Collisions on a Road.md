# Count Collisions on a Road

## Problem Description

There are **n cars** on an infinitely long road. The cars are numbered from **0 to n-1 from left to right**, and each car occupies a **unique position**.

You are given a **0-indexed string `directions`** of length `n`.

Each character represents the movement of a car:

- `L` → moving **left**
- `R` → moving **right**
- `S` → **stationary**

All moving cars have the **same speed**.

---

## Collision Rules

The number of collisions is calculated using the following rules:

1. **Opposite Direction Collision**
   - When two cars moving in **opposite directions** collide  
   - Collision count **increases by 2**

2. **Moving → Stationary Collision**
   - When a **moving car** hits a **stationary car**  
   - Collision count **increases by 1**

3. **After Collision**
   - All cars involved in the collision **become stationary**
   - They **remain at the collision point**
   - Cars **cannot change direction or state otherwise**

---

## Objective

Return the **total number of collisions** that occur on the road.

---

## Java Implementation

```java
class Solution {
    public int countCollisions(String directions) {

        int rightCounter = 0;
        boolean seenS = false;
        int counter = 0;

        for (int i = 0; i < directions.length(); i++) {
            char c = directions.charAt(i);

            if (c == 'R') {
                rightCounter++;

            } else if (c == 'S') {
                counter += rightCounter;
                rightCounter = 0;
                seenS = true;

            } else { // 'L'
                if (rightCounter > 0) {
                    counter += 2 + rightCounter - 1;
                    seenS = true;
                    rightCounter = 0;

                } else if (seenS) {
                    counter++;
                }
            }
        }

        return counter;
    }
}
