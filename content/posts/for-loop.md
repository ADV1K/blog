---
title: "How does the for loop work in Python?"
date: 2023-02-28T01:02:33+05:30
draft: true
---

Most container objects in Python can be looped over using a `for` statement.  
Behind the scenes, the `for` statement calls the built-in `iter()` function on the container object which returns an iterator.  
The iterator object has the `__next__()` method which is used to access elements in the container one at a time.  
When there are no more elements left, `__next__()` raises the `StopIteration` exception which tells the for loop that we're done.

And this is how the for loop works in Python.

## Phew... that was complicated!
Let's see how it all works using an example.

Let's say we have a string `"Hi!"` that we want to loop over. Firstly, we need to create an iterator using the `iter()` built-in function.
```python
>>> msg = "Hi!"
>>> it = iter(msg)
>>> it
<str_iterator at 0x7fc89510a7a0>
```
\
To get the next element from an iterator, we call the `next()` built-in function on the iterator (not the container object).
```python
>>> next(it)
'H'
>>> next(it)
'i'
>>> next(it)
'!'
```
\
When there are no more elements left `next()` raises the `StopIteration` exception, which tells us that we're done.
```python
>>> next(it)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
    next(it)
StopIteration
```
\
So, our for loop which loops over a container...
```python
msg = "Hi!"
for c in msg:
  print(c)
```
\
...becomes
```python
msg = "Hi!"
it = iter(msg)
while True:
  try:
    c = next(it)  # get the next element
    print(c)
  except StopIteration:  # we're done.
    break  # GTFO.
```

## Creating your own container class
To make our own iterable class, we need to implement either `__getitem__()` or both `__iter__()` and  `__next__()`.  

### Using `__getitem__()`
`__getitem__()` is used to implement `container[index]` syntax.  
It allows us to treat custom objects like lists and index them.  
The for loop can use this information to iterate over custom container objects.  

```python
class Count:
  def __init__(self, start_count=0):
    self.count = start_count

  def __getitem__(self, index):
    # return control back to user
    return self.count + index

# This keeps on counting the numbers from 0 till infinity
for i in Count():
  print(i)

```

### Using `__iter__()` and `__next__()`
In `__iter__()` we are just returning self, because we're also implementing `__next__()` which is used to get the next element.
The difference between using `__iter__()` and `__getitem__()` is that the latter also allows us to index a sequence, and sometimes that might not make much sense.
```python
# We inherit from the Random class to make our lives easier
class RandomSequence(Random):
  def __iter__(self):
    return self

  # This allows us to iterate over the sequence, which we can't do with Random class
  def __next__(self, lower_bound=0, upper_bound=100):
    # return control back to user
    return self.randint(lower_bound, upper_bound)

rng = RandomSequence()
print(next(rng))  # get the next random number
print(next(rng))  # this will never end
```

## Conclusion
There are many python built-ins that implement these special methods, like `open()`. This allows us to iterate over the lines of a file.  
\
Another example is the `range()` built-in which computes values on the fly, this is both memory and time efficient.  
\
This is called a Generator because it only generates the values that we need, when we need them and returns the control back to the user after it's done.  
More on Generators later...
