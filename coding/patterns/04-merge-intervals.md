# Pattern 4: Merge Intervals

## What it is

Each item is a range `[start, end]`. After sorting by start time, overlapping ranges sit next to each other, so a single left to right sweep can merge them, insert a new one, or count conflicts. Two intervals `a` and `b` overlap when `a.start <= b.end and b.start <= a.end`.

## When to use it

- The input is a list of intervals, ranges, meetings, or `[start, end]` pairs.
- You merge overlaps, insert an interval, find free time, or count rooms.
- The answer depends on how ranges relate, not on their exact values.

## Template

```python
def merge(intervals):
    intervals.sort(key=lambda pair: pair[0])
    merged = [intervals[0][:]]
    for start, end in intervals[1:]:
        last = merged[-1]
        if start <= last[1]:           # overlap, extend the previous interval
            last[1] = max(last[1], end)
        else:
            merged.append([start, end])
    return merged
```

## Worked example 1: merge all overlapping intervals

```python
def merge_intervals(intervals):
    if not intervals:
        return []
    intervals.sort(key=lambda pair: pair[0])
    result = [list(intervals[0])]
    for start, end in intervals[1:]:
        if start <= result[-1][1]:
            result[-1][1] = max(result[-1][1], end)
        else:
            result.append([start, end])
    return result

# merge_intervals([[1, 4], [2, 5], [7, 9]]) -> [[1, 5], [7, 9]]
```

Time O(n log n) for the sort, space O(n) for the output.

## Worked example 2: insert an interval into a sorted list

The input intervals are sorted by start and do not overlap. Insert `new_interval` and merge if needed.

```python
def insert_interval(intervals, new_interval):
    result = []
    i, n = 0, len(intervals)
    start, end = new_interval

    # intervals that end before the new one starts
    while i < n and intervals[i][1] < start:
        result.append(intervals[i])
        i += 1

    # intervals that overlap the new one
    while i < n and intervals[i][0] <= end:
        start = min(start, intervals[i][0])
        end = max(end, intervals[i][1])
        i += 1
    result.append([start, end])

    # the rest
    while i < n:
        result.append(intervals[i])
        i += 1
    return result

# insert_interval([[1, 3], [6, 9]], [2, 5]) -> [[1, 5], [6, 9]]
```

Time O(n), space O(n).

## Worked example 3: minimum meeting rooms

Given meeting time intervals, return the smallest number of rooms needed so no two meetings in the same room overlap.

```python
import heapq

def min_meeting_rooms(intervals):
    if not intervals:
        return 0
    intervals.sort(key=lambda pair: pair[0])
    end_times = []                      # min heap of meeting end times
    for start, end in intervals:
        if end_times and end_times[0] <= start:
            heapq.heappop(end_times)    # a room freed up
        heapq.heappush(end_times, end)
    return len(end_times)

# min_meeting_rooms([[1, 4], [2, 5], [7, 9]]) -> 2
```

Time O(n log n), space O(n).

## Common pitfalls

- Sorting by end time when you need start time, or the reverse. Meeting rooms and interval scheduling differ here.
- Using `<` instead of `<=` for the overlap test when touching endpoints should count as an overlap.
- Mutating the input intervals when the caller still needs them.

## Practice problems

- Merge Intervals
- Insert Interval
- Interval List Intersections
- Non-overlapping Intervals
- Meeting Rooms
- Meeting Rooms II
- Employee Free Time
- My Calendar I
