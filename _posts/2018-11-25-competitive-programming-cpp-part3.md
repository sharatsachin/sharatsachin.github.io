---
title: Competitive Programming in C++ (Part III - STL Algorithms)
date: 2018-11-25 00:00:00 +0530
comments: true
categories: [competitive-programming]
tags: [competitive-programming, c++, stl]
---

This post is a continuation on the series on the Standard Template Library (STL). It focuses on algorithms, and is the last of a three part series.

<!--more-->

# Algorithms
The algorithms of STL are functions designed to be used on the STL containers discussed previously. 

Some of these functions support binary predicates too, which are basically functions, `bin_pred(*iter1, *iter2)` or `bin_pred(*iter1, val)` that are convertible to `bool`.

## Non-modifying sequence operations

1.  `all_of` (true if condition returns true for all elements in the range, false otherwise. Returns true if the range is empty)
2.  `any_of` (true if condition returns true for at least one element in the range, false otherwise. Returns false if the range is empty)
3.  `none_of` (true if condition returns true for no elements in the range, false otherwise. Returns true if the range is empty)

```cpp
vector<int> v = {2, 4, 6, 8, 10};
bool r1 = all_of(v.cbegin(), v.cend(), [](int i){ return i % 2 == 0; });
bool r2 = none_of(v.cbegin(), v.cend(), [](int i){ return i < 0; });
struct Divisible
{
    const int d;
    Divisible(int n) : d(n) {}
    bool operator()(int n) const { return n % d == 0; }
};
bool r3 = any_of(v.cbegin(), v.cend(), Divisible(5));
cout << r1 << " " << r2 << " " << r3; // 1 1 1
```

4.  `for_each` (apply function to range)
5.  `for_each_n` (apply function to first \\(n\\) elements of range)

```cpp
vector<int> v = {1, 2, 3, 4, 5};
// v = {1, 2, 3, 4, 5}
for_each(v.begin(), v.end(), [](auto& x){ x++; });
// v = {2, 3, 4, 5, 6}
for_each_n(v.begin(), 3, [](auto& x){ x *= 2; });
// v = {4, 6, 8, 5, 6}
```
6.  `find` (iterator to the first element equal to value or last if no such element is found)
7.  `find_if` (iterator to the first element satisfying the condition or last if no such element is found)

```cpp
vector<int> v{1, 2, 4, 5};
auto is_even = [](int i){ return i%2 == 0; };
auto r1 = find(begin(v), end(v), 3);
cout << (r1 == end(v)) << '\n'; // 1
auto r2 = find(begin(v), end(v), 5);
cout << distance(begin(v), r2) << '\n'; // 3
auto r3 = find_if(begin(v), end(v), is_even);
cout << distance(begin(v), r3) << '\n'; // 1
```
8.  `find_end` (iterator to the beginning of last occurrence of the sequence range2 in range1, or iterator to last element of range1 if not found)

```cpp
// hopefully the example makes it clear
vector<int> v1 = {1, 2, 1, 2, 3, 4}, v2 = {1, 2}, v3= {1, 2, 4};
auto r1 = find_end(v1.begin(), v1.end(), v2.begin(), v2.end());
// {1, 2} is present twice in v1, the last occurence starting at index 2
auto r2 = find_end(v1.begin(), v1.end(), v3.begin(), v3.end());
// {1, 2, 4} is not present in v1 at all
cout << distance(v1.begin(), r1) << " " << distance(v1.begin(), r2); // 2 6
```
9. `find_first_of` (iterator to the first element in the range1 equal to any element from range2, or iterator to last element of range1 if not found)

```cpp
vector<int> v1 = {3, 4, 5, 6}, v2 = {1, 2, 3, 5}, v3 = {7, 8, 9};
auto r1 = find_first_of(v1.begin(), v1.end(), v2.begin(), v2.end());
auto r2 = find_first_of(v1.begin(), v1.end(), v3.begin(), v3.end());
cout << distance(v1.begin(), r1) << " " << distance(v1.begin(), r2); // 0 4
```
10.  `adjacent_find` (finds the first two adjacent elements that are equal (or satisfy a given consition))

