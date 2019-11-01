---
title: "Finding Files"
teaching: 20
exercises: 10
questions:
- "How can you use Python to find different types of files?"
objectives:
- “Import modules to add functions to a Python script”
- “Use os.walk and glob to generate lists of files”
keypoints:
- “The import statement allows you to expand Python's functionality by taking advantage of the additional features offered by modules (code libraries designed to accomplish specific tasks)”
-”There are different kinds of modules in Python: standard distribution modules, that are built into Python itself; third-party modules, which you load, often at the start of your script; and home-made modules, which you make for yourself to serve specific purposes”
- “Python offers a few different ways to traverse directory structures to locate or list file system objects; two common approaches are the os module and the glob module”
“Within a file system, the path (or file path), is just that: a series of text-based breadcrumbs, separated by forward or backward slashes (depending on your OS), that can help you determine the location of a particular file or directory”
- "Collecting filepaths is often the first step of any AV script"
---

## Navigating filenames, file paths, and file systems

Gaining a basic understanding of file systems is the key first step to learn how to find and work with files. At their core, file systems provide a consistent, structured method for arranging data on a hard drive or storage device. If you've ever formatted a hard drive using Disk Utility (macOS) or Disk Management (Windows), you've applied some form of file system to your device. It's through this formatting process that you create a means for your operating system to interpret, interact with, and store file system objects (files, directories, etc.).

File systems have different rules, regulations, and ways of behaving; in your work as AV archivists, you'll most likely encounter a few heavy hitters:

* FAT32 (old Windows)
* NTFS (new Windows)
* exFAT (made by Windows but cross-platform)
* HFS+ (old macOS, aka Mac OS Extended)
* APFS (new macOS as of 2019)

If you have used a program like Windows Explorer or macOS Finder, you probably have an intuitive sense of how to navigate within a file system by using a mouse to click or double-click on folders or file icons. When we use scripts, we use the same kind of logic to express the location of a particular object within the larger hierarchical system structure, the file path.

File paths are a series of text-based breadcrumbs, that describe the location of a particular file or directory. There are two types of file paths: 
# absolute, or full file paths, which include what's called the root directory, or the top-most level of your file system
# relative file paths, which provide a file's location in relation to your current working directory.

Alice uses macOS. For her, the root directory is the slash character `/`. Every absolute file path will start with `/`.

The absolute file path to a cool file on her desktop would be: `/Users/alice/Desktop/CoolStuff/qckitty.gif` That path will always lead to that file no matter where you're working from.

> ## Root Directories on Windows
>
> On Windows, there isn't a root directory for the entire machine.
> File systems are rooted on the storage volume that they use.
> For example, `A:\` has traditionally referred to the root directory of a floppy drive.
> And `C:\` generally refers to the root directory that contains user files, programs, and windows itself.
{: .callout}

Some portions of that path like `Desktop` may be familiar as folders you might see in a GUI.
A file path encodes the same heirarchy of folders that we click through, expressing it as folder names divided by slashes.
We know `CoolStuff` is a subdirectory stored inside of `Desktop`.
We can also see that `Desktop` is a subdirectory of `alice`, which is a subdirectory of `Users` which is a subdirectory of root.

You may not see folders like `Users` on a regular basis.
As computer users, we spend more time working from a folder like `Desktop` and thinking about the locations of files relative to that folder.
The relative file path to `qckitty.gif` from Alice's `Desktop` is `CoolStuff/qckitty.gif`.
That path only works relative from the `Desktop` folder.
If we try to access `CoolStuff/qckitty.gif` from the root folder, our computer will report an error that it can find anything at that path.

This distinction will become particularly important as we begin using Python to find and do things to different kinds of files.
Our first step will always be gathering up file paths based upon a chosen quality such as filename extension.

> ## Slashes
>
> On Windows, the backward slash `\` is used to separate parts of a path.
> This can be frustrating when moving back and forth between operating systems.
> We will be using Python to remove some of these frustrations and make scripts that are more universal.
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

`os.listdir()` prints the names of files and folders in the current directory as a list.

We can also try listing folders in the root directory.

~~~
os.listdir('/')
~~~
{: .language-python}

~~~
['home',
 'usr',
 'Applications',
...
~~~
{: .output}

> ## Python Syntax: Strings and Lists
>
> In order to parse our input, Python has rules about how it should be entered.
> We will cover these as they come up.
> ### Strings
> Characters that you want Python to interpret as single piece of text need to be quoted.
> This is called a string.
> Single-quotes `'` and double-quotes `"` are both valid as long as you use the same symbol at the beginning and end of the string.
> ### Lists
> An unordered series of data, a list, starts with a square bracket `[` , separates list items with commas `,`, and ends with a square bracket `]`. 
{: .callout}

## Importing modules
To start listing files, we had to run two lines of code. The first was to import a module.

Modules, or code libraries designed to expand Python's functionality, can be, at times, a little tricky. There are different kinds of modules in Python: 
* standard distribution modules, which are built into Python itself;
* third-party modules, which you load, often at the start of your script;
* home-made modules, which you create to meet your own specific needs.

