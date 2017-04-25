---
---

## Lists

Python offers different types of objects to represent collections of values,
the most common being a *list*. It is created by listing multiple values or
variables, separated by commas and enclosed by square brackets.


~~~python
>>> lst = [r, s, 'another string']
>>> lst
[4.5, 'three', 'another string']

~~~
{:.term}



===

### List subsetting

You can retrieve individual elements of a list by their index; note that in 
Python, the first element has an index of 0.


~~~python
>>> lst[1]
'three'

~~~
{:.term}



===

Negative indices are also possible: -1 is the last item in the list, 
-2 the second-to-last item, etc.


~~~python
>>> lst[-1]
'another string'

~~~
{:.term}



===

The syntax `list[i:j]` selects a sub-list starting with the element at index
`i` and ending with the element at index `j - 1`.


~~~python
>>> lst[0:2]
[4.5, 'three']

~~~
{:.term}



===

A blank space before or after the ":" indicates the start or end of the list,
respectively. For example, the previous example could have been written 
`lst[:2]`.

A potentially useful trick to remember the list subsetting rules in Python is
to picture the indices as "dividers" between list elements.

```
 0     1         2                  3 
 | 4.5 | 'three' | 'another string' |
-3    -2        -1
```

Positive indices are written at the top and negative indices at the bottom. 
`list[i]` returns the element to the right of `i` whereas `list[i:j]` returns
elements between `i` and `j`.

===

**Question**: Given any Python list, how can you retrieve its last two elements?

Lists can be nested within other lists: in this case, multiple sets of brackets
might be necessary to access individual elements.


~~~python
>>> nested_list = [1, 2, 3, [11, 12, 13]]
>>> nested_list[3][1]
12

~~~
{:.term}



===

### List methods

The Python language includes multiple functions that work with lists. 
Here are a few examples. Note that code lines starting with `#` are comments,
which serve to document the code but are ignored by the Python interpreter.


~~~python
>>> # Returns the length of a list
>>> len(lst)
3

~~~
{:.term}




~~~python
>>> # Returns the position of an element in a list
>>> lst.index(4.5)
0

~~~
{:.term}



===


~~~python
>>> # Appends an element to the end of a list
>>> lst.append(100)
>>> lst
[4.5, 'three', 'another string', 100]

~~~
{:.term}




~~~python
>>> # Reverse the order of a list's elements
>>> lst.reverse()
>>> lst
[100, 'another string', 'three', 4.5]

~~~
{:.term}



===

The last three examples feature a special type of functions called *methods*.
In object-oriented programming, methods belong to a specific object; in Python,
they are called with the `object.method()` syntax. In general, methods and 
functions operate in a similar manner; for example, `len()` could have been
a list method. 

Note that the `append` and `reverse` methods modify the `lst` object, and return
no value. A common mistake -- especially for those used to program in R --
would be to write `lst = lst.append(100)`, which overwrites `lst` with a null
value!
{:.notes}

===

**Question**: What is the output of `len(lst[2])`? What does it mean?
(Like the `+` operator, this is another case of a function that behaves
differently depending of the type of data it's applied to.)