```cpp
vector<int> v1{1, 2, 3, 3, 4, 3, 4};
auto r1 = adjacent_find(v1.begin(), v1.end());
// v1[2] = 3 and v1[3] = 3
auto r2 = adjacent_find(v1.begin(), v1.end(), greater<int>());
// v1[4] = 4 and v1[3] = 3, and 4 > 3
cout << distance(v1.begin(), r1) << " " << distance(v1.begin(), r2); // 2 4
```
11.  `count` (count appearances of value in range)
12.  `count_if` (count of elements in range satisfying condition)

```cpp
vector<int> v{1, 2, 4, 5};
auto is_even = [](int i){ return i%2 == 0; };
auto r1 = count(begin(v), end(v), 4);
cout << r1 << '\n'; // 1
auto r2 = count_if(begin(v), end(v), is_even);
cout << r2 << '\n'; // 2
```
13.  `mismatch` (return first position where the two ranges differ, supports binary predicate too)

```cpp
string s1 = "abcd", s2 = "abcc";
auto r1 = mismatch(s1.begin(), s1.end(), s2.begin());
cout << *r1.first << ' ' << *r1.second; // d c
```
14.  `equal` (test whether the elements in two ranges are equal, supports binary predicate too)

```cpp
// check if string is palindrome
string s1 = "abcba", s2 = "abcd";
cout << equal(s1.begin(), s1.begin() + s1.size()/2, s1.rbegin()); // 1
cout << equal(s2.begin(), s2.begin() + s2.size()/2, s2.rbegin()); // 0
```
15.  `is_permutation` (test whether range is permutation of another, supports binary predicate too)

```cpp
string s1 = "abcba", s2 = "cbaba";
cout << is_permutation(s1.begin(), s1.end(), s2.begin()); // 1
```
16.  `search` (search range for subsequence, supports binary predicate too)

```cpp
string s = "what is going on", s1 = "going", s2 = "gong";
auto r1 = search(s.begin(), s.end(), s1.begin(), s1.end());
auto r2 = search(s.begin(), s.end(), s2.begin(), s2.end());
cout << distance(s.begin(), r1) << '\n'   // 8
     << (s.end() == r2) << '\n';          // 1
```
17.  `search_n` (search range for elements, supports binary predicate too)

```cpp
string s = "10100010";
auto r1 = search_n(s.begin(), s.end(), 3, '0');
auto r2 = search_n(s.begin(), s.end(), 4, '0');
cout << distance(s.begin(), r1) << '\n'   // 3
     << (s.end() == r2) << '\n';          // 1
```

## Modifying sequence operations

1.  `copy` (copy range of elements)
2.  `copy_n` (copy n elements)
3.  `copy_if` (copy certain elements of range)
4.  `copy_backward` (copy range of elements backward)
5.  `move` (Move range of elements)
6.  `move_backward` (Move range of elements backward)

```cpp
vector<int> v = {1, 2, 3, 4, 5, 6};
vector<int> v1, v2, v3, v4(3);
copy(v.begin(), v.end(), back_inserter(v1));
// v1 = {1, 2, 3, 4, 5, 6}
copy_if(v.begin(), v.end(), back_inserter(v2), [](int x) {return x % 3 == 0; });
// v1 = {3, 6}
copy_n(v.begin(), 3, back_inserter(v3));
// v1 = {1, 2, 3}
copy_backward(v.end() - 3, v.end(), v4.end());
// v1 = {4, 5, 6}
// move and move_backward work in the same way
// elements in the moved-from range will not necessarily 
// contain the same values as before the move   
```
7.  `swap` (Exchange values of two objects)

```cpp
string a = "ab";
string b = "cd";
swap(a, b); // a is 'cd' and b is 'ab'
```
8.  `swap_ranges` (Exchange values of two ranges)

