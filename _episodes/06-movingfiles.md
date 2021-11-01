---
title: "Moving and Transcoding Files"
teaching: 10
exercises: 10
questions:
- "How can I use Python to move files from one place to another?"
- "How can I use Python to run ffmpeg?"
objectives:
- "Use shutil to copy and move files"
- "Use subprocess to call ffmpeg"
keypoints:
- "Collecting filepaths is often the first step of any AV script"
---

> ## Catch-up Code
> 
> If you get lost or fall behind or need to burn it all down and start again, here's some quick catch-up code that you can run to get back up to speed.
>
> Remember: press <kbd>Shift</kbd>+<kbd>Return</kbd> to execute the contents of the cell.
>
> ~~~
> video_dir = '/Users/username/Desktop/pyforav'
>
> or
>
> video_dir = 'C:\\Users\\username\\Desktop\\pyforav'
> 
> MAKE SURE TO CHANGE USERNAME TO YOUR USERNAME
> ~~~
> {: .language-python}
>
> Then copy and paste the following:
>
> ~~~
> import os
> import subprocess
> import glob
> mov_list = []
mov_list = glob.glob(os.path.join(video_dir, "**", "*mov"), recursive=True)
>
> ~~~
> {: .language-python}
>
> And run this to confirm that you're generating a file list properly (you should see a list of file names; if not, call for help!)
>
> ~~~
> mov_list
> ~~~
> {: .language-python}
>
>
{: .callout}

Remember Alice? She had 3 tasks that she needed to complete.

1. create service copy MP4s for all of the master files on her hard drive
2. share the duration of each video file with a cataloger
3. preserve the files

Alice will need to create a transcoding script to take care of the first step.
You may have noticed that the "120th Anniversary" project included service files.
So let's start there.

## Moving Files

When we review the directories in which those service files reside, it appears that they're all located in [BagIt](https://en.wikipedia.org/wiki/BagIt) bags.
Because bags use multiple mechanisms to track the fixity of their contents, we don't want to change their contents if we can avoid it.
In this case, we can make copies of those existing service files.

For any kind of local file moving or copying in Python, the `shutil` module is a good starting point.
Let's import that.

~~~
import shutil
~~~
{: .language-python}

We'll be using the `shutil.copy()` function to do this.

~~~
help(shutil.copy)
~~~
{: .language-python}

`shutil.copy()` takes two arguments, `src` (path to source file) and `dest` (path to destination). The source path we can pull from our `mov_list`. But the destination path we'll have to create anew.

Let's make a single subfolder within the `pyforav` directory to hold copies of those service files.

~~~
service_folder = os.path.join(video_dir, 'service')
service_folder
~~~
{: .language-python}

~~~
'/User/benjaminturkus/Desktop/pyforav/service'
~~~
{: .output}

This folder doesn't actually exist yet.
It's just a string. 
If we try to copy anything to this non-existent folder, nothing good will happen.

~~~
shutil.copy(mov_list[0], service_folder)
~~~
{: .language-python}

Because there wasn't a landing directory ready for our video files, we ended up with an extensionless file called "service."
We wanted to copy the file into a folder called "service", except the "service" folder didn't exist.

So let's delete that "service" file and do things the right way. 

We'll use the `os` module to make this folder.
But before doing that, it's good practice to make sure the folder doesn't already exist.
Among python users, this is what's called Looking Before You Leap (LBYP).

~~~
if not os.path.exists(service_folder):
	os.makedirs(service_folder)
~~~
{: .language-python}

And now that the destination directory actually exists, we should be able to copy files there.

~~~
shutil.copy(mov_list[0], service_folder)
~~~
{: .language-python}

So now we have the tool to do a drag-and-drop with Python.
But just using this tool would mean writing a command for every file we want to copy.
That would have the same problems as dragging-and-dropping every file.
To turn this tool into something really useful, we need another building block, the `for` loop.

## `for` loops

