
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
go to v-env dir then run
source /bin/activate
```

- To deactivate a VENV:
```bash
# run
deactivate



```


---

# Typehints
used to indicate variable types in functions and the desired variable return type:
```
def greeting(name: str) -> str:
    return 'Hello ' + name
```

we use these for clarity
```
def greeting(y: str, x: int) -> str:
    return 'Hello ' + y + x
    
#is much easier to understand than:

def greeting(y, x):
    return 'Hello ' + y + x
```

common type hints are:
`str` - string
`int` - integer
`float` - floating point
`bool` - T/F
`list` - list
`List[str/int]` - listed of strings/integers
`List[List[str/int]]` - nested list / nested list of some type e.g.`[[1,2,3],[4,5,6]]`

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

#### joining a list
you can join a list (make into string) with this command:
```
x="".join(list)
```


### removing certain identical elements
you can remove certain identical elements from a list by converting to a set or by converting to dict and then back to list.  [python - Removing duplicates in lists - Stack Overflow](https://stackoverflow.com/questions/7961363/removing-duplicates-in-lists)
``` python
some_list = [1,2,3,4,4]

newlist = list(dict.fromkeys(some_list)) #this uses a dictionary conversion to remove duplicates but maintain the list order, order is guaranteed to be maintained


newlist = list(set(some_list)) # converts to a set and then back to list, a set is an unordered collection of unique values, order is not guaranteed to by maintained
```

### list sorting
sorting by default changes the list to be in ascending order, you can invoke reverse=True to get descending:
```
aList.sort()
aList.sort(reverse=True)

```

## Get index/value
to get the index of a value in a list use list.index
```
mylist.index("somevalue")
```

To get the value use `list[index]`
```
mylist[4] #gets index 4 (actually 5, count from 0)
```

## 2D lists (nested lists)
sometimes you will need to nest lists to create a "matrix"/grid:
```
nested_list = [
    [1, 2, 3],  # Row 0
    [4, 5, 6],  # Row 1
    [7, 8, 9]   # Row 2
]
```

### removing all list occurrences:
to remove all elements of x in a list you can use this logic:
```python
while foo in list:
	list.remove(foo)
```
we have to iterate over the list until the element no longer exists since .remove only removes the first occurrence.

### list zipping
In Python, the zip() function is used to combine two or more lists (or any other iterables) into a single iterable, where elements from corresponding positions are paired together. The resulting iterable contains tuples, where the first element from each list is paired together, the second element from each list is paired together, and so on.
[zip() in Python - GeeksforGeeks](https://www.geeksforgeeks.org/zip-in-python/#)


---

## Shallow Copies and Slice Notation

#### Modifying a Sequence In Place (Slice Assignment)

**notes**
-In Python, list slicing allows out-of-bound indexing without raising errors. If we specify indices beyond the list length, Python will simply return the available items.

-first notation is inclusive (begins at 0), second is exclusive: `[3:5]` = from 3 (inclusive) to 4 (exclusive) (counting from 0)

**-These can be used for both lists and strings! e.g:**
```
list1=['a','b','c','d','e']
string1='abcde'
print(list1[1:3]); print(string1[1:3])
#^slices the same way in both, prints the same
```


In Python, using `sequence[:]` creates a shallow copy of the entire sequence (works for both lists and strings). Here's how it functions:

- `sequence` refers to your original list or string.
- `[:]` is slice notation, which specifies a slice that starts from the beginning and extends to the end of the sequence.
- The result is a new sequence containing all elements from the original.

```python
# For Lists
original_list = [1, 2, 3, 4, 5] 
copy_of_list = original_list[:]  
print(copy_of_list)  # Output: [1, 2, 3, 4, 5]

# For Strings
original_string = "hello"
copy_of_string = original_string[:]  
print(copy_of_string)  # Output: "hello"
```


**selecting via slice**
get all items from a specific position
```
a = [10, 20, 30, 40, 50]

# before (from 0-2, excludes 3 )
print(a[:3]) #10, 20, 30

