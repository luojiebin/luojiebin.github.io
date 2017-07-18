---
title: Use Python Collections the Right Way
date: 2017-07-18 16:57:57
---

In this blog, I will introduce the most commonly used Python collections (**list**, **tuple**, **dict**, **set**, **ChainMap**, **Defaultdict**, **OrderedDict**, **Deque**, **Heapq**, **NamedTuple**, **bisect**), their algorithmic compleity and what should be paied attention to when using them. Instead of explaining them in detail, I will only talk about something important . For detail tutorial, please read the official Python document.

## list
List is the most usually used data structure in Python, it's convenient and easy to use. In most cases, it can meet our need. However, their are something we should take care of. `append`, `get`, `len` all take O(1), but `remove` and `insert` have O(n) time complexity because the two have to iterate the whole list. If you have to execute lots of deletions, you had better use filter or list comprehension. `min`, `max`, `in` all take O(n) time complexity with list.

## tuple
Tuple can be treated as the immutable list. It can be used like a list but can't be changed. We can not change a tuple, but we can change a item in the tuple if the item is a mutable one. For example:

```python
>>> foo = (1, 2, [3])
>>> foo[0] = 4
Traceback (most recent call last):
  File "<stdin>", line1, in <module>
TypeError: 'tuple' object does not support itme assignment
>>> foo[2].append(4)
>>> foo
(1, 2, [3, 4])
>>>
```

It can be a key of **dict** if its items are immutable too because then it's hashable. Pythonista often use tuple packing and tuple unpacking, which can make assignment to more than one variable at the same time and lead to clean and compact code.
```python
>>> foo, bar, spam = 0, 1, 2
>>> foo, bar, spam
(0, 1, 2)
```
We can even pack and unpack objects with a variable number of items (Python 3):
```python
>>> foo, bar = 0, 1, 2, 3
>>> foo
0
>>> bar
[1, 2, 3]
>>> foo, bar = range(4)
>>> foo
0
>>> bar
[1, 2, 3]
>>> a, b, *c = foo, *bar
>>> a, b
(0, 1)
>>> c
[2, 3]
```
Tuple packing and unpacking can be used with function arguments, that is how functions with a variable number of arguments work.

## dict
Dict can be viewed as a dictionary with a collections of key-val pairs, it can be used as a hash table. Its key must be hashble and has a special method `__hash__` which converts the key to a hash. It's unsorted and will return items in random order when iterating. Thanks to the hash mechanism, `get`, `set` and `delete` have O(1) time complexity and it's really fast. However, when deleting items from a dict, its memory won't be actually resized automatically. To work around this issue, you can recreating the dictionary. Operations such as `copy` and `insert` recreat the dict internally to be memory efficient.

## set
Set is like a **dict** without values. It's often used to perform membership testing with the operator `in` because the time complextiy is O(1) then. Furthermore, it can be use like a set in math with `&`, `|`, `^`, `-`, `>`, `<`. The set function takes a sequence as argument.
```python
>>> foo = set([0, 1, 2, 3])
>>> bar = set('bugs')
>>> bar
{'u', 'b', 'g', 's'}
>>> spam = set(['bugs'])
>>> spam
{'bugs'}
```
It can result in bugs when used with strings easily if the programmer ignore this point.

## ChainMap
ChainMap meas *chain mappings*, it can be used to combine many dictionary to one.
```python
import collections
d1 = {'a':1, 'b':2}
d2 = {'c':3, 'd':4}
d3 = {'e':5, 'f':6}
mappings = collections.ChainMap(d1, d2, d3)
```
`mappings` now become a *dict* with all the key-value pairs in `d1`, `d2`, `d3`.

## defaultdict
Defaultdict is like a **dict** but with a default value. The default value for defaultdict needs to be a callable object.
```python
>>> import collections
>>> counter = collections.defaultdict(int)
>>> counter['a'] = 1
>>> counter['b'] += 3
>>> counter['a']
1
>>> counter['b']
3
```
In this case, if `counter` is a normal dictiionary, we have to test if couter has a key 'b' and so forth.

## OrderedDict
OrderedDict can be used as a normal dictionary, but it care about the insertion order. Whereas a normal dict will return your keys in the order of hash, OrderedDict will return your keys by the order of insertion. To be a dictionary and store insertion order, **OrderedDict** uses two **dict** internally, so its memory usage is twice as the normal one.

## Deque
Deque is a double ended queue so that it can add or remove items from both ends. It is created as a doubly linked list.
```python
>>> import collections
>>> queue = collections.deque()
>>> queue.append(1)
>>> queue.append(2)
>>> queue.append(3)
>>> queue
deque([1, 2, 3])
>>> queue.pop()
3
>>> queue
deque([1, 2])
>>> queue.popleft()
1
>>> queue
deque([2])
```
It can be used as a *queue* or *stack*. Deque can be also used to keep the last n status messages when the parameter `maxlen` is passed in.
```python
>>> import collections
>>> messages = collections.deque(maxlen=2)
>>> for i in range(6):
...    messages.append(i)
>>> messages
deque([4, 5], maxlen=2)
```
When the number of items exceeds `maxlen`, the old one will be removed.

## namedtuple
Namedtuple is like a tuple, but has fild names, its fild value can be accessed both by name and by index.
```python
>>> import collections
>>> Color = Color(5, 7, 8)
>>> color
Color(R=5, G=7, B=8)
>>> color.R
5
>>> color[0]
5
>>>
```
It is very conevient to use **namedtuple** when dealing with RGB color representation.

## heapq
Heapq keeps items in a *heap* order and can be used to create a priority queue.
```python
>>> import heapq
>>> heap = [9, 8, 7, 3, 2, 1]
>>> heapq.heapify(heap)
>>> heap
[1, 2, 7, 3, 8, 9]
>>> heapq.heappop(heap)
1
>>> heapq.heappop(heap)
2
>>> heap
[3, 8, 7, 9]
>>>
```
It will pop items in order and can be used to implement heap sort.

## bisect
Bisect can insert items to a list and keep it sorted.
```python
>>> import bisect
>>> numbers = [1, 4, 5, 9]
>>> bisect.insort(numbers, 2)
>>> numbers
[1, 2, 4, 5, 9]
>>> bisect.bisect_left(numbers, 2)
1
>>> bisect.bisect_left(numbers, 6)
4
>>>
```
`bisect.bisect_left(a, x)` will return the index where to insert item x in list a, assuming a is sorted. The return value i is such that all e in a[:i] have e < x, and all e in a[i:] have e >= x. So if x already appears in the list, i points just before the leftmost x already there. `bisect.bisect_left` can be used to implement binary search conveniently. And there is a similar `bisect_bisect_right`.
