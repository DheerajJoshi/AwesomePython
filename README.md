Hack101: Why Python Is Awesome
-------------------------------

Have you ever tried typing `import this` in python? If you do, the following poem is printed:

> The Zen of Python, by Tim Peters
> 
- Beautiful is better than ugly.
- Explicit is better than implicit.
- Simple is better than complex.
- Complex is better than complicated.
- Flat is better than nested.
- Sparse is better than dense.
- Readability counts.
- Special cases aren't special enough to break the rules.
- Although practicality beats purity.
- Errors should never pass silently.
- Unless explicitly silenced.
- In the face of ambiguity, refuse the temptation to guess.
- There should be one-- and preferably only one --obvious way to do it.
- Although that way may not be obvious at first unless you're Dutch.
- Now is better than never.
- Although never is often better than *right* now.
- If the implementation is hard to explain, it's a bad idea.
- If the implementation is easy to explain, it may be a good idea.
- Namespaces are one honking great idea -- let's do more of those!

Writing "Pythonic" code is all about simplicity and readability.
Python is equipped with some awesome tools to help you write code in this fashion.

### 0. Pip

Being a powerful language isn't all about the built in tools. 
Python is also great because it has a very nice package manager and plenty of packages to tackle almost any problem.
You can think of packages as libraries: large amounts of code that someone else has written to complete a task, and that you can easily use in your code.

For example, lets say I wanted to find all the webpages that are linked to by an url. It's easy with Python! 
Use pip install a library called beautiful soup with:  `pip install beautifulsoup`, 
then we add beautiful soup to our program using `import`, then all we need are the following 9 (!!!!) lines of code:

```python
import httplib2
from BeautifulSoup import BeautifulSoup, SoupStrainer

http = httplib2.Http()
status, response = http.request('http://www.nytimes.com')

for link in BeautifulSoup(response, parseOnlyThese=SoupStrainer('a')):
    if link.has_key('href'):
        print link['href']
```
[Source](http://stackoverflow.com/questions/1080411/retrieve-links-from-web-page-using-python-and-beautifulsoup)


We'll learn about list comprehension which actually lets you do this in a prettier and shorter way!


One other great thing about python is that it has an interactive mode, which is great for development. 
Unlike other languages (like C or Java) which need to be compiled before they are run, 
you can write python code in a shell and see the output.
For this tutorial, I will be writing my code in the interactive mode.
I'll also be using IPython, which is a special python shell you can install with (you guessed it) pip.
IPython offers things like tab-completion and basic OS commands. 
We'll see some of its awesome features as we go through the tutorial!

Vanilla interactive python is started opening terminal and by typing
`python`, but we're going to use IPython, so instead we'll type `ipython`.
If IPython doesn't exist,you may need to run `sudo pip install ipython` 
or `pip install ipython --user`, for your home computer or a Trottier 
computer respectively. If you don't have pip, see here: [http://pip.readthedocs.org/en/stable/installing/](http://pip.readthedocs.org/en/stable/installing/)


### 1. List and Dictonary Comprehension

One common problem in programming is building lists. For example. Let's say we're working in Java and we want a list of all numbers from 1 to 100, squared.
Our first approach may be like this:

```python
array = []
for i in range(1,101):
    array.append(i**2)
```

List comprehensions let us do this all in one expression!
List comprehensions are of the form: ` array = [<expression> for <variable> in <iterable>]`.
The code above becomes:

```python
array = [i**2 for i in range(1,101)]
```

As *The Zen of Python* states, our goals are readability and beauty. 
The new code is much more readable than the previous version.
Though it may seem insignificant for such a simple list, as lists become more and more complex,
list comprehensions become infinitely more readable than their for loop-and-append couterparts.

They also have another feature, filters. Let's say we want to change the above list and only have a list of all even numbers from 1 to 100, squared.
We use the syntax `array = [<expression> for <variable> in <iterable> if <condition>]`

```python
array = [i**2 for i in range(1,101) if i%2 == 0]
```

(It would probably be better to just use `range(1,101,2)`, but then I don't get to show off as many cool python features...)

Let's revisit the example above of getting all the links in a page. Can we improve it with a list comprehension?

```python
import httplib2
from BeautifulSoup import BeautifulSoup, SoupStrainer

http = httplib2.Http()
status, response = http.request('http://www.nytimes.com')
soup = BeautifulSoup(response, parseOnlyThese=SoupStrainer('a'))

links = [link['href'] for link in soup if link.has_key('href')]
```

We can also use these comprehensions for dictionaries! Let's say we have a CSV file with Names and Phone Numbers, and we want them in a python dictionary.

The file looks like so:

```
Name1,PhoneNumber1
Name2,PhoneNumber2
.
.
.
```

We can use a dictionary comprehension to grab this really quickly!

First we use list comprehension to get every line in the file and turn it into an array that looks like this:`[ [Name1,PhoneNumber1],[Name2,PhoneNumber2],...]`. 
Since files are iterables, list comprehension is the perfect choice for this! 

Each line looks like: `Name1,PhoneNumber1\n`. 
To turn it into an array like above, we use to methods.
First we use `strip` to get rid of the `\n`, then we use `split(',')` to split it at the comma. So the above array can be made like so:

```python
with open("phonebook.csv","rt") as f:
    print [line.strip().split(',') for line in f]
```

This doesn't look too pretty though... We can also use the `csv` package that's built into python!
```python
import csv
with open("phonebook.csv","rt") as f:
    print [row for row in csv.reader(f)]
```
Much better! 

Now, we want to turn this into a dictionary, where each name is used as a key to find each phone number (which is an integer).
We do that in the same way as we did list comprehensions:
`{<expression in the form 'key: value'> for <value> in <iterable>}`. 

```python
with open("phonebook.csv","rt") as f:
    print {row[0]: int(row[1]) for row in csv.reader(f)}
```

Cool! Could it be more readable though? The `row[i]` isn't too clear... Python has one more cool trick up its sleeve! (actually it has tons more) When we're iterating over an array of arrays, we can unpack the subarrays into variables! It's best explained with an example 

```python
with open("phonebook.csv","rt") as f:
    print {name: int(num) for name, num in csv.reader(f)}
```

Now that's readable! What's happening when we have `x, y in z` is the first element of each row is assigned to `x` and the second element to `y`, then the expression is evaluated. More explicitly, here's what the loop-and-append couterpart would look like
```python
with open("phonebook.csv","rt") as f:
    phonebook = {}
    for row in csv.reader(f):
        name = row[0]    # These two lines are like:
        num = row[1]     #     name, num in csv.reader(f)
        phonebook[name] = int(num)
```

*Note:*  `name, num = row` would also be a valid operation above! The expression
`a,b = [1,2]` does the assignment `a=1;b=2`. 

We can also have a filter, like in list comprehensions.

```python
with open("phonebook.csv","rt") as f:
    # I really don't like Dan...
    print {name: int(num) for name, num in csv.reader(f) if name[:3] != 'Dan'}
```

#### Note
Normally this is how one opens a file in Python:

```python
f = open("number.csv", "rt")
# Do something with the file handler f
f.close() # Close the file handler once done
```

Here is the Pythonic way:

```python
with open("number.csv", "rt") as f:
    # Do something with the file handler f
```

What this says is "Open the file, assign it's value to f, and then execute a block of code".

The advantage of this approach is that user does not have to worry about closing files when dealing with them. Now you can do all you need within the context of the file and once you are done, the file will be closed. Even if there is an exception, the file will be closed before exiting the context of the `with` statement. The with statement is part of a bigger concept in Python called context-managers.


If you're interested in going beyond the tutorial, this would be a good place to learn about [context managers](https://docs.python.org/2/reference/compound_stmts.html#the-with-statement)!

###### Challenges

Now that we know about list comprehensions, let's do a couple challenges! They get harder as you go down the list. Try one out now!

1. (Project Euler #1): If we list all the natural numbers below 10 that are multiples of 3 or 5, we get 3, 5, 6 and 9. The sum of these multiples is 23.
Find the sum of all the multiples of 3 or 5 below 1000. -- Try to do this in one line. You may need to use Python's `sum` function, which works like so:
`sum([1,2,3]) -> 6` 
2. [Here](https://github.com/hack101/AwesomePython/blob/master/words.txt) is a list of some random words. 
Take this list (you can copy and paste it) and create a new list using dictionary comprehension which only has words that start with a vowel. 
*Note:* This list of words was created using a list comprehension! Click [here](https://github.com/hack101/AwesomePython/blob/master/words.py) if you are interested in seeing how.
3. Try using list comprehension to flatten a list. For example, if `l=[ [1,2], [3,4], [5,6] ]`, we want to turn it into `[ 1, 2, 3, 4, 5, 6]`.
4. (FizzBuzz) Write a program that prints the numbers from 1 to 100. But for multiples of three print “Fizz” instead of the number and for the multiples of five print “Buzz”. For numbers which are multiples of both three and five print “FizzBuzz”. -- Try to do this in one line. You will need to use [join](http://www.tutorialspoint.com/python/string_join.htm).
*Hint*: There are multiple solutions but one of them uses that you can multiply strings to repeat them: `"a"*5 = "aaaaa"; "a"*0 = ""`.

### 2. Lambda Expressions

In python, functions are variables. 
Sometimes we want to use them without defining them first, and sometimes we want to define them without a full `def` statement. 
Lambda expressions are how we do that. 

They have the following syntax:

```
lambda <arguments>: <what to return from arguemnts>
```

For example, the function `lambda x: x+2` is equivalent to 
```python
def function(x):
    return x+2
```

And `val` has the same value in both of these cases:

```python
val = (lambda x: x+2)(5)

def function(x):
    return x+2

val = function(5)
```

"This is cool", you may be thinking, "but why?". 
One of the primary uses of lambda expressions is that we can use functions as arguments without defining them. 

For example, when sorting a list, you may not want to use the standard sorting built in to python.
The list sort method take a keyword argument `key`. 
The value of `key` should be a function that tells the sorting method what to sort by.

Maybe you want to do a reverse sort of a list.

```python
l = [ 3, 5, 2, 6, 10, 1 ]
l.sort()
print l # this prints [1, 2, 3, 5, 6, 10]

# Now let's use key to do a reverse sort.
# We tell sort that it should sort each element by its negative value
l.sort(key=lambda x: -x)
print l # this prints [10, 6, 5, 3, 2, 1]
```

##### Challenges

1. Take the list of words [here](https://github.com/hack101/AwesomePython/blob/master/words.txt) and sort them by their third letter.
2. (This one is a little difficult!) Try writing a method to sort a list using [quicksort](http://me.dt.in.th/page/Quicksort/) in only 1 line. 
You will need both list comprehension as well as lambda expressions. Hint: recursion is fine in lambda expressions using the python ternary. 
So `fib = lambda n: 1 if n == 1 else n * fib(n-1)`.

### 3. Built-in Functions

Built-in functions are exactly that, functions that are built into python. For a full list see [here](https://docs.python.org/2/library/functions.html).

This list includes `int`, `bool`, `str`, `float`, `list`, `dict`, `set`, and `tuple`, which are used for typecasting.

We already saw the `sum` function in one of the above challenges, (this function sums all the elements of a list). 
We'll go over a few other useful built-in functions now!

- `len`: returns a length of a list, set, tuple, string, or dictionary
- `min` and `max`: There built in functions find the min and max of a list.
- `zip`: This function "zips" together two or more lists, returning a list of tuples, where each tuple contains the corresponding elements for each list it zipped. It's best understood by example.
```python
list1 = [ 1, 2, 3, 4 ]
list2 = [ "a", "b", "c", "d" ]
list3 = [ "A", "B", "C", "D" ]
print zip(list1, list2, list3)
```
This prints `[(1, 'a', 'A'), (2, 'b', 'B'), (3, 'c', 'C'), (4, 'd', 'D')]`
- `filter`: As the name suggests, this functions *filters* a list. A filter is a function which returns true or false. This function is applied to each element and only those elements which return true are kept. For example, the lambda expression `lambda x: x>5` will return true for all numbers greater than 5. Consider the following example:
```python
list = [1,2,3,4,5,6,7,8]
new_list = filter(lambda x: x>5, list)
print new_list  # prints [6, 7, 8]
```
- `map`: `map` takes a function and it to each element of a list and returns a new list. For example, if we want to (again) make a list of every number from 1 to 100 squared. we could define a function which squares a number and `map` it over `range(1,101)`.
```python
list = map(lambda x: x ** 2, range(1,101))
```
Or, if we wanted to square only the even numbers, we could first use `filter` to get the even numbers then use `map` to square them 
```python
list = map(lambda x: x ** 2, filter(lambda x: x%2 == 0, range(1,101)))
```
We don't need to use lambda expressions, we can use any *callable* object (i.e. anything you can use as a function). We can use a function we defined in the usual `def` way, or even another builtin function. For example, this turns a list of integers into strings: `map(str,[1,2,3,4,5])`
- `any`: Checks a list of booleans and returns true if any of them is true. Like `OR`ing every element. For example,
```python
if any(map(lambda x: x == 5, list)):
    print "5 is in the list!"
```
- `all`: Checks that all of a list booleans are true. Like `AND`ing every element. For example,
```python
if all(map(lambda x: x == 5, list)):
    print "This list is just 5s!!!"
```
- `reduce`: Repeatedly apply a function to a list. The function called must take two arguments. We'll walk through an example to see how it works.
```python
list = [1,2,3,4,5]
print reduce(lambda x, y: x + y, list)  # This prints 15
```
It works like so:
1. First the function is applied to the first two elements: 1 + 2 = 3.
2. Next, the function is applied to the result of the last call, and the next element: 3 + 3 = 6 (if you like mathy notation, this is *f( f(1, 2), 3)*.)
3. Again, until the list is empty. 6 + 4 = 10; 10 + 5 = 15.
We basically just recreated the sum function!


##### Challenges:

1. Use `len` and `filter` to figure out how many of the words in [this list](https://github.com/hack101/AwesomePython/blob/master/words.txt) have an even number of letters.
2. Use `map` to turn [this](https://github.com/hack101/AwesomePython/blob/master/int_strings.txt) list of strings into their integer values + 2.
3. Find the second smallest in [this](https://github.com/hack101/AwesomePython/blob/master/numbers.txt) list of integers. (Do not sort!)
4. Use reduce to find the maximum of [this](https://github.com/hack101/AwesomePython/blob/master/numbers.txt) list of integers. (Do not use `max` or sort!!!)
5. *(This one is a little tricky!)* Use reduce to get the two smallest numbers of [this](https://github.com/hack101/AwesomePython/blob/master/numbers.txt) list of integers. You will need to use the fact that `reduce` takes an option third argument which is a starting `x`. `reduce(lambda x,y: x*y, [1,2,3], 0)` will return 0, not 6. The first function call it makes puts `0` in the `x` position. The advantage of this is that you can have `x` and `y` be different types (as long as the function returns the same type as `x`). You may also need to use the fact that `float('inf')` has the property that it is larger than every number.


### 4. Generators

There are two built in functions you may have encountered in your adventures with python without any immeditately noticible difference: `range` and `xrange`.
The difference is that `range` returns an array, while xrange returns a generator. 

What's a generator? 
A generator is a function that behaves like an iterable. The purpose of an iterator is to give us data one item at a time. The simple way is just take a list and go through it, which is what range returns, a list.
A generator, on the other hand, doesn't create all the elements that will be asked for at once. It preserves its state and *generates* them one at a time, as their asked for.

This is advantageous since it's super space-efficient! Let's look write our own version of `xrange` to get a feel for why that is.
A generator has the same syntax as a function, however, we replace `return` with `yeild`. Whenever `yield` is reached, that value is returned, and the generator's state is preserved. 
When the next item is asked for the generator picks up where it left off. So our version of `xrange` might look something like this:

```python
def generator_range(range_max):
    i = 0
    while i < range_max:
        yield i
        i += 1
```

This works as follows:
1. `for i in generator_range(range_max)` is called, and the loop asks for the first element.
2. `i` is set to 0, and when yield is reached, `i` is returned. 
3. When the next item is asked for, the generator picks up again right after the last yield and increments `i`, then yields it again.
4. This repeats until the function has no more items to yield, and then the loop exits. 


What if the range is really big? Like 1,000,000? It is much more efficient to only store one variable and increment that variable up to 1,000,000 than it is to make a list of 1,000,000 variables!

We can use IPython's %%timeit magic function to see this in action (though the real gains are in space, we save time with memory allocation).

```
In [9]: %%timeit
for i in xrange(1000000):
        a = i
   ...:
10 loops, best of 3: 30.1 ms per loop

In [10]: %%timeit
for i in range(1000000):
        a = i
   ....:
10 loops, best of 3: 42.5 ms per loop
```

The built in function `list` can convert a generator into a list, if you really must...

We can also create new generators from other generators! We use the same notation as list comprehension, with regular parentheses and a generator for the iterable.

Remeber our list of squared numbers we were making before? Let's do it as a generator!

```python
squares = ( i**2 for i in xrange(1,101))
```

And now squares is a generator! Note since we didn't define it as a function, it's a one time use deal. Once we iterate over squares, there will be nothing left if we try and iterate again.

###### Note
File objects in Python are like generators! If we wanted to process a big file, we can do
```python
with open(FILENAME, 'r') as f:
    for line in f:
        ...
```
And this will only look at lines one at a time, remebering it's position each time, just like a generator! This makes searching a massive file very space efficient. 

##### Challenges
1. Write a generator that gives you all the numbers up to 100, squared, without using `xrange`. How about all the even numbers?
2. Write a generator for the generates all prime numbers. 

### 5. Argument Unpacking

Argument unpacking is a way of having functions take variable numbers of arguments. There are two ways of having a variable number of arguments

##### `*args`: Regular arguments
When defining a function, having a `*args` argument takes all extra arguments and and puts them into an array. You can then use this array any way you'd like in your function!

Just to get an idea of what's going on, try running this:
```python
def print_args(*args):
    print args

print_args()  # (,)
print_args(1,2,3,4)  # (1,2,3,4)
```

`*args` will gobble up all the positional arguments after the ones you explicitly declare (called formal arguments), and put them in an array called `args`. Note that the name `args` is not important. We could just as easily write
```python
def print_args(*foo):
    print foo
```

But as a matter of convention, it is always `args`. 

We use `*args` for when we want a function to take a variable number of regular (i.e. positional, not keyword) arguments. Let's imagine a contrived example. Let's say I want a print function which takes an int `margin` and any number of strings then prints each string with a left padding of `margin`. For example

```python
my_print(3, "Hi", "This is a test")
#   Hi
#   This is a test
my_print(5, "Now", "with", "more", "strings!!")
#     Now
#     with
#     more
#     strings
```

But in what we've seen so far, we can't have the same function take a different number of arguments! Enter `*args`. 
```python
def my_print(margin, *args):
    for s in args:
        print " "*margin + s
```    

The `*` operator can also be used to pass an array to a function as positional arguments, the inverse of the operation above.

For example, 
```python
def add(a, b):
    return a + b

vals = [2, 3]
print add(*vals)  # prints 5
```
The above puts the first element of `vals` into `a` and the second into `b`.

##### `**kwargs`: Keyword arguments

`**kwargs` is similar to above in that it gives us the opportunity to use multiple arguements, but instead of these arguments being positional and given as an array, they are *keyword arguments* and given to us as a dictionary.
Let's have a look at a simple example

```python
def print_kwargs(**kwargs):
    print kwargs

print_kwargs()  # {}
print_kwargs(val1=1, val2="test")  # {'val2': 'test', 'val1': 1}
```

Once again, there is nothing special about the name `kwargs` (`**bar` would work just as well), but it is always used as a matter of convention.

`**kwargs` is often used to have functions take a large number of settings. 

For example, let's say we want to add an optional setting to `my_print`, `lower`.
If `lower` is `True`, all the strings are converted to lowercase, otherwise not.

```python
def my_print(margin, *args, **kwargs):
    strings = args
    if 'lower' in kwargs and kwargs['lower']:
        strings = map(lambda s: s.lower(), strings)
    for s in strings:
        print " "*margin + s


my_print(3, "Hi", "This is a test")
#   Hi
#   This is a test
my_print(3, "Hi", "This is a test", lower=True)
#   hi
#   this is a test
```

Let's try add another optional argument! `start`, a string which, if present, will be used to start each line.

```python
def my_print(margin, *args, **kwargs):
    strings = args
    if 'lower' in kwargs and kwargs['lower']:
        strings = map(lambda s: s.lower(), strings)
    start = kwargs.get('start', '')
    for s in strings:
        print start + " "*margin + s

my_print(3, "Hi", "This is a test", lower=True, start=':::')
#:::   hi
#:::   this is a test
```

The `get` function used there is a method of dictionaries which
returns the value for a given key, if the key is present and returns the second argument if the key is not. Check out [here](https://www.tutorialspoint.com/python/dictionary_get.htm) if you've never seen it before. 

Like the `*` operator could be used to do the inverse of `*args`, so can the `**` operator!

In our contrived example, we could do
```python
settings = {'lower': True, 'start': '->'}
my_print(3, "Hi", "This is a test", **settings)
#->   hi
#->   this is a test
```

A cool application of this is string formatting. Python has a built in function `locals`, which when called returns a dictionary with all the local variables in the current scope.
```python
def func():
    x = 5
    print locals()

func()  # {'x': 5}
```

We can unpack this to pass a function all the current variables as kwargs!
We'll use this for string formatting. Consider the following example.
```python
name = "Bob"
amount = "10"
tax = "15"
tip = "18"
bill_stmt = "Hi {name}, you owe {amount}. There is a "\
         "{tax}% tax and we reccomend a tip of {tip}%".format(
        name=name,
        amount=amount,
        tax=tax,
        tip=tip)
```

Let's try using argument unpacking
```python
name = "Bob"
amount = "10"
tax = "15"
tip = "18"
bill_stmt = "Hi {name}, you owe {amount}. There is a "\
         "{tax}% tax and we reccomend a tip of {tip}%".format(**locals())
```

This example may seem a little contrived, but this type of string interpolation can come in handy! (Ruby has it built in, but lets not talk about that...)

##### Challenges
1. Write a `mysum` function which takes its arguments positionally rather than as a list. So `mysum(1,2) -> 3`, `mysum(5, 2, 7) -> 14`. __Optional__: Add keyword args to a) allow multiplcation or subtraction, b) print the result, c) convert all floats to ints.
2. (This one can be tricky to wrap your head around!) Write an unzip function, which is the inverse of zip. unzip should take a list of lists and unzip them. It should be one or two lines. For example:
```python
l1 = (1,2,3)
l2 = ('a','b','c')
zipped = zip(l1,l2)  # [(1, 'a'), (2, 'b'), (3, 'c')]
unzipped = unzip(zipped)  # [(1, 2, 3), ('a', 'b', 'c')]
```

### 6. Decorators

##### Note: In Python everything is an object
It's probably worth noting here that in python, everything is an object, including functions!! Above when we saw `f = lambda x: x+2`, we were able to assign a variable to a function because functions are just objects, like any other variable! So below, when we define a function which takes another function as its argument, that's totally fine! As far as python is concerned, taking a function as an argument is the same as taking an integer as an argument. The same goes for returning a function from a function.  

Let's illustrate this with an example.
```python
def func(x):
    return x + 5

def add_10(f):
    def h(x):
        return f(x) + 10
    return h

print func(20)  # prints 25
g = add_10(func)
print g(20)  #prints 35
```

Here we first made a function which takes an integer and adds 5.
Next we made a function which takes a function as its argument,
defines a new function, which adds 10 the result of its argument,
then returns the new function. Just like `func` took an integer and returns an integer, `add_10` takes a function and returns a function. 

__Now back to decorators!__

A lot of the time, while programming, we find ourselves writing a whole load of similar functions. 
Remember, pythonic code is all about beauty and simplicity, and part of that is not repeating yourself. ("Keeping it *D.R.Y.*" == Don't Repeat Yourself).
Decorators are python's solution to many similar functions. 

A decorator is a function which wraps another function. 
For example, let's say a lot of your functions are throwing errors and you want to ignore them.
You might do something like this:

```python
try:
    function1()
except:
    pass

try:
    function2()
except:
    pass

try:
    function3()
except:
    pass
.
.
.
```

But you're repeating yourself! We're wrapping each function in a try/catch, so let's use a decorator to do that more easily.

First we define a function which takes a function as it's argument and returns a new function which does not throw errors. 

```python
def noerrors(func): 
    def wrapper(*args): # define a new function which will be the function we return
        #inside this function, call the first function and catch all errors
        try:
            func(*args)
        except:
            pass
    return wrapper
```

We use `*args` here since we don't know that arguments `func` may take and we want to pass them all along.

We now want to apply this new function to our function. Let's make a function which raises an error just to see how it this might work.

```python
def function():
    raise Exception()

function() # This raises an error, obviously. 

 # now we wrap the function to stop errors
function = noerrors(function)

function() # No errors!!!
```


Decorators are just a shorthand for doing exactly what we did above! Instead of redefining `function` like we did above, we can write

```python
@noerrors
def function():
    raise Exception
```

This is entirely equivalent to `function = noerrors(function)`!

Of course, we could have found a package on pip to do this for us... [https://pypi.python.org/pypi/fuckit/4.8.0](https://pypi.python.org/pypi/fuckit/4.8.0).

What if we want to change our decorator each time? Let's say we want a decorator that only catches `NameError`s, and another that only catches `ArithmeticError`s?

We could write a different decorator for each type of exception, but then we'd be repeating ourselves, and that's not what we're all about.
Instead, we would like to pass an argument to our decorator telling it which exception to ignore. 
The problem is that decorators can take one argument and one argument only, the function they are on top of. 
Remember, when we use a decorator, it is equivalent to doing `function = decorator(function)` How do we pass extra arguments here!?

Our goal is to be able to write this:
```python
@decorator(<decorator arguments>)
def function():
    ...
```

For this we use *callable objects*

In Python, there are certain methods that can de defined when writing a class that give the class certain properties.
All these methods look like this `__method__()`. For example, a constructor in python looks like this:

```python
class MyClass(object):
    def __init__(self, value):
        # this is a constructor
        self.value = value
```

Another useful such method is `__iter__`, which, (along with a function called `next()`) makes your object an iterable object.
I.e. an object which lets you use `for x in MyClass`. 

The method we will use in this case is `__call__`. If an object has a call method, then it is callable and can be used similarly to a function. Here is an example.

```python
class Adder(object):
    """
    Creates an object which will act as an adder, adding a given amount to every number it is called on
    """
    def __init__(self, amount):
        self.amount = amount

    def __call__(self, x):
        return self.amount + x

# create an adder which will add 5 to a number
adder = Adder(5)
x = adder(10) #  x => 15
```

Let's look back at decorators. All a decorator does is translate this:

```python
@decorator
def function(args):
    # stuff
    pass
```

to:

```python
def function(args):
    # stuff
    pass

function = decorator(function)
```

So our decorator doesn't need to be a function, it just needs to be callable!
Let's leverage callable objects.

Instead of our `noerrors` function above, let's define a class which accepts as a constructor argument a specific exception to ignore.

```python
class noerrors(object):
    def __init__(self, exception=Exception):
        self.exception = exception

    def __call__(self, function):
        def wrapper(*args):
            try:
                function(*args)
            except self.exception:
                pass
        return wrapper

@noerrors()
def function1():
    raise Exception()

@noerrors(ArithmeticError)
def function2():
    raise NameError()

@noerrors(ArithmeticError)
def function3():
    raise ArithmeticError()


function1() # Nothing happens

function2() # Raises Exception

function3() # Nothing happens
```

##### Challenges
1. Create a decorator `paragraph` which is for functions which return strings. The decorator shold return the strings inside `<p></p>` HTML tags.
2. Create a decorator `HTMLTag` for functions which retun strings. This decorator should wrap all the returned strings in an HTML tag. The decorator should accept an argument which is the tag type.
3. Create a decorator which counts how many times a function is called and prints this count after each call.





### I want more!
Then check out [this](http://book.pythontips.com/en/latest/index.html) awesome online book! It covers everything seen today and more.