```cpp
vector<char> v = {'a', 'b', 'c', 'd', 'e'};
list<char> l = {'1', '2', '3', '4', '5'};
swap_ranges(v.begin(), v.begin()+3, l.begin());
// v = {'1', '2', '3', 'd', 'e'}, l = {'a', 'b', 'c', '4', '5'}
```
9. iter_swap (Exchange values of objects pointed to by two iterators)

```cpp
vector<int> v = { 1, 2, 3 };
iter_swap(v.begin(), v.begin() + 1);
cout << v[0] << ' ' << v[1]; // 2 1
```

10. transform (Transform range)

```cpp
vector<int> v = { 1, 2, 3 };
transform(v.begin(), v.end(), v.begin(), [](int i) { return i * 2; });
for (int i : v) cout << i << ' '; // 2 4 6
```

11. replace (Replace value in range)

```cpp
vector<int> v = { 1, 2, 2, 3 };
replace(v.begin(), v.end(), 2, 4);
for (int i : v) cout << i << ' '; // 1 4 4 3
```

12. replace_if (Replace values in range)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
replace_if(v.begin(), v.end(), [](int i) { return i % 2 == 0; }, 0);
for (int i : v) cout << i << ' '; // 1 0 3 0 5
```

13. replace_copy (Copy range replacing value)

```cpp
vector<int> v = { 1, 2, 2, 3 };
vector<int> v2(v.size());
replace_copy(v.begin(), v.end(), v2.begin(), 2, 4);
for (int i : v2) cout << i << ' '; // 1 4 4 3
```

14. replace_copy_if (Copy range replacing value)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
vector<int> v2(v.size());
replace_copy_if(v.begin(), v.end(), v2.begin(), [](int i) { return i % 2 == 0; }, 0);
for (int i : v2) cout << i << ' '; // 1 0 3 0 5
```

15.  `fill` (Fill range with value)
16.  `fill_n` (Fill sequence with value)

```cpp
vector<int> v(5);
fill(v.begin(), v.end(), 1);
for (int i : v) cout << i << ' '; // 1 1 1 1 1
fill_n(v.begin(), 3, 2);
for (int i : v) cout << i << ' '; // 2 2 2 1 1
```

17.  `generate` (Generate values for range with function)
18.  `generate_n` (Generate values for sequence with function)

```cpp
vector<int> v(5);
generate(v.begin(), v.end(), [n = 0]() mutable { return n++; });
for (int i : v) cout << i << ' '; // 0 1 2 3 4
generate_n(v.begin(), 3, [n = 5]() mutable { return n++; });
for (int i : v) cout << i << ' '; // 5 6 7 3 4
// the lambda function captures a variable n with an initial value of 5, takes 
// no arguments, and returns the current value of n while incrementing it
```


19.  `remove` (Remove value from range)
20.  `remove_if` (Remove elements from range)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
v.erase(remove(v.begin(), v.end(), 3), v.end());
// remove doesn't actually resize the container, so we use erase to do that
for (int i : v) cout << i << ' '; // 1 2 4 5
v.erase(remove_if(v.begin(), v.end(), [](int i) { return i % 2 == 0; }), v.end());
for (int i : v) cout << i << ' '; // 1 5
```

21.  `remove_copy` (copy range removing value)
22.  `remove_copy_if` (copy range removing values)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
vector<int> v2(v.size());
remove_copy(v.begin(), v.end(), v2.begin(), 3);
for (int i : v2) cout << i << ' '; // 1 2 4 5 0
remove_copy_if(v.begin(), v.end(), v2.begin(), [](int i) { return i % 2 == 0; });
for (int i : v2) cout << i << ' '; // 1 3 5 5 0
```


23.  `unique` (Remove consecutive duplicates in range)

```cpp
vector<int> v = { 1, 2, 2, 3, 3, 3, 4, 5 };
v.erase(unique(v.begin(), v.end()), v.end());
for (int i : v) cout << i << ' '; // 1 2 3 4 5
```

24.  `unique_copy` (copy range removing duplicates)

