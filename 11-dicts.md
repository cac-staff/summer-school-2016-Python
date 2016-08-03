---
layout: page
title: Programming with Python
subtitle: Storing information with Dictionaries
minutes: 20
---
> ## Learning Objectives {.objectives}
>
> *   Understand the key-value dictionary data structure.
> *   Be able to store and retrieve information from Python dictionaries.
> *   Be able to iterate through dictionary elements in an ordered manner.

Lists and arrays are useful, but don't cover every use case. One of Python's best features is its "dictionary" data structure. Whereas a list or array is simply a collection of elements, a dictionary supports key-value storage of data. Put into plain english, it lets you store and retrieve data values by name.

Let's create and use a simple dictionary to illustrate this:

```{.python}
# create an empty dictionary, then store 4 values in it
dict = {}
dict['a'] = 'cheesecake'
dict['b'] = True
dict['c'] = 3.0
dict['d'] = None

# retrieve a value from the dictionary
print('value for a is:', dict['a'])
```
```{.output}
value for a is: cheesecake
```

What we did here is create a dictionary, store 4 values under the letters a-d, then retrieve element a's value. 

Note that we could have initialized the dictionary with the following syntax as well:
```{.python}
dict = {'a': 'cheesecake', 'b': True, 'c': 3.0, 'd': None}
print('value for a is:', dict['a'])
```
```{.output}
value for a is: cheesecake
```

## Using dictionary keys

What if we wanted to see what was in the dictionary? How do we get all of the names (keys) that data is stored under?
```{.python}
print(dict.keys())
```
```{.output}
dict_keys(['c', 'd', 'b', 'a'])
```


We can iterate through all of the keys like this:
```{.python}
for key in dict.keys():
    print('key:', key, '- value:', dict[key])
```
```{.output}
key: b - value: True
key: d - value: None
key: c - value: 3.0
key: a - value: cheesecake
```


Note that the output is inherently unordered. If we re-run creating the dictionary, and getting the keys, we will likely see the keys come out in a different order. This, of course raises an obvious question? How do we iterate through keys in order?

As it turns out, getting the keys in order is as simple as using the sorted() function on the output of `dict.keys()`.
```{.python}
ordered = sorted(dict.keys())
print(ordered)
for key in ordered:
    print('key:', key, ', value:', dict[key])
```
```{.output}
['a', 'b', 'c', 'd']
key: a , value: cheesecake
key: b , value: True
key: c , value: 3.0
key: d , value: None
```

## Using dictionary values

To get the values of the data stored in the dictionary, we can call `dict.values()`. As before, the output will be inherently unsorted. 
```{.python}
print(dict.values())
```
```{.output}
dict_values([True, None, 3.0, 'cheesecake'])
```

## Removing dictionary elements

To simply delete a dictionary element, we can use the `del` keyword.
```{.python}
del dict['a']
print(dict.keys())
```
```{.output}
dict_keys(['c', 'd', 'b'])
```

To remove an element AND save its value, we can call `dict.pop()`. This is particularly useful if we want to do something with a dictionary element, then remove it after we're done with it.
```{.python}
bval = dict.pop('b')
print(bval)
print(dict.keys)
```
```{.output}
True
dict_keys(['c', 'd'])
```


