---
layout: post
comments: true
excerpt: An introduction to the art of competitive programming with C++, with emphasis on containers in this post. This is the first of a many part series.
title: Competitive Programming in C++ (Part I - STL Containers)
---

You may already know C++. Still, you may notice that others often write code faster and more concisely than you. You may wonder how others code ends up being so much shorter than yours. Why is this? More often than not, it is the knowledge they possess of the language constructs that exist in C++, that are especially suited for competitive programming. This post will help you get up to speed with the tools at your disposal, especially the Standard Template Library (STL).

The STL is a set of C++ template classes to provide common programming data structures and functions such as lists, stacks, arrays, etc. It consists of :

1. containers
2. iterators
3. algorithms
4. functions

So let us proceed through these one by one.

# Containers
The STL contains sequential and associative containers. The containers are objects that store data. The sequential containers include `vector`, `deque` and `list`. The associative containers are `set`, `multiset`, `map`, `multimap`, `unordered_set`, `unordered_multiset`, `unordered_map` and `unordered_multimap`. There are also container adapters `queue`, `priority_queue`, and `stack`, that are containers with specific interface, using other containers as implementation.

The commonly used STL containers are:

* Sequential
	* `vector` provides a dynamic contiguous array
	* `list` provides a doubly-linked list
	* `deque` provides a double-ended queue, where elements can be added to the front or back of the queue.
	* `stack` provides an LIFO data structure
	* `queue` provides a FIFO data structure
	* `priority_queue` provides a priority queue, which allows for constant-time lookup of the largest element (by default)

* Associative
	* Keys are unique
		* `set` is a collection of unique keys, sorted by keys
		* `map` is a collection of key-value pairs, sorted by keys
		* `unordered_set` is a collection of keys, hashed by keys
		* `unordered_map` is a collection of key-value pairs, hashed by keys
	* Multiple entries for the same key are permitted
		* `multiset` is a collection of keys, sorted by keys
		* `multimap` is a collection of key-value pairs, sorted by keys
		* `unordered_multiset` is a collection of keys, hashed by keys
		* `unordered_multimap` is a collection of key-value pairs, hashed by keys

STL containers are fast, have common interfaces, are well documented and implemented correctly. So you should use them as often as possible.

All STL objects are defined in a special `std` namespace. So, include the following line of code after your includes and before your code starts.
```cpp
using namespace std;
```

Before using the containers, we have to include the necessary header files. For example, for using a vector, stack or a map, we have to use the header files -
```cpp
#include <vector>
#include <stack>
#include <map> 
```

## Vector
Let us now look at the functionality of the vector, the simplest STL container. The vector is essentially an array with some extra functionality built in.

The declaration of an array is as follows:
```cpp
vector<int> v;
```

This creates a vector of int's, that can now be worked with. The vector can be of any predefined or user-defined data type, like 
```cpp
vector<string> v;
```

You can create vectors of vectors, 2D or 3D vectors too.
```cpp
vector<vector<int> > vv;
vector<vector<vector<int> > > vvv;
```

We can also create a vector of predefined size:
```cpp
vector<int> v(n); //vector of size n
vector<int> v(n,100); //vector of size n with
			//all elements initialized to 100
```

Creating multi-dimensional vectors of predefined size can be done like this.
```cpp
vector<vector<int> > vv(r, vector<int>(c,-1)); // r rows and c columns filled with -1
```

Element access is done with operator[] with no bounds checking - 
```cpp
int x = v[i]; //assuming v is a vector of int's
```

Let us look at inserting (an array of) elements into a vector. If we have an empty array, we can use a `for` loop for the insertion as follows:
```cpp
for(int i=0; i<n; i++){
    cin>>x;
    v.push_back(x);
}
```

On the other hand, if the size of the vector has already been set, the insertion can be done in the much more familiar way:
```cpp
for(int i=0; i<n; i++){
    cin>>v[i];
}
```

Let us have a quick look at iterators before continuing.
An iterator is any object that, pointing to some element in a range of elements (such as an array or a container), has the ability to iterate through the elements of that range using a set of operators (with at least the increment (++) and dereference (\*) operators).
A vector has member functions called `begin()` and `end()` that return iterators to the begin and the 'past-the-end' element in the vector container.
So to access the elements of a vector using an iterator, we would -
```cpp
cout<<"The vector contains: "
for (vector<int>::iterator it = v.begin() ; it != v.end(); ++it)
    cout<<' '<<*it;
```
Here you can use the `auto` keyword, which specifies that the type of the variable that is being declared will be automatically deduced from its initializer. So we can write - 
```cpp
for (auto it = v.begin() ; it != v.end(); ++it)
    cout<<' '<<*it;
```
We can also use a range based for loop for iterating through the elements of a container.
```cpp
for (auto i : v) // access by value, the type of i is int
    cout<<' '<<i;
```

