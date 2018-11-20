---
layout: post
comments: true
title: Competitive Programming in C++ (Part III - STL Algorithms)
---

This post is a continuation on the series on the Standard Template Library (STL). This is a quick summary of some of the most useful algorithms in the STL, and is the third of a many part series.

<!--more-->

# Algorithms
Algorithms are functions that operate on ranges of elements in various ways - searching, sorting, counting, manipulating. These functions take iterators, not containers. Many of them also take functional arguments. What you can do with generic algorithms, iterators, and functional arguments can be pretty amazing.

## Functions, Functors and Lambda expressions
The `copy_if` algorithm copies the elements in the range `[first,last)` for which `pred` returns `true` into the range beginning at result. The function returns an iterator to the end of the destination range (which points to the element following the last element copied). It is defined as :
```cpp
OutputIterator copy_if (InputIterator first, InputIterator last, OutputIterator result, UnaryPredicate pred);
```
Here, the unary predicate can be supplied in different ways: using functions, functors or lambda expressions.
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
bool div2(int x){return x%2==0; }
class divBy{
private:
	int d;
public:
	divBy(int a): d(a){}
	bool operator()(int x) {return x%d==0; }
};
int main() {
	vector<int> v = {1,2,3,4,5,6,7,8,9,10};
	vector<int> a, b, c;
	copy_if(v.begin(), v.end(), // using simple function
			back_inserter(a), div2);
	// a = {2,4,6,8,10}
	
	int n = 3;
	copy_if(v.begin(), v.end(), // using functor
			back_inserter(b), divBy(n));
	// b = {3,6,9}
	
	n = 5;
	copy_if(v.begin(), v.end(), // using lambda expression
			back_inserter(c), [n](int x){return x%n==0; });
	// c = {5,10}
	
	return 0;
}
```

Functions are usually the easiest to use, but they do not allow any opportunity for customisation. 

A functor is a class that acts like a function. Functors are called using the same old function call syntax. To create a functor, we create a object that overloads the operator(). Unlike functions, functors have state and can be used with more customizability. Learn more about functors [here.](https://stackoverflow.com/q/356950/4542098)

Lambda expressions make creation of simple functor classes much easier. Behind the scenes, they force the compiler to create an unnamed functor class. However, they are much less verbose, and cause no namespace pollution. More about them [here.](https://stackoverflow.com/q/7627098/4542098)

## Algorithm Library

Following is a list of the most common and useful algorithms for competitive programming available in the STL.

#### 1. `all_of/any_of/none_of`:

This function returns true if the unary predicate is true for all/any/none of the elements in the range respectively.
```cpp
vector<int> v = {1,2,3,4,5,6,7};

```
