# LEETCODE-Array-1482
Let's break down and understand the `minDays` method in the `Solution` class and see how it operates step-by-step.

### Problem Understanding
The goal of the problem is to determine the minimum number of days required to make `m` bouquets, each consisting of `k` adjacent flowers, given an array `bloomDay` where each element represents the day a specific flower will bloom.

### Explanation of the Code

1. **Initial Check:**
    ```java
    if (bloomDay.length < (long) m * k)
      return -1;
    ```
    - If the total number of flowers (`bloomDay.length`) is less than the total number of flowers needed to make `m` bouquets (`m * k`), it's impossible to create the bouquets, so the function returns `-1`.

2. **Setting the Search Range:**
    ```java
    int l = Arrays.stream(bloomDay).min().getAsInt();
    int r = Arrays.stream(bloomDay).max().getAsInt();
    ```
    - The lower bound (`l`) is set to the minimum value in `bloomDay` (earliest blooming flower).
    - The upper bound (`r`) is set to the maximum value in `bloomDay` (latest blooming flower).

3. **Binary Search:**
    ```java
    while (l < r) {
      final int mid = (l + r) / 2;
      if (getBouquetCount(bloomDay, k, mid) >= m)
        r = mid;
      else
        l = mid + 1;
    }
    ```
    - The binary search is performed to find the minimum day (`mid`) such that at least `m` bouquets can be made.
    - `getBouquetCount` is a helper method that returns the number of bouquets that can be made if we wait until `mid` days.
    - If we can make at least `m` bouquets by `mid` days, then we try for fewer days by adjusting the upper bound (`r = mid`).
    - Otherwise, we increase the lower bound (`l = mid + 1`).

4. **Return Result:**
    ```java
    return l;
    ```
    - After the binary search completes, `l` will be the minimum number of days required to make the `m` bouquets.

### Helper Method: `getBouquetCount`
This method calculates how many bouquets can be made if we wait until a specified number of days (`waitingDays`).

```java
private int getBouquetCount(int[] bloomDay, int k, int waitingDays) {
  int bouquetCount = 0;
  int requiredFlowers = k;
  for (final int day : bloomDay) {
    if (day > waitingDays) {
      requiredFlowers = k;
    } else if (--requiredFlowers == 0) {
      ++bouquetCount;
      requiredFlowers = k;
    }
  }
  return bouquetCount;
}
```
- The method iterates through each flower's bloom day in the `bloomDay` array.
- If a flower blooms after `waitingDays`, it resets the `requiredFlowers` counter because these flowers cannot be part of the same bouquet.
- If a flower blooms within `waitingDays`, it decreases the `requiredFlowers` counter.
- When `requiredFlowers` reaches zero, a bouquet is made, and the bouquet counter (`bouquetCount`) is incremented. The `requiredFlowers` counter is reset for the next bouquet.

### Dry Run Example
Let's consider a quick dry run example with an array `bloomDay = [1, 10, 3, 10, 2]`, `m = 3`, and `k = 1`.

1. **Initial Check:** 
   - `bloomDay.length = 5`
   - `m * k = 3 * 1 = 3`
   - Condition `bloomDay.length < m * k` is false, so we continue.

2. **Search Range:**
   - `l = 1` (min of `bloomDay`)
   - `r = 10` (max of `bloomDay`)

3. **Binary Search Iterations:**
   - `mid = (1 + 10) / 2 = 5`
     - `getBouquetCount([1, 10, 3, 10, 2], 1, 5) = 3`
     - Since `3 >= 3`, we update `r = 5`.
   - `mid = (1 + 5) / 2 = 3`
     - `getBouquetCount([1, 10, 3, 10, 2], 1, 3) = 3`
     - Since `3 >= 3`, we update `r = 3`.
   - `mid = (1 + 3) / 2 = 2`
     - `getBouquetCount([1, 10, 3, 10, 2], 1, 2) = 2`
     - Since `2 < 3`, we update `l = 3`.

4. **Result:**
   - The binary search ends with `l = 3`, which is the minimum number of days required to make the bouquets.

This illustrates how the algorithm efficiently narrows down the minimum days required through binary search and helper method logic.