```cpp
vector<int> v = { 1, 2, 2, 3, 3, 3, 4, 5 };
vector<int> v2(v.size());
unique_copy(v.begin(), v.end(), v2.begin());
for (int i : v2) cout << i << ' '; // 1 2 3 4 5 0 0 0 
```

25.  `reverse` (Reverse range)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
reverse(v.begin(), v.end());
for (int i : v) cout << i << ' '; // 5 4 3 2 1
```

26.  `reverse_copy` (copy range reversed)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
vector<int> v2(v.size());
reverse_copy(v.begin(), v.end(), v2.begin());
for (int i : v2) cout << i << ' '; // 5 4 3 2 1
```

27.  `rotate` (Rotate left the elements in range)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
rotate(v.begin(), v.begin() + 2, v.end());
for (int i : v) cout << i << ' '; // 3 4 5 1 2
```

28.  `rotate_copy` (copy range rotated left)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
vector<int> v2(v.size());
rotate_copy(v.begin(), v.begin() + 2, v.end(), v2.begin());
for (int i : v2) cout << i << ' '; // 3 4 5 1 2
```

29.  `random_shuffle` (Randomly rearrange elements in range)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
random_shuffle(v.begin(), v.end());
for (int i : v) cout << i << ' '; // 4 2 5 1 3
```

30.  `shuffle` (Randomly rearrange elements in range using generator)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
shuffle(v.begin(), v.end(), default_random_engine());
for (int i : v) cout << i << ' '; // 4 1 5 2 3
```

31.  `sample` (Selects n random elements from a sequence)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
vector<int> v2(3);
sample(v.begin(), v.end(), v2.begin(), 3, default_random_engine());
for (int i : v2) cout << i << ' '; // 4 1 5
```

## Partitions

1.  `is_partitioned` (Test whether range is partitioned)
2.  `partition` (Partition range in two)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
cout << is_partitioned(v.begin(), v.end(), [](int i) { return i % 2 == 0; }); // 0
partition(v.begin(), v.end(), [](int i) { return i % 2 == 0; });
// ordering of elements is not preserved
cout << is_partitioned(v.begin(), v.end(), [](int i) { return i % 2 == 0; }); // 1
cout << is_partitioned(v.rbegin(), v.rend(), [](int i) { return i % 2 == 0; }); // 0
// all the elements for which the predicate returns true appear before those for which it returns false
```

3.  `stable_partition` (Partition range in two - stable ordering)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
stable_partition(v.begin(), v.end(), [](int i) { return i % 2 == 0; });
for (int i : v) cout << i << ' '; // 2 4 1 3 5
// enforces the relative ordering of elements
```

4.  `partition_copy` (Partition range into two)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
vector<int> v2, v3;
partition_copy(v.begin(), v.end(), back_inserter(v2), back_inserter(v3), [](int i) { return i % 2 == 0; });
for (int i : v2) cout << i << ' '; // 2 4
for (int i : v3) cout << i << ' '; // 1 3 5
```