Some member functions defined on a vector are:
1. `v.size()` – Returns the number of elements in the vector.
2. `v.capacity()` – Returns the size of the storage space currently allocated to the vector expressed as number of elements.
3. `v.resize(g)` – Resizes the container so that it contains ‘g’ elements.
4. `v.empty()` – Returns whether the container is empty.
5. `v.reserve(g)` – Requests that the vector capacity be at least enough to contain ‘g’ elements.
6. `v.clear()` – Used to clear the contents of the vector

```cpp
vector<int> v;
cout << "Size : " << v.size(); 
cout << "\nCapacity : " << v.capacity();
v.resize(4); 
if (v.empty())
	cout << "\nVector is empty";
v.clear();
```
7. `v.insert()` – Inserts elements at the specified location in the container.
8. `v.pop_back()` – Removes the last element of the container.

```cpp
vector<int> v(3,1); // v = {1, 1, 1}
v.insert(v.begin(),2); // v = {2, 1, 1, 1}
v.insert(v.begin(),2,3); // v = {3, 3, 2, 1, 1, 1}
v.pop_back(); // v = {3, 3, 2, 1, 1}
```

One important thing to remember is that when vectors are passed to functions, a copy of the vector is created that is time and memory-consuming. So we must pass vectors by reference as follows -
```cpp
void modify_vector(vector<int>& v) {
	// perform operations
}
```
We will learn more about the operations on vectors in the iterators and algorithms sections.

## Deque

A deque (double-ended queue) is an indexed sequence container that allows fast insertion and deletion at both its beginning and its end. As opposed to vector, the elements of a deque are not stored contiguously: typical implementations use a sequence of individually allocated fixed-size arrays. Indexed access to deque must perform two pointer dereferences, compared to vector's indexed access which performs only one.

In addition to all the operations of vectors, the deque supports two additional operations:
1. `d.push_front(x)` – Prepends the given element value to the beginning of the container.
2. `d.pop_front()` – Removes the first element of the container.

## List

A list is a container that supports constant time insertion and removal of elements from anywhere in the container. Fast random access is not supported (You cannot use [] to access elements). It is usually implemented as a doubly-linked list.
Here elements can be accessed using the `l.front()` and `l.back()` member functions.

A list has special member functions to help perform various operations:
1. `l.merge()` – merges two sorted lists
2. `l.splice()` – moves elements from another list
3. `l.remove(x)/remove_if()` – removes elements satisfying specific criteria
4. `l.reverse()` – reverses the order of the elements
5. `l.unique()` – removes consecutive duplicate elements
6. `l.sort()` – sorts the elements

```cpp
list<int> l1 = { 5,9,0,1,3,1 };
list<int> l2 = { 8,7,2,6,4 };
l1.reverse(); // l1 = {1,3,1,0,9,5}
l1.sort(); // l1 = {0,1,1,3,5,9}
l2.sort(); // l2 = {2,4,6,7,8}
l1.unique();  // l1 = {0,1,3,5,9}
l1.remove(0);  // l1 = {1,3,5,9}
l1.merge(l2); // l1 = {1,2,3,4,5,6,7,8,9}
```

## Stack

The stack class is a container adapter that gives the programmer the functionality of a stack - specifically, a FILO (first-in, last-out) data structure. It has the following member functions:
1. `st.push(x)` - inserts element at the top 
2. `st.pop()` - removes the top element
3. `st.top()` - accesses the top element 
4. `st.empty()` - returns true if the underlying container is empty, false otherwise
5. `st.size()` - returns the number of elements 

```cpp
stack<int> st;
st.push(5); // [5]
st.top(); // 5
st.pop(); // []
if(st.empty()) 
	cout<<"Stack is empty."; // Stack is empty
else 
	cout<<"Stack has "<<st.size()<<" elements.";
```

## Queue
The queue class is a container adapter that gives the programmer the functionality of a queue - specifically, a FIFO (first-in, first-out) data structure. It has the following member functions:
1. `qu.back()` - access the last element 
2. `qu.front()` - access the first element 
3. `qu.push(x)` - inserts element at the end 
4. `qu.pop()` - removes the first element 
5. `qu.empty()` - returns true if the underlying container is empty, false otherwise
6. `qu.size()` - returns the number of elements 

```cpp
queue<int> qu;
qu.push(5); // [5]
qu.front(); // 5
qu.push(6); // [5, 6]
qu.back(); // 6
qu.pop(); // [6]
if(qu.empty()) 
	cout<<"Queue is empty."; 
else 
	cout<<"Queue has "<<qu.size()<<" elements."; // Queue has 1 elements
```

## Priority Queue