#after (from 3 - x includes 3)
print([a[3:]]) # 40, 50

#get items using negative notation (from end) end is -1 steps by -1 backwards
print(a[-3:-1]) # 30, 40 (-1 still exclusive)

```

select all items at intervals (step operator)
```
a = [10, 20, 30, 40, 50]

#gets all intervals of 2 starting at 0 (0,2,4...)
print(a[::2]) # 10, 30, 50 
```

to get a section of a list in standard left-right you can slice it e.g. `[2:5]` to get a reverse slice of the list you iterate from the end of the list (-1) to the desired value, you must step by -1 for this to work.
```
a = [10, 20, 30, 40, 50, 60, 70]

#get slice from left to right
print(a[2:5]) #30,40,50

#get slice from right to left (STEP BY -1)
print(a[-2:-5:-1]) #60,50,40

```

### cutting a list/string
you can cut a set of values off a list or string with `[x:y]` where x is number of letters to cut off the front/y the back
```
list1=['a','b','c','d','e']
string1='abcde'

print(string1[2:])
print(list1[2:])

'''
cuts the first two indexes from the list & string, outputs:
cde
['c', 'd', 'e']
'''
```


Using `sequence[:]` on the left side of an assignment (like `nums[:] = ...`) modifies the original sequence in place. This behavior applies to lists, but not strings, as strings are immutable. For example:

```python
# Only works for lists
SomeList = [1, 2, 3, 4, 5] 
SomeList[:] = list(set(SomeList))  # Removes duplicates in place, maintaining the original list.

# Contrast with:
someList = []  
someList = list(set(someList))  # This creates a new list, and the original is no longer referenced.
```



When using slice notation on the left side, you're instructing Python to replace elements of the original list. You can perform various targeted modifications:

```python
# Slicing works as [x:y], where x is the start index (inclusive) and y is the end index (exclusive).
# For example, [3:4] targets the 4th index.

# Replace the entire list:
numbers = [1, 2, 3, 4, 5] 
numbers[:] = [10, 20, 30]  # Output: [10, 20, 30]

# Replace a subset within the list:
numbers = [1, 2, 3, 4, 5] 
numbers[1:4] = [20, 30]  # Output: [1, 20, 30, 5]

# Replace a specific value in a list:
SomeList = [1, 2, 3, 4, 5]
SomeList[3:4] = [40]  # Output: [1, 2, 3, 40, 5]

# Insert a new value in a list:
SomeList = [1, 2, 3, 4, 5] 
SomeList[3:3] = [40]  # Output: [1, 2, 3, 40, 4, 5]

# Delete specific value(s) from a list:
SomeList = [1, 2, 3, 4, 5, 3, 6] 
del SomeList[1:3]  # Output: [1, 4, 5, 3, 6]
# Or delete a specific index:
del SomeList[3]  # Removes the value at index 3 (which is 4).
```

### Reversing a Sequence

Using `[::-1]` works for both lists and strings and creates a reversed copy:

```python
# For Lists
reversed_list = SomeList[::-1]  # Reverses the list.

# For Strings
original_string = "hello"
reversed_string = original_string[::-1]  # Output: "olleh"
```




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
assigned with `[]` e.g. `list=[1,2,3]`

##### tuples
immutable collections of items
assigned with `()` e.g. `something=('1','2')`

#### Dictionaries
Dictionaries are used to store data in key-value pairs. Each key is unique and immutable, but values can be mutable and can include anything, such as lists, other dictionaries, integers, strings, etc. assigned with `{}`

NOTE: In Python, a dictionary's values can be of any type, but if you want to store multiple non-list values (like multiple integers or floats) under a single key, you need to group them together in a list or tuple.

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

     # Accessing the second item in key1 if its a list:
     print(dict1['key1'][1])  # Outputs: '2'

     # Accessing the second character in key2 if its a list:
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

###### Additional Points

- **Mutability**: Values in dictionaries can be mutable (like lists) or immutable (like strings or tuples). If you assign a new value to a key, it replaces the old value.
  
- **Dictionary Methods**: Familiarize yourself with useful dictionary methods such as `.keys()` (accesses all dict keys), `.values()`(accesses all dict values), and `.items()`(accesses all dict key pair indexes) for accessing keys, values, and keyvalue pairs, respectively.

- When you store a list (or any mutable object) in a dictionary, what you’re actually storing is a reference (or pointer) to that list, not a copy of it.



dataframes (bonus)







---
# Logic
*source of truth=


#### **While Loops**
A `while` loop continues executing as long as a condition is true. Here’s an example:

```python
while True:
    x = input('Enter something: ')
    print(x)
    