5.  `partition_point` (Get partition point)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
partition(v.begin(), v.end(), [](int i) { return i % 2 == 0; });
for (int i : v) cout << i << ' '; // 4 2 3 1 5
auto r1 = partition_point(v.begin(), v.end(), [](int i) { return i % 2 == 0; });
cout << *r1 << '\n'; // 3
cout << distance(v.begin(), r1); // 2
// returns the first element in the second partition
```

## Sorting

1.  `sort` (Sort elements in range)

```cpp
vector<int> v = { 6, 2, 8, 1, 3, 9 };
sort(v.begin(), v.end());
sort(v.rbegin(), v.rend()); // sort in descending order
sort(v.begin(), v.end(), greater<int>()); // sort in descending order
```

2.  `stable_sort` (Sort elements preserving order of equivalents)

```cpp
vector<vector<int>> v = { {1, 3}, {1, 1}, {2, 2}, {1, 2}, {2, 3}, {2, 1} };
stable_sort(v.begin(), v.end(), [](auto& a, auto& b) { return a[0] < b[0]; });
// v = { {1, 3}, {1, 1}, {1, 2}, {2, 2}, {2, 3}, {2, 1} }
```

3.  `partial_sort` (Partially sort elements in range)

```cpp
vector<int> v = { 6, 2, 8, 1, 3, 9 };
partial_sort(v.begin(), v.begin() + 3, v.end());
// v = {1, 2, 3, 6, 8, 9}
```

4.  `partial_sort_copy` (copy and partially sort range)

```cpp
vector<int> v = { 6, 2, 8, 1, 3, 9 };
vector<int> v2(3);
partial_sort_copy(v.begin(), v.end(), v2.begin(), v2.end());
for (int i : v2) cout << i << ' '; // 1 2 3
```

5.  `is_sorted` (check whether range is sorted)

```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
cout << is_sorted(v.begin(), v.end()); // 1
cout << is_sorted(v.begin(), v.end(), greater<int>()); // 0
```

6.  `is_sorted_until` (Find first unsorted element in range)

```cpp
vector<int> v = { 1, 2, 3, 4, 2, 5 };
auto r1 = is_sorted_until(v.begin(), v.end());
cout << *r1 << '\n'; // 2 (since 2 is the first unsorted element)
cout << distance(v.begin(), r1); // 4
```

7.  `nth_element` (Sort element in range)

```cpp
vector<int> v = {5, 10, 6, 4, 3, 2, 6, 7, 9, 3};
auto m = v.begin() + v.size()/2;
nth_element(v.begin(), m, v.end());
cout << *m << '\n'; // 5 (the median)
nth_element(v.begin(), v.begin() + 1, v.end(), greater<int>());
cout << v[1] << '\n'; // 9 (the second largest element)
```


## Binary search

1.  `lower_bound` (Return iterator to the first element in the range [first,last) which does not compare less than val)
2.  `upper_bound` (Return iterator to the first element in the range [first,last) which compares greater than val)

```cpp
// lower_bound returns an iterator pointing to the first element in the range [first,last) which does not compare less than val
vector<int> v = { 1, 2, 3, 4, 5, 7, 8, 9 };
auto r1 = lower_bound(v.begin(), v.end(), 3); 
// *r1 == 3 and distance(v.begin(), r1) == 2
auto r2 = lower_bound(v.begin(), v.end(), 6);
// *r2 == 7 and distance(v.begin(), r2) == 5
auto r3 = upper_bound(v.begin(), v.end(), 3);
// *r3 == 4 and distance(v.begin(), r3) == 3
auto r4 = upper_bound(v.begin(), v.end(), 6);
// *r4 == 7 and distance(v.begin(), r4) == 5
// upper_bound and lower_bound are the same if the element is not found
// if element is found, upper bound points to the element after the last occurence of the searched element
```

3.  `equal_range` (Get pair of iterators defining range of equal elements)

```cpp
vector<int> v = { 1, 2, 3, 5, 5, 5, 8, 9 };
auto r1 = equal_range(v.begin(), v.end(), 5);
// distance(v.begin(), r1.first) == 3 and distance(v.begin(), r1.second) == 6```cpp
```

4.  `binary_search` (Test if value exists in sorted sequence)

```cpp
vector<int> v = { 1, 2, 3, 4, 5, 7, 8, 9 };
cout << binary_search(v.begin(), v.end(), 5); // 4
```

## Merge

1.  `merge` (Merge sorted ranges)

```cpp
vector<int> v1 = { 1, 4, 6, 8 };
vector<int> v2 = { 2, 3, 5, 7 }; // v1 and v2 are sorted
vector<int> v3(v1.size() + v2.size());
merge(v1.begin(), v1.end(), v2.begin(), v2.end(), v3.begin());
```

2.  `inplace_merge` (Merges two consecutive sorted ranges [first,middle) and [middle,last) into one sorted range [first,last))

```cpp
vector<int> v = { 1, 4, 6, 2, 3, 5, 7, 8 };
inplace_merge(v.begin(), v.begin() + 3, v.end());
// v = { 1, 2, 3, 4, 5, 6, 7, 8 }

