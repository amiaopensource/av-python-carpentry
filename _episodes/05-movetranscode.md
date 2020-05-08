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
> If you get lost or fall behind or need to burn it all down and start again, here's some quick catch-up code that you can re-> run to get back up to speed.
>
> ~~~
> video_dir = '/Users/username/Desktop/amia19'
>
> or
>
> video_dir = 'C:\\Users\\username\\Desktop\\amia19'
> 
> ~~~
> {: .language-python}
>
> Then copy and paste the following:
>
> ~~~
> import os
> import subprocess
> mov_list = []
for root, dirs, files in os.walk(video_dir):
    for file in files:
        if file.endswith('.mov'):
            item_path = os.path.join(root, file)
            mov_list.append(item_path)
> ~~~
> {: .language-python}
{: .callout}


Alice had 3 tasks she wanted to complete.

1. create service copy MP4s for all of the files on her hard drive
2. share the duration of each video file with a cataloger
3. preserve the files

She'll need to create a transcoding script to take care of the first step.
You may have noticed that the "120th Anniversary" project included service files.
So why not take advantage of that work first?

## Moving Files

When we review the folders where those service files live, it looks like they're all located in [BagIt](https://en.wikipedia.org/wiki/BagIt) bags.
Because bags have multiple mechanisms to track the fixity of their contents, we don't want to change their contents if we don't have to.
For this case, we can make copies of the existing service files.

For any kind of local file moving or copying, the `shutil` module is a good starting point.
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

`shutil.copy()` takes two arguments, `src` (path to source file) and `dest` (path to destination). The source path we can pull from our `mov_list`. The destination path, we'll have to create.

Let's make a single subfolder in the `amia19` folder to hold copies of the service files.

~~~
service_folder = os.path.join('Desktop', 'amia19', 'service')
service_folder
~~~
{: .language-python}

~~~
'Desktop/amia19/service'
~~~
{: .output}

This folder doesn't actually exist yet.
It's just a string. 
If we try to copy anything to this folder, we won't get the results we might expect.

~~~
shutil.copy(mov_list[0], service_folder)
~~~
{: .language-python}

As there wasn't a landing directory ready for our video, we ended up with an extensionless file called "service" file.
We wanted to copy the gif into a folder called "service", except the "service" folder didn't exist.

Let's delete that "service" file and do things the right way. 

Now, let's use the `os` module to make this folder.
But, before doing that, it's good practice to make sure the folder doesn't already exist.
Among python users, this is called Looking Before You Leap (LBYP).

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

Finally, let's use a loop to do this for all of the existing service files.

~~~
mp4_list = glob.glob(os.path.join(video_dir, '**', '*mp4'))
for item in mp4_list:
	shutil.copy(item, service_folder)

os.listdir(service_folder)
~~~
{: .language-python}

That's a big chunk of Alice's task performed with a minimal amount of work.
For the next part, she'll need to do some transcoding.

## Using ffmpeg from Python

Python can be used to run other scripts and programs on a computer.
For example, if there is a command-line utility that does an essential function, you can incorporate it into a Python script that automates it across a group of files.
For this lesson, we will use the `subprocess` module to run terminal commands from our script.
This module work for all command-line tools.
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

First, let's assemble an FFMPEG command to make mp4/h264 files.
And since we're mortals, we'll use the wonderful [ffmprovisr](https://amiaopensource.github.io/ffmprovisr/#transcode_h264)

The suggested command is `ffmpeg -i input_file -c:v libx264 -pix_fmt yuv420p -c:a aac output_file`.
The 'subprocess.run()' function requires the command to be in a list format.
We do that by treating the spaces in the command as the delimiters for each item in our list.
When we use this function in a for-loop, most of these items will remain the same each time the command runs, but we will change the input and output files.
For those, we will use a variable instead of a string.

~~~
['ffmpeg', '-i', input_file, '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', output_file]
~~~
{: .language-python}

The `input_file` and `ouput_file` are like the source and destination paths we needed for `shutil.copy()`.
We can take `input_file` from the first entry on our `mov_list`.
For `output_file`, we can use the `service_folder`, but `ffmpeg` requires a path with a filename.

Since we're transcoding preservation files to make service files, we can use the preservation filename as the basis for our service filename.
And because we're dealing with paths, let's use an `os.path` function.

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
CompletedProcess(['ffmpeg', '-i', 'Desktop/amia19/federal_grant/napl1777.mov', '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', 'Desktop/amia19/service/napl1777.mp4'], returncode=0)
~~~
{: .output}

> ## Using variables 1
> One of the challenging things about learning to program is how and when to use variables.
> You can be verbose and store the result of every change to a variable like this.
> ~~~
> original_filename = os.path.basename(mov_list[0])
> output_filename = original_filename.replace('mov', 'mp4')
> output_filepath = output_file = os.path.join(service_folder, output_filename)
> subprocess.run(['ffmpeg', '-i', input_file, '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', output_filepath])
> ~~~
> {: language-python}
> You can also be very terse and nest all of your functions inside of each other like this.
> ~~~
> subprocess.run(['ffmpeg', '-i', input_file, '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', os.path.join(service_folder, os.path.basename(mov_list[0]).replace('mov', 'mp4'))])
> ~~~
> {: language-python}
>
> Both of these approaches will work.
> In practice, most code falls somewhere between these extremes to produce something that is concise but readable.
> When you're starting, it's easier to focus on getting code to run.
> As you continue you may find resources like the [PEP-8](https://www.python.org/dev/peps/pep-0008/) style guide useful.
{: .callout}

## Looking Before You Transcode

It might be tempting to wrap `ffmpeg` command in a `for` loop and celebrate, but let's think through what might happen when we run this code across all the files in `mov_list`.

> ## Problems with Transcoding Everything
> What issues would face if you tried to transcode every file in `mov_list` right now?
> What steps would you take to avoid those issues?
> > You would transcode files that you already have service files for, like `napl1777.mov`.
> > To avoid this, you would need create an `if` statements to skip transcoding for those files.
> {: .solution}
{: .challenge} 


Let's just focus on untranscoded movs. To do that, we'll look before we leap by seeing if a service copy already exists for that mov file.

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

> ## Using variables 2
> The code above is an example of how a variable can be useful.
> By first storing the service file path to `output_file`, we are able to use it in two different contexts.
> First, to see if the service file already exists.
> Second, to store the newly created service file if it doesn't.
> Without creating a variable for that file path, we would have needed to run `os.path.join(service_folder, os.path.basename(item).replace('mov', 'mp4'))` twice.
{: .callout}

