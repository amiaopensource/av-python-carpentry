---
title: "Finding Files"
teaching: 20
exercises: 10
questions:
- "How can I use Python to find different types of files?"

objectives:
- "Import modules to add functions to a Python script"
- "Use `os.walk` and `glob.glob` to generate lists of files"

keypoints:
- "Collecting filepaths is often the first step of any AV script"
- "The import statement allows you to expand Python's functionality by loading modules with specialized functions"
- "Standard distribution modules are installed with Python. Other modules must be installed"
- "Two common modules for listing files with Python are `os` and `glob`"
- "The filepath is the sequence of parent folders that define the location of a particular file or directory"
---

## The First Step: Finding Files

Before Alice can do anything with any file in Python, she first needs to be able to refer Python to the location that file.

## Navigating filenames, file paths, and file systems

Gaining a basic understanding of file systems is the first step in learning how to find and work with files. File systems provide a structured method for arranging data on a storage device. If you've ever formatted a hard drive using Disk Utility (macOS) or Disk Management (Windows), you've applied some form of file system to your device. That file system is what makes it possible to create files and folders on your storage device.

File systems have different rules, regulations, and ways of behaving; in your work as AV archivists, you'll most likely encounter a few common acronyms:

* FAT32 (old Windows)
* NTFS (new Windows)
* exFAT (made by Windows but cross-platform)
* HFS+ (old macOS, aka Mac OS Extended)
* APFS (new macOS as of 2019)

If you've used a program like Windows Explorer or macOS Finder, you probably have an intuitive sense of how to navigate within a file system by using a mouse to click or double-click on folders or file icons. When we use scripts, we use a similar logic to express the location of a particular object within the larger hierarchical system structure -- the file path.

File paths are a series of text-based breadcrumbs that describe the location of a particular file or directory. There are two types of file paths: 
* absolute, or full file paths, which include what's called the root directory, or the top-most level of your file system
* relative file paths, which provide a file's location in relation to your current working directory.

Alice uses macOS. For her, the root directory is the slash character `/`. Every absolute file path will start with `/`.

The absolute file path to a cool file on her desktop would be: `/Users/alice/Desktop/CoolStuff/qckitty.gif` That path will always lead to that file, no matter where she's working from (her current working directory).

> ## Root Directories on Windows vs Mac/Linux
>
> On Windows, there isn't a root directory for the entire machine.
> File systems are rooted on the storage volume that they use.
> For example, `A:\` has traditionally referred to the root directory of a floppy drive.
> And `C:\` generally refers to the root directory that contains user files, programs, and windows itself.
>
> On Mac/Linux, the operating system defines a single file system for all devices.
> The root folder is '/'.
> If you plug in an external drive, it is probably mounted as a folder in '/Volumes/'.
{: .callout}

Some portions of that path, for example, `Desktop`, might be as familiar to you as the folders you see in a GUI.
A file path encodes the same hierarchy of folders that we click through, expressing it as series of folder names divided by slashes.
We know that `CoolStuff` is a subdirectory stored inside of `Desktop`.
We can also see that `Desktop` is a subdirectory of `alice`, which is a subdirectory of `Users` which is a subdirectory of root.

You may not see folders like `Users` on a regular basis.
As computer users, we spend more time working from a folder like `Desktop` and thinking about the locations of files relative to that folder.
The relative file path to `qckitty.gif` from Alice's `Desktop` is `CoolStuff/qckitty.gif`.
That path only works in relation to the `Desktop` folder.
If we try to access `CoolStuff/qckitty.gif` from the root folder, our computer will report an error, explaining that it cannot find anything at that path.

This distinction will become particularly important as we begin using Python to find and do things to different kinds of files.
Our first step will always be gathering up file paths based upon a chosen quality such as filename extension.

> ## Slashes
>
> On Windows, the backward slash `\` is used to separate parts of a path.
> This can be frustrating when moving back and forth between operating systems.
> We will be using Python to remove some of these frustrations and write code that is more portable.
> 
> If you find code in this workshop that doesn't work because it uses the wrong kind of slashes for your operating system, please let us know.
{: .callout}


## Listing Files in a Folder

Now let's learn how to see the contents of our filesystem with Python.

~~~
import os
os.listdir()
~~~
{: .language-python}

~~~
['Library',
 'Applications',
 'Downloads',
 'Desktop',
 'Documents',
...
~~~
{: .output}

(Your results may be slightly different depending on your operating system and how you have customized your filesystem.)

In this code
* `os` is a module that connects Python to your underlying operating system (Mac, Windows, Linux)
* `listdir()` is a function that is part of that module

We'll explain more about modules in a bit.
For now, we will work with just this function.
Then, we'll explore other paths of the `os` module.

`os.listdir()` prints the names of files and folders in the current directory as a list.
By default, `os.listdir()` prints contents of the current working directory.
In our case that's the home folder.

It can also print the contents of another folder.
We will use the relative path to the Desktop.

~~~
os.listdir('Desktop')
~~~
{: .language-python}

If the path points to folder that doesn't exist, `os.listdir()` will give an error.
~~~
os.listdir('Desk')
~~~
{: .language-python}

We're curious about the contents of the `amia19` folder, so let's examine that.
Because we will use this path repeatedly, first we will store it with a variable.

On macOS:
~~~
video_dir = '/Users/username/Desktop/amia19'
~~~
{: .language-python}

On Windows:
~~~
video_dir = 'C:\\Users\\username\\Desktop\\amia19'
~~~
{: .language-python}

~~~
os.listdir(video_dir)
~~~
{: .language-python}

~~~
['120th anniversary',
 'federal_grant',
 'on_demand'
]
~~~
{: .output}


> ## Python Syntax: Strings
> 
> ### Strings
> Characters that you want Python to interpret as single piece of text need to be quoted.
> This is called a string.
> Single-quotes `'` and double-quotes `"` are both valid as long as you use the same symbol at the beginning and end of the string.
{: .callout}