Often when we works with lists, we use `for` loops, a computer programming method for repeating sections of code.
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
> * listitem(s - the generic name(s) to refer to items in the code section, you choose these names
> The body of the `for` loop is always indented from the first line and can include multiple lines of code, even additional `for` loops.
{: .callout}

~~~
for filepath in mov_list:
    print(filepath)
~~~
{: .language-python}

This command outputs each item from our `mov_list`.
We can perform more complicated actions within the loop.

~~~
for filepath in mov_list:
    print(filepath + ' exists')
~~~
{: .language-python}

> ## Printing, `for` loops, and Jupyter
>
> What happens when you don't include the print function?
> Why do you think this is the case?
>
> ~~~
> for filepath in mov_list:
>   filepath + ' exists'
> ~~~
> {: .language-python}
> 
> > ## Solution
> > Jupyter only displays the result from the final item in the list, because it only displays the results of the final line of code.
> > Because we're learning about how Python works, during the workshop we will use the `print()` function a lot.
> {: .solution}
{: .challenge}

Now we have all of the tools to automate the copying:
* using `glob.glob()` to create a list of files
* using `shutil.copy` to copy a file
* using a `for` loop to perform the same action repeatedly

Putting those tools together, we can write code like this.

~~~
mp4_list = glob.glob(os.path.join(video_dir, '**', '*mp4'), recursive=True)
for item in mp4_list:
	shutil.copy(item, service_folder)
~~~
{: .language-python}

And we can check that this code did what we intended either by viewing the folder or by using some more Python code.

~~~
os.listdir(service_folder)
~~~
{: .language-python}

That's a big chunk of Alice's task performed with a minimal amount of work.
For the next part, she'll need to do some transcoding.

> ## Building `for` Loops
> The process we went through above is a great practice when writing a `for` loop.
> 1. Write the code to run on a single piece of data.
> 2. Test out the code and make sure it works. If not, keep developing it.
> 3. Add the `for` loop syntax and adjust any variable names.
> 
> That way, if there are any problems in the code you write, you're more likely to catch them before the `for` loops makes the same mistake over-and-over again.
> Imagine if you were copying 1000's of files to multiple locations and had to find and manually delete them because of a typo.
{: .callout}

## Using FFmpeg within Python

Python can be used to run other scripts and programs on a computer.
For example, if there is a command-line utility that performs an essential function, you can incorporate it into a Python script that automates that action for a group of files.
For this lesson, we will use the `subprocess` module to run terminal commands from our script.
The `subprocess` module works for all command-line tools.
Later, we will look at another method that exists for some, but not all, command-line tools.

~~~
import subprocess
~~~
{: .language-python}

We'll use the `subprocess.run()` function.

~~~
help(subprocess.run)
~~~
{: .language-python}

We can try out the example code to see how this works.

~~~
subprocess.run(['ls', '-l'])
~~~
{: .language-python}

> ## What do you expect this code to do?
> Before you run this code, predict what the results will be.
> Did the results match your expectations?
> 
> > ## Solution
> > `subprocess.run()` runs a terminal command and returns whether it was successful or not.
> > `0` means the command finished successfully.
> > `1` means the command had an error and did not finish.
> > 
> > For some commands like `ffmpeg` this is all we're interested.
> > But if we want to see the output of the command, we can do the following.
> > ~~~
> > subprocess.run(['ls', '-l'], capture_output=True)
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge} 

If you've used command line tools, you might recognize `ls -l` as the way to return the contents of a directory as a list.
`subprocess.run()` structures that command differently.
Instead of taking a string like `subprocess.call('ls -l')`, it takes a list where each item in the list is a string.
We'll be taking advantage of that.

