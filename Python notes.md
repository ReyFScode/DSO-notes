
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
`list2 = list1(filter(None, list1))`


### removing certain identical elements
you can remove certain identical elements from a list by converting to a set or by converting to dict and then back to list.  [python - Removing duplicates in lists - Stack Overflow](https://stackoverflow.com/questions/7961363/removing-duplicates-in-lists)
``` python
some_list = [1,2,3,4,4]

newlist = list(dict.fromkeys(some_list)) #this uses a dictionary conversion to remove duplicates but maintain the list order, order is guaranteed to be maintained


newlist = list(set(some_list)) # converts to a set and then back to list, a set is an unordered collection of unique values, order is not guaranteed to by maintained
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





# Variables

Classic variables = 

Lists [Python Lists - GeeksforGeeks](https://www.geeksforgeeks.org/python-lists/)

Dictionaries

dataframes (bonus)

# Logic
*source of truth=

# splitting/appending
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