# This will continuously prompt for input and display it since the condition (True) never changes.
# To exit, you can use conditional logic, such as an if statement to break the loop based on a condition.
```

#### **For Loops**
A `for` loop iterates over a sequence (like a list) and operates on each element:

```python
list1 = [1, 2, 3]

for x in list1:
    print(x + 1)

# This increments each value and prints the result. Output: 2, 3, 4
```

##### **For Loop with Range**
The `range` function is commonly used in for loops to iterate a specified number of times, following zero-based indexing:

```python
for x in range(5):
    print(x)

# Outputs numbers from 0 to 4. Output: 0, 1, 2, 3, 4

# You can set a start and end value:
for x in range(7, 11):
    print(x)

# Outputs 7 to 10. Output: 7, 8, 9, 10 (note that the upper bound is exclusive)

# You can also specify a step value:
for x in range(0, 11, 2):
    print(x)

# Outputs every second number from 0 to 10. Output: 0, 2, 4, 6, 8, 10

# Often used with lists:
for x in range(len(test_list)):
```

##### **Enumerate Function**
You can use the `enumerate()` function to iterate through a list while keeping track of the index. This is useful for accessing both the index and the value:

```python
list1 = [10, 20, 30, 6]

print(list(enumerate(list1)))
# Prints: [(0, 10), (1, 20), (2, 30), (3, 6)]

for num in enumerate(list1): 
    print(num)

# Will print each index-value pair: (0, 10), (1, 20), etc.

for x, y in enumerate(list1): 
    print(x)  # Prints the index
    print(y)  # Prints the value

# This is particularly useful for creating dictionaries that map indices to values.
```

#### **If and If/Else Statements**
`if` statements allow you to execute code based on whether a condition is met:

```python
while True:
    x = input(": ")
    if x == '5':
        print(" 5!")
        break
    else:
        print('Not 5!')

# This continues prompting until '5' is entered. When '5' is detected, it prints a message and exits the loop.
```

You can also check if variables have values:

```python
x = ""
y = "somevalue"

if x:
    print('true')
else:
    print('false')

if y:
    print('true')
else:
    print('false')

# Empty variables are considered false, so x will print 'false' and y will print 'true'.
# This can be useful for checking return values from functions:
if "returned value":
    print("The function returned a value")
else:
    print("No value returned")
```

#### **While Else**
You can also use an `else` clause with a `while` loop, which executes after the loop finishes normally (not through a break):

```python
while condition:
    # do something
else:
    # executes when the loop condition is false
```

#### **Try/Except**
The `try` and `except` blocks handle exceptions, allowing your program to manage errors gracefully:

```python
try:
    # code that might raise an exception
except SomeException:
    # code that runs if the exception occurs
```

This structure prevents your program from crashing and allows for proper error handling.













# File modification
*source of truth=

Sure! Here’s a structured version of the section on file modification, completing the thoughts and providing examples:

### File Modification

When working with files in Python, you often need to read from and write to them. The concept of a "source of truth" typically refers to the primary source of data that you modify or update. Here’s how to handle file modification in Python:

#### Opening a File
To modify a file, you first need to open it using the `open()` function. You can specify the mode in which you want to open the file:
- `'r'`: Read (default mode).
- `'w'`: Write (overwrites the file).
- `'a'`: Append (adds to the end of the file).
- `'r+'`: Read and write (without truncating).

```python
# Example of opening a file in write mode
with open('example.txt', 'w') as file:
    file.write("This is a new line.\n")