First, let's assemble an FFmpeg command to make mp4/h264 files.
And since we're mortals, we'll use the wonderful [ffmprovisr](https://amiaopensource.github.io/ffmprovisr/#transcode_h264)

The suggested command is `ffmpeg -i input_file -c:v libx264 -pix_fmt yuv420p -c:a aac output_file`.
The 'subprocess.run()' function requires the command to be in a list format.
We can do this by treating the spaces in the command as the delimiters for each item in our list.
When we use this function in a for-loop, most of these items will remain the same each time the command runs, but we will change the input and output files.
For those, we will use a variable instead of a string.

~~~
['ffmpeg', '-i', input_file, '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', output_file]
~~~
{: .language-python}

The `input_file` and `ouput_file` are like the source and destination paths we needed for `shutil.copy()`.
We can take `input_file` from the first entry on our `mov_list`.
For `output_file`, we can use the `service_folder`, but `ffmpeg` requires a named output path with a filename.

Since we're transcoding preservation files to make service files, we can use the preservation filename as the basis for our service filename.
And because we're dealing with paths, let's use an `os.path` function. The function `os.path.basename` will always return the last piece of a path, whether that's a directory name or filename.

~~~
os.path.basename(mov_list[0])
~~~
{: .language-python}

~~~
napl1777.mov
~~~
{: .output}

That's a good start.
Our service files use an 'mp4' wrapper instead of an 'mov' wrapper.
Because a filename is a string, we can use the `replace()` method for strings to make this change.

~~~
os.path.basename(mov_list[0]).replace('mov', 'mp4')
~~~
{: .language-python}

~~~
napl1777.mp4
~~~
{: .output}

For a final step, we can join the new filename to the `service_folder` path.

~~~
input_file = mov_list[0]
output_file = os.path.join(service_folder, os.path.basename(mov_list[0]).replace('mov', 'mp4'))
subprocess.run(['ffmpeg', '-i', input_file, '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', output_file])
~~~
{: language-python}

~~~
CompletedProcess(['ffmpeg', '-i', '/User/benjaminturkus/Desktop/pyforav/federal_grant/napl1777.mov', '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', '/User/benjaminturkus/Desktop/pyforav/service/napl1777.mp4'], returncode=0)
~~~
{: .output}

> ## Using Variables 1
> One of the challenging things about learning to program is how and when to use variables.
> You can be verbose and store the result of every change to a variable like this.
> ~~~
> original_filename = os.path.basename(mov_list[0])
> output_filename = original_filename.replace('mov', 'mp4')
> output_filepath = os.path.join(service_folder, output_filename)
> subprocess.run(['ffmpeg', '-i', input_file, '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', output_filepath])
> ~~~
> {: .language-python}
> You can also be very terse and nest all of your functions inside of each other like this.
> ~~~
> subprocess.run(['ffmpeg', '-i', input_file, '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', os.path.join(service_folder, os.path.basename(mov_list[0]).replace('mov', 'mp4'))])
> ~~~
> {: .language-python}
>
> Both of these approaches will work.
> In practice, most code falls somewhere between these extremes, producing something that is concise but readable.
> When you're learning to code, it's better to focus on getting code to run.
> Don't be afraid of using too many variables as long as they help you understand the code you're writing.
> And as you advance you may find resources like the [PEP-8](https://www.python.org/dev/peps/pep-0008/) style guide useful.
{: .callout}

## Looking Before You Transcode

It might be tempting to wrap `ffmpeg` command in a `for` loop and celebrate, but let's think through what might happen when we run this code across all of the files in `mov_list`.

> ## Problems with Transcoding Everything
> What issues might you encounter if you tried to transcode every file in `mov_list` right now?
> What steps could you take to avoid those issues?
> > ## Solution
> > You would transcode files that you already have service files for, like `napl1777.mov`.
> > To avoid this, you could use `if` statements, to skip the transcoding process for those files like the `os.makedirs()` example above.
> {: .solution}
{: .challenge} 


Let's focus on untranscoded movs.
To do that, we'll look before we leap by seeing if a service copy already exists for that mov file.
We'll explore more about how to use `if` statements in Lesson 8.

~~~
for item in mov_list:
	if item.endswith('mov'):
		output_file = os.path.join(service_folder, os.path.basename(item).replace('mov', 'mp4'))
		if not os.path.exists(output_file):
			subprocess.run(['ffmpeg', '-i', item, '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', output_file])

os.listdir(service_folder)
~~~
{: language-python}

Success!

> ## Using Variables 2
> The code above is an example of how a variable can be useful.
> By first storing the service file path to `output_file`, we are able to use it in two different contexts.
> First, to see if the service file already exists.
> Second, to store the newly created service file if it doesn't.
> Without creating a variable for that file path, we would have needed to run `os.path.join(service_folder, os.path.basename(item).replace('mov', 'mp4'))` twice.
{: .callout}

