
### V-ENVS
*source of truth:* [Python venv: How To Create, Activate, Deactivate, And Delete • Python Land Tutorial](https://python.land/virtual-environments/virtualenv#:~:text=In%20this%20article%2C%20you%20will%20learn%3A%201%20The,a%20venv%205%20How%20a%20venv%20works%20internally)

Venvs are created to manage python packages/libraries for specific projects, they are used for a variety of reasons but a primary use case is so that programs that run on specific version of a package don't lose functionality if the package is upgraded system-wide, it will still have access to the particular package version in the Venv

**VENV alternative / exporting VENVs**
Alternatively to a VENV you can always develop in a docker container, this provides a portable dev environment which is something Venvs don't provide, you can bind mount your development materials and then exec into the container to install/develop/test. To "export" Venvs to a diff machine you must run a `pip freeze > requirements.txt` and then export the file and reinstall the packages in a new Venv on the target machine with `pip install -r requirements.txt`

If you are running python 3.4+ you can simply use the Venv module integrated into python, If you are under 3.4 you will have to install it with `pip install virtualenv`

**Commands to manage V-ENVS**
- create new V-ENV: 
```bash
Python -m venv [directory to target] # VENVs are created/stored in a specific target directory

virtualenv [directory to target] # for the pip installed virtualenv package
```

- To activate a new VENV: 
```
When the Venv is created in a dir, it creates activation scripts

Windows: 
cmd: [venv dir]\Scripts\activate.bat
powershell: [venv dir]\Scripts\Activate.ps1

Linux: 
[venv dir] myvenv/bin/activate

```


# List modification

### splitting/appending
you can append items to a list with the append function:
```python
somelist = []
for x in something:
	somelist.append(x)
```

you can split something into a list using split:
```python
# split based on spaces
string1 = "something something2 some3"
newlist = string1.split(" ")
#output// >>> print(newlist) >>> ['something', 'something2', 'some3']


# split based on commas
string1 = "something,something2,some3"
newlist = string1.split(",")
#output// >>> print(newlist) >>> ['something', 'something2', 'some3']
```

### removing empty elements
you can remove empty elements from a list with a filter:
`list2 = list(filter(None, list1))`


### removing certain identical elements
you can remove certain identical elements from a list by converting to a set or by converting to dict and then back to list.  [python - Removing duplicates in lists - Stack Overflow](https://stackoverflow.com/questions/7961363/removing-duplicates-in-lists)
``` python
some_list = [1,2,3,4,4]

newlist = list(dict.fromkeys(some_list)) #this uses a dictionary conversion to remove duplicates but maintain the list order, order is guaranteed to be maintained


newlist = list(set(some_list)) # converts to a set and then back to list, a set is an unordered collection of unique values, order is not guaranteed to by maintained
```

### sorting a list
you can sort a list using the `sorted` function:
``` python
str1 = [-1, 1, 9, 43, -98]

str1 = sorted(str1) # makes new list str1 and sorts it
str1[:] = sorted(str1) # uses slice assignment to sort the original list more on slice assignment below

# output = [-98, -1, 1, 9, 43]
```



### Shallow copies / slice notation

In Python, `list[:]` creates a shallow copy of the entire list. Here's what it does in detail:

- `list` is your original list.
- `[:]` is slice notation. When used like this, it specifies a slice that starts from the beginning of the list and goes to the end.
- The result is a new list that contains all the elements of the original list
```python
original_list = [1, 2, 3, 4, 5] 
copy_of_list = original_list[:]  

print(copy_of_list)  # Output: [1, 2, 3, 4, 5]`

```


### How to modify a list in place (slice assignment)

`list[:]` creates a shallow copy of the list. However, when used on the left-hand side of an assignment (like `nums[:] = ...`), it doesn't create a copy. Instead, it updates the content of the original list in place. 
e.g.
```python

SomeList = [1, 2, 3, 4, 5] 

SomeList[:] = list(set(SomeList)) # this removes all duplicates from the list in place, maintaing the initial list

# as opposed to:
someList = []; somelist = list(set(someList)) # which creates a new list and modifies that, even though it has the same name the original list is no longer referenced by `SomeList`, and the original list is effectively discarded if it has no other references.
```

When you use slice notation on the left side of an assignment, you're telling Python to replace the elements of the original list with the elements on the right-hand side.
 You can also use slice  to do other target modifications:
 ```python

# view it as [x:y] x is the index that starts the operation and y is the index that ends the operation. to make it easy use programming counting starting from 0 for the first index and true counting for the second value, e.g. [3:4] would be 0 to 3 = 4 for value one and 1 to 4 = 4 so this would target precisely the 4th index.

#replace whole list
numbers = [1, 2, 3, 4, 5] 
numbers[:] = [10, 20, 30]  # Replaces the entire list content print(numbers). Output: [10, 20, 30]

#replace set in list
numbers = [1, 2, 3, 4, 5] 
numbers[1:4] = [20, 30]  # Replaces the sublist [2, 3, 4] with [20, 30] print(numbers). Output: [1, 20, 30, 5]

#replace specific value
SomeList = [1, 2, 3, 4, 5]
SomeList[3:4] = [40] # Replaces the element at index 3 (which is 4) with 40. Output: [1, 2, 3, 40, 5]

#insert a new value
SomeList = [1, 2, 3, 4, 5] 
SomeList[3:3] = [40] # Inserts 40 at index 3 (element 4), pushing the existing elements to the right. Output: [1, 2, 3, 40, 4, 5]

#delete specific value(s)
SomeList = [1, 2, 3, 4, 5, 3, 6] 
del SomeList[1:3] # Removes elements at positions 1 to 2 (2, 3). Output: [1, 4, 5, 3, 6]

```



list zipping

del list[index]


---

# Modules v Libraries
### **Modules:**

- **Definition**: A module is a single file containing Python code. It can define functions, classes, and variables, and it can also include runnable code.
- **Usage**: Modules help organize code by breaking it into smaller, manageable parts. For example, a module might be named `math_operations.py` and contain functions for various mathematical operations.
### **Libraries:**

- **Definition**: A library is a collection of modules and packages bundled together to provide a set of related functionalities. It is a broader term that can encompass many modules.
- **Usage**: Libraries offer a wide range of functionalities. For example, the `pandas` library includes multiple modules and sub-packages for data manipulation and analysis.

# Data structures
 

##### Lists [Python Lists - GeeksforGeeks](https://www.geeksforgeeks.org/python-lists/)

mutable collections
assigned with `list=[1,2,3]`

##### tuples
immutable collections of items
assigned with `something='1','2'`



##### Dictionaries
Dictionaries are used to store data in key-value pairs. Each key is unique and immutable, but values can be mutable and can include anything, such as lists, other dictionaries, integers, strings, etc.

2. **Creating a Dictionary**: 
   - You create dictionaries with curly braces: 
     ```python
     somedict = {}
     ```

3. **Adding Values**: 
   - You can add values to dictionaries by creating a key and assigning a value:
     ```python
     somedict['testkey'] = 'test value'
     ```
   - If you want a key to store multiple values, you can use a list or comma separation (tuple)
     ```python
     somedict['testkey'] = ['test value', 'other value']
      somedict['testkey2'] ='1', '2'
     ```

4. **Accessing Values**: 
   - You can access values in dictionaries by using the key name:
     ```python
     print(somedict['testkey'])  # Outputs the value associated with 'testkey'
     ```
   - you can access specific items using an index, if its a collection of values you will get the index, if it is a single value you will get the character at the index:
     ```python
     dict1 = {}
     dict1['key1'] = '1', '2'
     dict1['key2'] = 'some_value'

     print(dict1)  # Outputs: {'key1': ('1', '2'), 'key2': 'some_value'}

     # Accessing all of key 1:
     print(dict1['key1'])  # Outputs: ('1', '2')

     # Accessing the second item in key1:
     print(dict1['key1'][1])  # Outputs: '2'

     # Accessing the second character in key2:
     print(dict1['key2'][1])  # Outputs: 'o'
     ```

5. **Appending Values**:
   - You cannot directly append a value to a key in a dictionary. However, if the value is a list, you can append to that list, because the dictionary simply holds a pointer to the list the value will be reflected immediately 
     ```python
     dict1 = {}
     somelist = ['1', '2', '3']
     dict1['list_key'] = somelist

     # Append to the list
     somelist.append('4')  # Now somelist is ['1', '2', '3', '4']

     # Accessing the updated list
     print(dict1['list_key'])  # Outputs: ['1', '2', '3', '4']
     ```

### Additional Points

- **Mutability**: Values in dictionaries can be mutable (like lists) or immutable (like strings or tuples). If you assign a new value to a key, it replaces the old value.
  
- **Dictionary Methods**: Familiarize yourself with useful dictionary methods such as `.keys()`, `.values()`, and `.items()` for accessing keys, values, and key-value pairs, respectively.

- When you store a list (or any mutable object) in a dictionary, what you’re actually storing is a reference (or pointer) to that list, not a copy of it.



dataframes (bonus)







---
# Logic
*source of truth=


- **while**: while loops do something while something is true:
```
while True:
	x = input('enter something')
	 print(x)
	 
#this will infinitely prompt for input and display the input since true never changes, to break this we can use some conditional logic like if to break in response to a condition.
```


- **for**: for will increment over something and operate on each element:
```
list1 = [1, 2, 3]

for x in list1:
	print(x+1)

#increments over each value and prints the value + 1 (output: 2, 3 ,4)
```


	- for in range:   A implementation of a for loop, very useful, for in range will increment to a specified value, uses computer counting:
```
for x in range(5):
	print(x)
#increments from 0-4 (5 total), output=0,1,2,3,4

# can also be used to set a start and upper bound:
for x in range(7, 11)
	print(x)
#increments by 1 from 7-10, not 11, since the upper bound terminates

#we can also use steps to determine exactly how many values to increment by:
for x in range(0, 11, 2):
	print(x)
#increments from 0-10 by 2, output: 0,2,4,6,8,10


often used with lists like:
for x in range(len(test_list)):
```


	- for loops using enumerator iterator:   we can use the enumerate() function to go through a list, enumerate is a builtin function that creates an iterator for lists, this returns the list index + value:
```
list1=[10, 20, 30, 6]


print(list(enumerate(list1)))
#prints raw output of the enumerate function(object must be list): [10, 20, 30, 6]


for num in enumerate(list1): 
	print(num)
#will print the output of the enumerate object individually: 
# (0, 10)
# (1, 20)...

for x, y in enumerate(list1): 
	print(x); print(y)
#will iterate through both the index and value and print them individually:
# 0
# 10
# 1
# 20...
# This is particularly useful for creating hashmaps(dictionaries) that contain both an index and a value for list elements

```



- **if + if/else**: this is catch logic that responds if or if/else a condition is met:
```
while True:
	x = input(":")
	if x == '5':
	   print("you input 5!"); break
	else: print('not 5!')

# Continues to prompt for input until 5 is detected, if 5 is detected we print a message and break out of the while loop terminating the program. else handles anything but 5.


x = ""
y = "somevalue"
if x: print('true')
else: print('false')
if y: print('true')
else: print('false')
# empty variables are considered false, so we can use if to handle checking if things exist, here x will print false because its empty, and y will print true because it has a value, good way to handle empty returns from functions e.g
if "returned value": print("the func returned a value")
else: print("no value returned")
```





try except















# File modification
*source of truth=



























# --------Common Modules -------
# subprocess run / os.system module
*source of truth=*
# argparse module
*source of truth=*[Command-Line Option and Argument Parsing using argparse in Python - GeeksforGeeks](https://www.geeksforgeeks.org/command-line-option-and-argument-parsing-using-argparse-in-python/)
argparse is used to allow your program to take command line arguments 
(e.g python app.py start -c) (start and -c are the args)

**top level:** 
`pip install argparse`
`import argparse`

**usage**
To implement argparse into your program you need:

1) A parser object you always get a default -h flag with this object, description provides a default description that will display when -h is invoked, example:
```
parser = argparse.ArgumentParser(description="something")
```
2) arguments that are supplied to the parser, these will be the flags that are passed, you should supply:
1- the flag either as -something / something
2- action='store_true' which stores the flag and allows you to utilize it when supplied
3-help='something' a description of the flag that will be output when -h (help) is invoked
example of argument declaration:
```
parser.add_argument('start', action='store_true', help='something')
parser.add_argument('-c', action='store_true', help='something else')
```
3) The parser invocation to actually parse the args:
```
args = parser.parse_args()
```

4) actions to perform when the flag is invoked (specified with args.argName) example (for ...add_argument('-c'...):
```
if args.c:
	print("hello!")
```

put it together...

```
# print hello when -c is specified e.g. python app.py -c
import argparse
def main():
	parser = argparse.ArgumentParser(description="something")
	parser.add_argument('-c', action='store_true', help='something else')
	args = parser.parse_args()
	if args.c:
		print("hello!")
main()
```