## Listing all the files in a directory

If you're asking yourself: are we ever gonna get to finding some damn AV files?
We're here!
Our time is now!

> ## Finding files with Bash
> For reference, in a Unix environment, you might run something like this.
> 
> ~~~
> $ find Desktop/amia19 -type f 
> ~~~
> {: .language-bash} 
>
> Again, it can be extremely useful to know how to accomplish the same goal in multiple ways.
{: .callout}

With `os.listdir()` we could list all the files in a directory of our choosing, but if we have a really nested set of folders like Alice does, we would have to run `os.listdir` over and over again.
Instead, we will use another function in the `os` module to look through all of the directories, `os.walk()`

~~~
os.walk('Desktop/amia19')
~~~
{: .language-python}

~~~
<generator object walk at 0x1d2da49150>
~~~
{: .output}

That's not very useful, so we'll check the help for this function.

~~~
help(os.walk)
~~~
{: .language-python}

From the help text, we can see that os.walk works through a directory tree structure and, for each folder that it finds, it generates:
* the relative path to the folder (dirpath)
* a list of subfolders in the folder (dirnames)
* a list of files in the folder (filenames)

> ## Looking for help
>
> What are other ways that you could look for help?
> 
> > ## Solution
> > Search engines can be very useful, especially results from [Python Documentation](https://docs.python.org/) and [Stack Overflow](http://stackoverflow.com) which often have sample code.
> > Co-workers and friends can also be really helpful.
> > Often, helping someone with a coding problem ends up with both people learning more.
> {: .solution}
{: .challenge}

### `for` loops

To work with all of these lists, we will need to use `for` loops, a computer programming method for repeating sections of code.
During a `for` loop, Python will take items from a list one-at-a-time and perform the same actions on each item.

__`for` loops will be the most important tool that we'll use in this workshop.__

Dealing with 100's, 1000's, or even more files means a lot of batch processing.
The `for` loop is the most common way that we'll be doing that batch processing.


> ## Python Syntax: `for` Loops
> In the case of Python, there are a few important things to keep in mind about for loops.
> The first line of a for loop always looks similar to this:
`for listitem in somelist:`
> * for - tells Python it will have to repeat code on multiple items from a list
> * somelist - the list of items to be worked through
> * listitem(s - the generic name(s) to refer to items in the code section
> The body of the `for` loop is always indented from the first line and can include multiple lines of code, even additional `for` loops.
{: .callout}

~~~
for root, subdirs, files in os.walk('.'):
    print(root)
~~~
{: .language-python}

This command looks through all of the sets of folders and contents found by `os.walk`, and for each set, it prints the folder name. 
We can do the same thing, except printing the filenames.

~~~
for root, subdirs, files in os.walk('.'):
    print(files)
~~~
{: .language-python}

Comparing the outputs, for each folder name from the first command there is a corresponding list of files in the second command.

> ## Printing, `for` loops, and Jupyter
>
> What happens when you don't include the print function?
> Why do you think this is the case?
>
> ~~~
> for root, subdirs, files in os.walk('.'):
>   files
> ~~~
> {: .language-python}
> 
> > ## Solution
> > Jupyter only displays the final item in the list, because it only displays the results of the final line of code.
> > Because we're learning about how Python works, during the workshop we will use the `print()` function a lot.
> {: .solution}
{: .challenge}
 
### A trip down the os.path, with a sidetrack to strings

If we want to activate Python in this "do things" sense, we need give it with a clear sense of where exactly our files are located within our system.
To achieve this, we will need to combine the folder names and file names that we generated above.
For that, we'll take a stroll down the `os.path`, the piece of the os module that allows for pathname manipulation.

Functions in `os.path` try to understand any string as a filepath. 

> ## Forms of `os.path`
>`os.path` objects can exist in one of two forms:
> * as strings, a.k.a. string literals, immutable (unchangeable) sequences of characters that are bounded by quotation marks (single, double, or triple),
> * as bytes (binary digits)
> The byte-form is less common.
> We will only use the string-form in this workshop.
{: .callout}

We can use our QCKitty absolute path to walk through a few of the ways that `os.path` can process a string into information about out filesystem:

On macOS:
~~~
kittypath = '/Users/username/Desktop/CoolStuff/qckitty.gif'
~~~
{: .language-python}

On Windows:
~~~
kittypath = 'C:\\username\\Desktop\\CoolStuff\\qckitty.gif'
~~~
{: .language-python}

We can find out if the file path is for a file or folder.
~~~
os.path.isfile(kittypath)
~~~
{: .language-python}

~~~
True
~~~
{: .output}

~~~
os.path.isdir(kittypath)
~~~
{: .language-python}

~~~
False
~~~
{: .output}

We can extract a file's extension if it exists
~~~
os.path.splitext(kittypath)
~~~
{: .language-python}

~~~
('/Users/username/Desktop/CoolStuff/qckitty', '.gif')
~~~
{: .output}

We can extract the relative path based on our current directory.
~~~
os.path.relpath(kittypath)
~~~
{: .language-python}

~~~
CoolStuff/qckitty.gif
~~~
{: .output}

We can generate the absolute path, if we only have a relative path.
~~~
relpath = os.path.relpath(kittypath)
os.path.abspath(relpath)
~~~
{: .language-python}

~~~
/Users/username/Desktop/CoolStuff/qckitty.gif
~~~
{: .output}

We can find the last piece of filepath.
~~~
os.path.basename(kittypath)
~~~
{: .language-python}

~~~
qckitty.gif
~~~
{: .output}

We can find the folder that holds the last piece of the filepath.
~~~
os.path.dirname(kittypath)
~~~
{: .language-python}

~~~
/Users/username/Desktop/CoolStuff
~~~
{: .output}

We can perform both of the last two actions at once. 
~~~
os.path.split(kittypath)
~~~
{: .language-python}

~~~
('/Users/username/Desktop/CoolStuff', 'qckitty.gif')
~~~
{: .output}

We can create a path based on components, and let `os.path` handle the syntax.
~~~
dirpath, filename = os.path.split(kittypath)
os.path.join(dirpath, 'nonexistent_file.gif')
~~~
{: .language-python}

~~~
('/Users/username/Desktop/CoolStuff/nonexistent_file.gif')
~~~
{: .output}

~~~
badpath = os.path.join(dirpath, 'nonexistent_file.gif')
os.path.isfile(badpath)
~~~
{: .language-python}

~~~
False
~~~
{: .output}

Returning to os.walk, we want to print a list of file paths.

~~~
for root, subdirs, files in os.walk('.'):
    os.path.join(root, files)
~~~
{: .language-python}

~~~
Error
~~~
{: .output}

`os.path.join` can only join strings together.
To access each string in the lists of files, we will need another `for` loop.

~~~
for root, subdirs, files in os.walk('.'):
    for file in files:
        item_path = os.path.join(root, file)
        print(item_path)
~~~
{: .language-python}

> ## Python Syntax: Function arguments `( )` 
>
> Data that will be used by a function is placed within parentheses `(` `)` immediately after the function name.
> If a function uses more than argument, each argument is separated by a comma.
> Arguments can be anything, including strings, lists, variables that hold strings, and other functions.
> However, functions can have rules for what they will accept.
> For example, using strings as arguments in `os.path.join` returns an error.
> ~~~
> os.path.join('Desktop', 'amia19')
> ~~~
> {: .language-python}
>
> But using the same strings as elements in a list, returns the result we want.
>
> ~~~
> os.path.join(['Desktop', 'amia19'])
> ~~~
> {: .language-python}
{: .callout}

We've successfully listed the absolute path for every file in our directory.
The reason for taking this extra step—joining together path elements to create absolute paths for each file being surveyed—is to position Python to "do things" (gather metadata, group according to specs, transcode, etc.).

#### Lists in Python

Now that we've figured out how to print full file paths for all of the files located within our chosen directory, we'll move on to the next piece of using Python for AV file management: gathering those paths into a list, one of Python's main collection data types. 

Each of Python's four collection data types—lists, tuples, sets, and dictionaries—have different rules governing their behavior and different ways of operating.
Knowing these rules, and knowing when to choose one type over the other, is an important step in advancing one's Python knowledge.
But as this workshop is geared toward showing, not telling, we'll continue to be selective in our review of the basics.

We've already seen lists created by other functions, but we haven't talked about what makes them different or useful.


> ## Python Syntax: Lists
> In Python, lists start and end with square brackets `[` `]`.
> Lists can contain any kind of data, strings, numbers, other lists, etc.
> Each list item is separated by a comma.
>
> This is a list with two items.
> ~~~
> ['emu', 'cat']
> ~~~
> {: .language-python}
>
> This is a list with one item.
>
> ~~~
> ['emu, cat']
> ~~~
> {: .language-python}
> 
{: .callout}

Let's create a list to explore how they work in Python.

~~~
kittylist = ['emu', 'cat', 'dog']
~~~
{: .language-python}

Lists are ordered, changeable, and allow for the presence of duplicate members (as opposed to say, sets, which are unordered and do not allow for the duplication of members).

We can print the list:

~~~
kittylist
~~~
{: .language-python}

~~~
['emu', 'cat', 'dog']
~~~
{: .output}

We can calculate the length of a list:

~~~
len(kittylist)
~~~
{: .language-python}

~~~
3
~~~
{: .output}

We can print specific items within a list by "subscripting" (note that Python uses "zero-based indexing," a.k.a. the first element of a list will start with 0):

~~~
print(kittylist[0])
print(kittylist[1])
print(kittylist[2])
~~~
{: .language-python}

~~~
'emu'
'cat'
'dog'
~~~
{: .output}

We can use a negative index to begin at the end of a list:

~~~
kittylist[-1]
~~~
{: .language-python}

~~~
dog
~~~
{: .output}

We can sort the list (when strings, this means alphabetizing; when integers/floats, this means imposing numerical order):

~~~
sorted(kittylist)
~~~
{: .language-python}

~~~
['cat', 'dog', 'emu']
~~~
{: .output}

We can add items to a list (by using the append method):
~~~
kittylist.append('frog')
kittylist
~~~
{: .language-python}

~~~
['emu', 'cat', 'dog', 'frog']
~~~
{: .output}

And, as we've already done a few times, we can use `for` loops to perform repetitive actions on each item contained within a list:

~~~
for animal in kittylist:
    print(animal + 's')
~~~
{: .language-python}

~~~
'emus'
'cats'
'dogs'
'frogs'
~~~
{: .output}

## Listing all the MOV files in a directory

If we combine these techniques, we can get to a place where we can build a list of all the files we're looking for (in Alice's case, all files that have a .mov extension).

First, we'll create an empty list to hold each file path as we generate it.  

~~~
file_list = [ ]
~~~
{: .language-python}

Then, we'll use `os.walk`, a `for` loop, and `os.path.join` to find all the files and store their paths in our list:

~~~
for root, dirs, files in os.walk(video_dir):
    for file in files:
        item_path = os.path.join(root, file)
        file_list.append(item_path)

file_list[0:20]
~~~
{: .language-python}

`file_list` contains the path for every file in `amia19`.
It would be more useful if we had a list of only the mov files.
For that we will need an `if` statement.

> ## Python Syntax: `if` statements 
>
> `if` statements share a similar structure to `for` loops.
> The first line will look similar to this.
> `if something is true:`
> * `if` tell Python that this will be conditional code
> * `something is true` is a test to see if the conditional code should be run.
> The body of the `if` statement is always indented from the first line.
> It can include multiple lines of code, including more `if` statements.
{: .callout}

In our case, we would like to know if the end of the filename is `.mov`.
If it is, we will add the file to our list.
If it isn't, we will do nothing and go to the next item in our `for` loop.

~~~
mov_list = []

for root, dirs, files in os.walk(video_dir):
    for file in files:
        if file.endswith('.mov'):
            item_path = os.path.join(root, file)
            mov_list.append(item_path)
~~~
{: .language-python}

Let's look at the list to confirm that it contains what we imagined it should.

~~~
mov_list
~~~
{: .language-python}

> ## Finding out how many Quicktime files
>
> How would you calculate the number of Quicktime files you found?
> 
> > ## Solution
> > ~~~
> > len(mov_list)
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

## Importing modules
At the very beginning of this exercise with `os`, Alice had to run two lines of code. The first was to import a module.

Modules, or code libraries designed to expand Python's functionality, can be, at times, a little tricky. There are different kinds of modules in Python: 
* standard distribution modules, which are built into Python itself;
* third-party modules, which you load, often at the start of your script;
* home-made modules, which you create to meet your own specific needs.

There are two ways to figure out which modules you already have installed.

When you're in a Jupyter notebook or Python console:

~~~
help('modules')
~~~
{: .language-python}

When you're using a terminal:

~~~
pip list
~~~
{: .language-bash}

The base python environment has very few functions built-in.
In order to power yourself up, you need to import modules with the functions you need.
You only need to load a module once per script.
To make sure we load every module that we need, we typically put all of our import statements at the very top of a script.

> ## Python Syntax: Modules and `.`
>
> Like directory structures, modules can contain functions and submodules, which themselves can contain functions and submodules.
> The `.` syntax works like the slash in a filepath.
> It clarifies that we want to use a function defined in a specific module.
> If we don't include the parent modules, the Python interpreter will not know where to find the function we want to run.
{: .callout}

> ## Loading another module
>
> `glob` is another useful module for finding files.
> Do you have glob installed?
> How would you load it into your script?
> 
> > ## Solution
> > Yes, glob is one of the standard distribution modules.
> > It can be loaded like this:
> > ~~~
> > import glob
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

### glob

`os.walk()` is not the only way to make a file list.
Earlier, we loaded the glob module.
`glob` includes functions that do a lot of the `for` looping and path gluing that we just did for ourselves.

The `glob.glob` function understands filepaths like `os.walk` except it interprets special characters as wildcards. For example:

* `*` is a wildcard for part or all of a file name or folder
* `**` is a wildcard for nested folders
~~~
mov_list = glob.glob(os.path.join(video_dir, "**", "*mov"), recursive=True)
~~~
{: .language-python}

Here, we're asking glob to behave in a fashion similar to os.walk, and recursively dig through directories to identify any Quicktime file located within.
In fact, underneath the hood the glob module is joining the forces of two other Python modules: os.scandir (a cousin of os.walk) and fnmatch.fnmatch (a module devoted to pattern matching).

We can create even more powerful filtering pattern matching.
Say, for example, that Alice found a filenaming policy that said all filenames should include the item id surrounded by underscores `_`.
We could do this with glob pattern like this:

~~~
goodfn_mov_list = glob.glob(os.path.join(video_dir, "**", "*_[0-9][0-9][0-9][0-9]_*mov", recursive=True)
~~~
{: .language-python}

If you're thinking, "If I have the power of `glob.glob` in one line, why should I bother writing all the looping of `os.walk`?" you're asking a good question.

> ## `os.walk` vs. `glob.glob`
>
> In what types of situations would it be useful to use `for` loops with `os.walk` instead of `glob.glob`
> 
> > ## Solution
> > `glob.glob` does one job, find filepaths, really well.
> > It can't do other jobs, like count the number of subdirectories or find all the files that don't match a particular pattern.
> > If you need to do additional filtering, counting, or other operations while you're are looping through the file system, `os.walk` is probably more useful.
> {: .solution}
{: .challenge}

Expanding upon our previous efforts to gather up Quicktime files, we can amend our earlier os.walk and use what’s called a tuple to add a bunch of different media-specific file extensions to our endswith if statement.

~~~
media_list = [ ]

for root, dirs, files in os.walk(video_dir):
    for file in files:
        if file.endswith(('.mkv', '.mov', '.wav', '.mp4', '.dv', '.iso', '.flac')):
        	item_path = os.path.join(root, file)
            media_list.append(item_path)
~~~
{: .language-python}