auto merge_sort = [](vector<int>::iterator l, vector<int>::iterator r)
{
    if (r - l > 1)
    {
        auto m = l + (r - l)/2;
        merge_sort(l, m);
        merge_sort(m, r);
        inplace_merge(l, m, r);
    }
};
vector<int> v1 = { 7, 3, 5, 1, 6, 2, 4, 8 };
merge_sort(v1.begin(), v1.end());
// v1 = { 1, 2, 3, 4, 5, 6, 7, 8 }
```

3.  `set_union` (Union of two sorted ranges)
4.  `set_intersection` (Intersection of two sorted ranges)
5.  `set_difference` (Difference of two sorted ranges)
6.  `set_symmetric_difference` (Symmetric difference of two sorted ranges)

```cpp
vector<int> v1 = { 1, 2, 3, 4, 5 }, v2 = { 3, 4, 5, 6, 7 }, v3 , v4, v5, v6; 
// v1 and v2 must be sorted
set_union(v1.begin(), v1.end(), v2.begin(), v2.end(), back_inserter(v3));
// v3 = { 1, 2, 3, 4, 5, 6, 7 }
set_intersection(v1.begin(), v1.end(), v2.begin(), v2.end(), back_inserter(v4));
// v4 = { 3, 4, 5 }
set_difference(v1.begin(), v1.end(), v2.begin(), v2.end(), back_inserter(v5));
// v5 = { 1, 2 }
set_symmetric_difference(v1.begin(), v1.end(), v2.begin(), v2.end(), back_inserter(v6));
// v6 = { 1, 2, 6, 7 } // elements that are in either of the ranges, but not in both
```


## Heap

1.  `push_heap` (Push element into heap range)
2.  `pop_heap` (Pop element from heap range)
3.  `make_heap` (Make heap from range)
4.  `sort_heap` (Sort elements of heap)
5.  `is_heap` (Test if range is heap)
6.  `is_heap_until` (Find first element not in heap order)

```cpp
vector<int> v = { 3, 1, 4, 1, 5, 2, 6, 5, 3, 5 };
make_heap(v.begin(), v.end()); // creates a max heap
// make_heap(v.begin(), v.end(), greater<int>()); // creates a min heap
// 6 5 4 5 5 2 3 1 3 1
v.push_back(7);
push_heap(v.begin(), v.end());
// 7 6 4 5 5 2 3 1 3 1 5
v.pop_heap(v.begin(), v.end()); // moves the largest element to the end
// 6 5 4 5 5 2 3 1 3 1 7
int largest = v.back();
v.pop_back();
// 6 5 4 5 5 2 3 1 3 1 
sort_heap(v.begin(), v.end());
// 1 1 2 3 3 4 5 5 5 6

cout << is_heap(v.begin(), v.end()); // 0

auto r1 = is_heap_until(v.begin(), v.end());
cout << distance(v.begin(), r1); // 2 (the first element that is not in heap order)
```


## Min/max

1.  `min` (Return the smallest)
2.  `max` (Return the largest)
3.  `minmax` (Return pair of elements, first is smallest, second is largest)

```cpp
cout << min(3, 4) << '\n'; // 3
cout << max(3, 4) << '\n'; // 4
cout << minmax(3, 4).first << ' ' << minmax(3, 4).second << '\n'; // 3 4
```

4.  `min_element` (Return smallest element in range)
5.  `max_element` (Return largest element in range)
6.  `minmax_element` (Return smallest and largest elements in range)

```cpp
vector<int> v = { 3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5 };
auto r1 = min_element(v.begin(), v.end());
cout << *r1 << '\n'; // 1
auto r2 = max_element(v.begin(), v.end());
cout << *r2 << '\n'; // 9
auto [r3, r4] = minmax_element(v.begin(), v.end());
cout << *r3 << ' ' << *r4 << '\n'; // 1 9
```

7.  `clamp` (clamps a value between a pair of boundary values)

```cpp
cout << clamp(3, 1, 4) << '\n'; // 3
cout << clamp(0, 1, 4) << '\n'; // 1
cout << clamp(5, 1, 4) << '\n'; // 4
```

## Other

1.  `lexicographical_compare` (Lexicographical less-than comparison)

```cpp
vector<char> v1 = { 'a', 'b', 'c', 'd' };
vector<char> v2 = { 'c', 'b', 'a', 'd' };
cout << lexicographical_compare(v1.begin(), v1.end(), v2.begin(), v2.end()); // 1
```

2.  `next_permutation` (Transform range to next permutation, i.e. rearranges the elements in the range [first,last) into the next lexicographically greater permutation)
3.  `prev_permutation` (Transform range to previous permutation, i.e. rearranges the elements in the range [first,last) into the previous lexicographically-ordered permutation)

```cpp
vector<int> v = { 1, 2, 3 };
do {
    for (int i : v) cout << i << ' ';
    cout << '\n';
} while (next_permutation(v.begin(), v.end()));
// 1 2 3 
// 1 3 2 
// 2 1 3 
// 2 3 1 
// 3 1 2 
// 3 2 1 