```

#### Reading from a File
You can read the contents of a file using different methods, depending on your needs:

```python
# Reading the entire file
with open('example.txt', 'r') as file:
    content = file.read()
    print(content)

# Reading line by line
with open('example.txt', 'r') as file:
    for line in file:
        print(line.strip())
```

#### Modifying a File
To modify the contents of a file, you typically read its existing content, make your changes, and then write the modified content back to the file. 

Here’s a basic example:

```python
# Reading and modifying a file
with open('example.txt', 'r') as file:
    lines = file.readlines()

# Modify the content (for example, adding a line)
lines.append("This is an added line.\n")

# Write the modified content back to the file
with open('example.txt', 'w') as file:
    file.writelines(lines)
```




## Reading from CSVs
you can read in CSVs by using the *csv* module `import csv`

we read in with the same method as a normal file read, but use the csv.DictReader function on the file

```
with open('somefile.csv', 'r') as file:
	data = csv.DictReader(file)
```

the dict reader create an object with all lines in the CSV stored as an array with a key value structure of title:value
e.g.
```csv
NAME,LEG_LENGTH,DIET
Hadrosaurus,1.4,herbivore
Struthiomimus,0.72,omnivore
Velociraptor,1.8,carnivore
```
becomes:
{'NAME': 'Hadrosaurus', 'LEG_LENGTH': '1.4', 'DIET': 'herbivore'}
{'NAME': 'Struthiomimus', 'LEG_LENGTH': '0.72', 'DIET': 'omnivore'}
{'NAME': 'Velociraptor', 'LEG_LENGTH': '1.8', 'DIET': 'carnivore'}

so we can then initialize a new array and store whatever particular values we want, e.g. if we wanted only name and leg_length we can iterate through the object and assign each key in our new array by calling the keys in the object row e.g. `newdict[x['NAME] = float(x['LEG_LENGTH])`
so the total code becomes:

```
data1={}
with open('data1.csv', 'r') as file:
	readobject = csv.DictReader(file)
	 for x in readobject:
		 data1[x['NAME']]=float(x['LEG_LENGTH])
print(data1)

#outputs---------
{'Hadrosaurus': 1.4, 'Struthiomimus': 0.72, 'Velociraptor': 1.8, 'Triceratops': 0.47, 'Euoplocephalus': 2.6, 'Stegosaurus': 1.5, 'Tyrannosaurus Rex': 6.5}
```



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








---
# DSA

### 1. List In-Place Modification (Remove Duplicates)

This algorithm removes duplicates from a list while maintaining the original order.

```python
def remove_duplicates(nums):
    seen = set()  # Set to keep track of seen numbers
    result = []
    for num in nums:
        if num not in seen:
            seen.add(num)  # Add to set
            result.append(num)  # Append to result if not seen
    return result

# Example usage
nums = [1, 2, 2, 3, 4, 4, 5]
print(remove_duplicates(nums))  # Output: [1, 2, 3, 4, 5]
```

### 2. Nested for loops (example-two sum)

we can use nested for loops to solve the two sum problem by iterating from index 0-n in a list and comparing it to index 1-n
ex) 1,2,3,11,5 target sum = 13
we iterate from 1+2,3,11,5 and then from 2+,3,11,5...
we never need to back compare because the iteration before already performed that
```python
        q=0
        v=1
        z = [10,20,60,66,500,3,14]
        target=69
        
        for x in range(q,len(z)-1):
            print('x', x)
            for y in range(v, len(z)-1):
                print('y', y)
                tmpsum = z[x] + z[y]
                if tmpsum == target: print(z[x] , z[y]); return
            q+=1; v+=1
```

### 2. Two pointer

two pointer is used to increment from 0 to len(object) and perform operations on sets of values, we can do this by having one pointer at the start of the object and a pointer at the end (len(object)-1)

 1  2  3  4  5  6  7  8
p1^                  p2^

we then proceed to iterate inward p1+1 & p2-1 while p1 < p2, this will allow us to perform operations over the whole list


## Breadth first search:
