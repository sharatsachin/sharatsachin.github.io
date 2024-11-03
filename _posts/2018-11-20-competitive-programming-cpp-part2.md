---
title: Competitive Programming in C++ (Part II - STL Iterators)
date: 2018-11-20 00:00:00 +0530
comments: true
categories: [competitive-programming]
tags: [competitive-programming, c++, stl]
---

This post is a continuation on the series on the Standard Template Library (STL). It focuses on iterators, and is the second of a three part series.

<!--more-->

# Iterators
Iterators are used to point at memory addresses of STL containers, and help navigate over them with a consistent interface. Iterator is a generalized pointer, a position in a container/sequence.

A range, a pair of iterators, is the standard way to represent a sequence in STL. Most STL algoritms take a range as an argument. Iterators make the algorithms independent of the type of container used.

`begin()` and `end()` define a half-open range that includes the first element but exclude the last: `[begin(),end())`.

The half open interval has several advantages:
* `[iter,iter)` is the empty range
* the length of `[begin,end)` is end-begin
* `[A,A+N)` is the range for array `int A[N];`
* `end` can be used to signal failure of search
* loops over all elements are clean
* if mid is in range `[begin,end]`, then `[begin,end)` is `[begin,mid)[mid,end)`
* when inserting at position iter, the resulting sequence is `[begin,iter)[new][iter,end)`

The following operations are defined for iterators:
* get value of an iterator, `int x = *it;`
* increment and decrement iterators `it1++, it2--;`
* compare iterators by `!=` and by `<`
* add to iterator `it += 20;` `\\ shift 20 elements forward`
* get the distance between iterators, `int n = it2-it1;`

However not all the operations are available for all the iterators. There are 5 different types of iterators based on the operations that are available for it. They are listed from most to least powerful:
* random access (most powerful) - Store and retrieve values, move forward and backward, access values randomly
* bidirectional	- Store and retrieve values, move forward and backward
* forward - Store and retrieve values, move forward only
* input	- Retrieve but not store values
* output (least powerful) - Store but not retrieve values

Sample implementation of `find` algorithm in STL:
```cpp
template <class Iterator, class T>
Iterator find(Iterator begin, Iterator end, const T& value)
{
  while (begin != end && *begin != value)
    ++begin;
  return begin;
}
```
Each of the STL containers has a default iterator type that can be obtained in a manner analogous to:
```cpp
vector<int>::iterator it;
```

| Container | Default Iterator |Operations|
| -------- | -------- |-------- |
| vector, deque | random access |`* = ++ –> == != ––  + – [] < > <= >= += –=`|
| list, (multi)set, (multi)map | bidirectional | `* = ++ –> == != ––` |
| stack, queue, priority_queue | none |  |

## Iterator Adapters

An iterator adapter wraps an object that is not an iterator itself into an iterator. An adaptor class acts like a "translator" by "adapting" the messages you want to send to produce messages that the other class object wants to receive.

### For Streams

For instance, there are iterator adapters for streams that translate operations by STL algorithms into their approppriate `istream` and `ostream` operations. These adapters are called `istream_iterator` and `ostream_iterator` respectively.

For example, to display the contents of a vector using `ostream_iterator`, we can create a named iterator as follows, which we can use to write to the output. The second parameter denotes the value seperator

```cpp
vector<int> v = {1,2,3};
ostream_iterator<int> outStream(cout," | ");
copy(v.begin(),v.end(),outStream); //  1 | 2 | 3 | 
```

We can also use an anonymous iterator as follows:
```cpp
copy(v.begin(),v.end(),ostream_iterator<int>(cout," ")); // 1 2 3 
```
We can also use istream_iterator to read directly from standard input and write into a container such as vector.
```cpp
vector<int> v(5);
copy(istream_iterator<int>(cin),stream_iterator<int>(), v.begin());
```

We can directly read from input and write to the output while applying a transformation:
```cpp
transform(istream_iterator<int>(cin), istream_iterator<int>(),
        ostream_iterator<double>(cout, " "), [](int x) {return x*x; });
```
### For Insertion
Insert iterators allow algoirthms to operate in insert mode rather than overwrite mode, which is the default behavior. They allow the destination container to grow as needed to accomodate the contents of the source. There are three kinds of inserters, as shown in the table below:

* The `back_inserter(x)`, which can be used if the recipient container (x) supports `push_back()`.
* The `front_inserter(x)`, which can be used if the recipient container (x) supports `push_front()`.
* The `inserter(x,it)`, which can be used if the recipient container (x) supports `insert()`, at the position pointed by `it`.

Examples:
```cpp
vector<int> v = {1,2,3,4,5};
deque<int> d;
copy(v.begin(), v.end(), back_inserter(d));
copy(v.begin(), v.end(), front_inserter(d));
```
```cpp
vector<char> v(6, 'A');
copy(v.begin(), v.end(), ostream_iterator<char>(cout));
    
list<char> l(6, 'B');
copy(l.begin(), l.end(), ostream_iterator<char>(cout));
    
copy(l.begin(), l.end(), inserter(v, v.begin()+3));
copy(v.begin(), v.end(), ostream_iterator<char>(cout));
    
list<char>::iterator p=l.begin();
++p; ++p; ++p;
copy(v.begin(), v.end(), inserter(l, p)); //Note: Can't use l.begin()+3 here
copy(l.begin(), l.end(), ostream_iterator<char>(cout));

// Output
// AAAAAA
// BBBBBB
// AAABBBBBBAAA
// BBBAAABBBBBBAAABBB
```

## Useful Iterator Functions
Only random access iterators allow you to add values to iterators or subtract two iterators. We can use the following functions to perform the equivalent operations with non random access iterators.

* advance(it,n) - Advances the iterator it n times. Equivalent to `it+=n` for random iterators.
* distance(itB,itE) - Returns the number of values in the half-open interval (itB,itE]. It doesn't take negative values and should be typecast to int. Equivalent to `(itE-itB)` for random iterators.

```cpp
list<int> v = {1,2,3,4,5,6,7,8,9};
auto p = v.begin();
advance(p,7);
cout<<*p<<" "<<(int)distance(v.begin(),p)<<'\n'; // 8 7

advance(p,-5);
cout<<*p<<" "<<(int)distance(v.begin(),p)<<'\n'; // 3 2 

list<int>::reverse_iterator p_r = v.rbegin();
advance(p_r,5);
cout<<*p_r<<" "<<(int)distance(v.rbegin(),p_r)<<'\n';  // 4 5
```