reverse(v.begin(), v.end()); // turning {1, 2, 3} into {3, 2, 1}
cout << '\n';
do {
    for (int i : v) cout << i << ' ';
    cout << '\n';
} while (prev_permutation(v.begin(), v.end()));
// 3 2 1 
// 3 1 2 
// 2 3 1 
// 2 1 3 
// 1 3 2 
// 1 2 3 
```

## Numeric operations
1. `iota` (Fills a range with successive increments of the starting value)

```cpp
vector<int> v(5);
iota(v.begin(), v.end(), 1);
// v = {1, 2, 3, 4, 5}
```

2. `accumulate` (sums up a range of elements)

```cpp
vector<int> v = {1, 2, 3, 4, 5};
auto r1 = accumulate(v.begin(), v.end(), 0);
cout << r1 << '\n'; // 15
auto r2 = accumulate(v.begin(), v.end(), 1, multiplies<int>());
cout << r2 << '\n'; // 120
```

3. `inner_product` (computes the inner product of two ranges of elements)

```cpp
vector<int> v1 = {1, 2, 3, 4, 5};
vector<int> v2 = {1, 0, 3, 0, 1};
auto r1 = inner_product(v1.begin(), v1.end(), v2.begin(), 0);
cout << r1 << '\n'; // 15
auto r2 = inner_product(v1.begin(), v1.end(), v2.begin(), 1, multiplies<int>(), plus<int>());
cout << r2 << '\n'; // 576 (1+1)*(2+0)*(3+3)*(4+0)*(5+1)
auto r3 = inner_product(v1.begin(), v1.end(), v2.begin(), 0, plus<int>(), equal_to<int>());
cout << r3 << '\n'; // 2 (1==1) + (2==0) + (3==3) + (4==0) + (5==1) (number of equal pairs)
```

4. `adjacent_difference` (computes the differences between adjacent elements in a range)

```cpp
vector<int> v = {4, 6, 9, 13, 18, 19, 19, 15, 10};
adjacent_difference(v.begin(), v.end(), v.begin());
// v = {4, 2, 3, 4, 5, 1, 0, -4, -5}
```

5. `partial_sum` (computes the partial sums of the elements in a range)
6. `exclusive_scan` (similar to std::partial_sum, excludes the ith input element from the ith sum)
7. `inclusive_scan` (similar to std::partial_sum, includes the ith input element in the ith sum)

```cpp
vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6};
vector<int> v2(v.size()), v3(v.size()), v4(v.size());
partial_sum(v.begin(), v.end(), v2.begin());
// v2 = {3, 4, 8, 9, 14, 23, 25, 31}
exclusive_scan(v.begin(), v.end(), v3.begin(), 0);
// v3 = {0, 3, 4, 8, 9, 14, 23, 25}
inclusive_scan(v.begin(), v.end(), v4.begin(), multiplies<int>());
// v4 = {3, 3, 12, 12, 60, 540, 1080, 6480}
// partial_sum is a specialization of inclusive_scan that always uses addition as the binary operation
```