To figure out which modules you already have installed, you can run one of two commands:

~~~
help('modules')
~~~
{: .language-python}

The base python environment has very few functions built-in.
In order to power yourself up you need to import modules with the functions you need.
You only need to load a module once per script, so to make sure we load every module that we need, we typically put all import statements at the very top of our script.

While we'll be using the `os` module for this one purpose, it's worth noting that the `os` module has a lot more power and a lot more to offer.
The `os` module is like a gateway that connects Python to your underlying operating system (Mac, Windows, or Linux) and, as such, it allows you to do many operating system tasks (starting/killing processes; adding/removing files, etc.) from inside your Python program. 

Let's import another module that we'll be using in this lesson: `glob`.

~~~
import glob
~~~
{: .language-python}

The `glob` module is more limited in scope.
It's specifically designed for path, directory, and filename matching, and it allows for targeting in a number of different ways: exact string searches, wildcard matching, or alphabetical and numerical ranges.

## Listing all the files in a directory

If you're asking yourself: are we ever gonna get to finding some damn AV files? We're here! Our time is now! 

With `os.listdir()` we could list all the files in a directory of our choosing, but if we have a really nested set of folders, like Alice does, we would have to run `os.listdir` over and over again.
Instead, we will use another function in the `os` module look through all of the directories, `os.walk()`

> ## Finding files with Bash
> In a Unix environment, you might run something like this.
> 
> ~~~
> find Desktop/amia19 -type f 
> ~~~
> {: .language-bash} 
>
> Again, it can be extremely useful to know how to accomplish the same goal in multiple ways.
{: callout}

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

From the help text, we can see that os.walk works through a directory tree structure and for each folder that it finds, it generates:
* the relative path to the folder (dirpath)
* a list of subfolders in the folder (dirnames)
* a list of files in the folder (filenames)

To work with all of these lists, we will need to use for loops, a computer programming method for repeating sections of code.
During a for loop, Python will take items from a list one-at-a-time and perform the same actions on each item.

> ## Python Syntax: For Loops
> In the case of Python, there are a few important things to keep in mind about for loops.
> The first line of a for loop always looks like this:
`for listitem(s) in somelist:`
> * for - tells Python it will have to repeat code on multiple items from a list
> * somelist - the list of items to be worked through
> * listitem(s) - the generic name(s) to refer to items in the code section
> The body of the for-loop can include multiple lines of code, even additional for-loops.
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

Comparing the output, for each folder name from the first command, there is a corresponding list of files in the second command.
 
### A trip down the os.path, with a sidetrack to strings

If we want to activate Python in this “do things” sense, we need provide it with a clear sense of where exactly our files are located within our system.
To achieve this, we will need to combines the folder names and file names that we generated above.
For that we'll take a stroll down the `os.path`, the piece of the os module that allows for pathname manipulation.

Functions in `os.path` try to understand any string as a filepath. 

> ## Forms of `os.path`
>`os.path` objects can exist in one of two forms:
> * as strings, a.k.a. string literals, immutable (unchangeable) sequences of characters that are bounded by quotation marks (single, double, or triple),
> * as bytes (binary digits)
> The byte-form is less common, and we will only use the string-form in this workshop.
{: .callout}

We can use our QCKitty absolute path to walk through a few of the ways that `os.path` can process a string into information about out filesystem:

~~~
kittypath = '/Users/benjaminturkus/Desktop/CoolStuff/qckitty.gif'
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
('/Users/benjaminturkus/Desktop/CoolStuff/qckitty', '.gif')
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

We can generate the absolute path, if we only have a relative path.
~~~
relpath = os.path.relpath(kittypath)
os.path.abspath(relpath)
~~~
{: .language-python}

~~~
/Users/benjaminturkus/Desktop/CoolStuff/qckitty.gif
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

We can find the folder that hold the last piece of the filepath.
~~~
os.path.dirname(kittypath)
~~~
{: .language-python}

~~~
/Users/benjaminturkus/Desktop/CoolStuff
~~~
{: .output}

We can do both of the last two actions at once. 
~~~
os.path.split(kittypath)
~~~
{: .language-python}

~~~
('/Users/benjaminturkus/Desktop/CoolStuff', 'qckitty.gif')
~~~
{: .output}

We can create a path based on components, and let `os.path` handle the syntax.
~~~
dirpath, filename = os.path.split(kittypath)
os.path.join(dirpath, 'nonexistant_file.gif')
~~~
{: .language-python}

~~~
('/Users/benjaminturkus/Desktop/CoolStuff/qckitty.gif')
~~~
{: .output}

~~~
badpath = os.path.join(dirpath, 'nonexistant_file.gif')
os.path.isfile(badpath)
~~~
{: .language-python}

~~~
False
~~~
{: .output}

Returning to os.walk, we want to print a list of file paths.
Our second command printed lists of files, but `os.path.join` can only join strings together.
We will need another for-loop.

~~~
for root, subdirs, files in os.walk('.'):
    for file in files:
        print(file)
~~~
{: .language-python}

Next we need join the folder path and file name.
~~~
for root, subdirs, files in os.walk('.'):
    for file in files:
        item_path = os.path.join(root, file)
        print(item_path)
~~~
{: .language-python}

Again, the reason for taking this extra step—joining together path elements to create absolute paths for each file being surveyed—is to position Python to “do things” (gather metadata, group according to specs, transcode, etc.).
Python must know where objects are living within a file system in order to find and execute processes on them. 

#### Lists in Python

Now that we've figured out how to print full file paths for all of the files located within our chosen directory, we'll move on to the next piece of using Python for AV file management: gathering those paths into a list, one of Python's main collection data types. 

Each of Python's four collection data types—lists, tuples, sets, and dictionaries—have different rules governing their behavior, and different ways of operating. Knowing these rules, and knowing when to choose one type over the other, is an important step in advancing one's Python knowledge. But as this workshop is geared toward showing, not telling, we'll continue to be selective in our review of the basics.

We've already seen lists created by other functions, but we haven't talked about what makes them different or useful.
In Python, lists are indicated by comma-separated items that are contained within square brackets:

~~~
kittylist = ['emu', 'cat', 'dog']
~~~
{: .language-python}

Lists are ordered, changeable, and allow for the presence of duplicate members (as opposed to say, sets, which are unordered and do not allow for the duplication of members).

We can print the list:

~~~
print(kittylist) = ['emu', 'cat', 'dog']
~~~
{: .language-python}

We can print the length of a list:

~~~
print(len(kittylist))
~~~
{: .language-python}

~~~
3
~~~
{: .output}

We can print specific items within a list by “subscripting” (note that Python uses “zero-based indexing,” a.k.a. the first element of a list will start with 0):

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
print(kittylist[-1])
~~~
{: .language-python}

~~~
dog
~~~
{: .output}

We can sort the list (when strings, this means alphabetizing; when integers/floats, this means imposing numerical order):

~~~
print(sorted(kittylist))
~~~
{: .language-python}

~~~
['cat', 'dog', 'emu']
~~~
{: .output}

We can add items to a list (by using the append method):
~~~
kittylist.append('frog')
print(kittylist)
~~~
{: .language-python}

~~~
['emu', 'cat', 'dog', 'frog']
~~~
{: .output}

And, as we've done a few time now and will continue to do, we can use for-loops to perform repetitive actions on each item contained within a list:

~~~
for animal in kittylist:
    print(animal)
~~~
{: .language-python}

~~~
emu
cat
dog
frog
~~~
{: .output}

## Listing all the MOV files in a directory

If we combine these techniques, and add just a few more tricks, we can get to a place where we're comfortable traversing a directory structure to gather up the full file paths of a particular type of file (in our case, all files that have a .mov extension).

First, we'll initiate a new list for our file paths:  

~~~
mov_list = [ ]
~~~
{: .language-python}

Then, to add all of file paths that we'll need down the road to our list, we'll use os.walk, a for loop, an if statement, the endswith string method, and the os.path join method:

~~~
for root, dirs, files in os.walk(mydir):
    for file in files:
        if file.endswith('.mov'):
            item_path = os.path.join(root, file)
            mov_list.append(item_path)
~~~
{: .language-python}

To confirm that this has gone down in the way that we imagined, we can use the print statement to ensure that our list contains Quicktime (and only Quicktime) file paths:

~~~
print(mov_list)
~~~
{: .language-python}

### glob
`os.walk()` is not the only way to make a file list.
The `glob` inludes a functions to do a lot of the for-looping and path glueing that we just did for us.

The `glob.glob` function understands filepaths like `os.walk` except it interprets special characters as wildcards.

* `*` is a wildcard for part or all of a file name or folder
* `**` is a wildcard for nested folders

~~~
mov_list = glob.glob(os.path.join(mydir, "**", "*mov"), recursive=True)
~~~
{: .language-python}

Here, we're asking glob to behave in a fashion similar to os.walk, and recursively dig through directories to identify any Quicktime file located within. In fact, underneath the hood the glob module is simply a joining of forces of two other Python modules: os.scandir (a cousin of os.walk) and fnmatch.fnmatch (a module devoted to pattern matching).

> ## List Comprehensions
> In this workshop we use the following pattern a lot.
> ~~~
> results = []
> for item in list_of_items:
>   result = do_something(item)
>   results.append(result)
> ~~~
> {: .language-python}
>
> Python has a more succint way to write small loops like this, the list comprehension.
> ~~~
> results = [do_something(item) for item in list_of_items]
> ~~~
> {: .language-python}
> (see! Python isn't always super verbose!)
{: .callout}

If you're asking, “When should I use `os.walk` and when should I use `glob.glob`?" you're asking a good question.


should I use os.walk or glob for these purposes?” there are a few reasons to privilege glob:

* glob returns full file paths, sparing us the tediousness of os.path joins; and
* glob can be used for more exact file path matching, which could be useful depending on your context

Say, for example, we wanted to gather up for processing only those files that match our institution's unique identification file naming system, which uses a three-digit pattern. We could do this with glob in a quick and easy way:

mov_list = [file for file in glob.glob(mydir + "**/*[0-9][0-9][0-9]*mov", recursive=True)]
