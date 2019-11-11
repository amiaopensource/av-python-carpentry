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

Alice had 3 tasks she wanted to complete.

1. create service copy MP4s for all of the files on her hard drive
2. share the duration of each video file with a cataloger
3. preserve the files

She'll need to create a transcoding script to take care of the first step.
You may have noticed that the "120th Anniversary" project included service files.
So why not take advantage of that work first?

## Moving Files

Unfortunately, when we review the folders where those service files live, it looks like they're all located in [BagIt](https://en.wikipedia.org/wiki/BagIt) bags.
Moving the files out of the bags would render the bags invalid.
To avoid that issue, we can make a copy of those service files.

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

`shutil.copy()` takes two arguments, `src` (path to source file) and `dest` (path to destination). The source path we can pull from our `media_list`. The destination path, we'll have to create.

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
If we try to copy anything to this folder, we'll get an error.

~~~
shutil.copy(qckitty_path, service_folder)
~~~
{: .language-python}

As there wasn't a landing directory ready for our QCKitty, we ended up with an extensionless file called "service" file.
It is exactly the same size as our gif (8.7 MB).
We wanted to copy the gif into a folder called "service", except the "service" folder didn't exist.

Let's delete that "service" file and do things the right way. 
We can use the `os` module to make this folder.
But, before doing that, it's good practice to make sure the folder doesn't already exist.
Among python users, this is called Looking Before You Leap (LBYP).

~~~
if not os.path.exists(service_folder):
	os.makedirs(service_folder)
~~~
{: .language-python}

And now that the destination directory actually exists, we should be able to copy files there.

~~~
shutil.copy(kittypath, service_folder)
~~~
{: .language-python}

Now we can unleash a loop on this problem.

~~~
for item in media_list:
	if item.endswith('mp4'):
		shutil.copy(item, service_folder)

os.listdir(service_folder)
~~~
{: .language-python}

That's a big chunk of Alice's task performed with a minimal amount of work.
For the next part, she'll need to do some transcoding.

## Using ffmpeg from Python

Some command-line tools like MediaInfo have a Python module that makes them easier to use in a script.
Even if they don't, we can use the `subprocess` module to run terminal commands from our script.

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
We can turn that into a list.

~~~
['ffmpeg', '-i', input_file, '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', output_file]
~~~
{: .language-python}

Just like with `shutil.copy()`, we need to provide source and destination paths for this command to function properly.
Again, the source will be taken from `media_list`.
For the destination, we can use the `service_folder`, but `ffmpeg` requires a path with a filename.

Since we're transcoding preservation files to make service files, we can use the preservation filename as the basis of our service filename.
And because we're dealing with path, let's use an `os.path` function.

~~~
os.path.basename(media_list[0])
~~~
{: .language-python}

~~~
napl1777.mov
~~~
{: .output}

That's a good start.
Now we need the filename to have an 'mp4' extension instead of 'mov'.
For that, we can use the `replace()` function for strings.

~~~
pres_filename = os.path.basename(media_list[0])
pres_filename.replace('mov', 'mp4')
~~~
{: .language-python}

~~~
napl1777.mp4
~~~
{: .output}

For a final step, we can join the new filename to the `service_folder` path, all within the context of the `ffmpeg` command and `subprocess` call.

~~~
subprocess.run(['ffmpeg', '-i', media_list[0], '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', os.path.join(service_folder, os.path.basename(media_list[0]).replace('mov', 'mp4'))])
~~~
{: language-python}

~~~
CompletedProcess(['ffmpeg', '-i', 'Desktop/amia19/federal_grant/napl1777.mov', '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', 'Desktop/amia19/service/napl1777.mp4'], returncode=0)
~~~
{: .output}

## Looking Before You Transcode

It might be tempting to wrap this all in a `for` loop and celebrate, but let's think through what might happen when we run this code across all the files in `media_list`.

> ## Problems with Transcoding Everything
> What issues would face if you tried to transcode every file in `media_list` right now?
> What steps would you take to avoid those issues?
> > You would transcode files that you already have service files for, like `napl1777.mov`.
> > You would double-transcode the service files you have, like the 120th Anniversary service files.
> > To avoid this, you would need create some `if` statements to skip transcoding for those files.
> {: .solution}
{: .challenge} 


Let's just focus on untranscoded movs.

~~~
for item in media_list:
	if item.endswith('mov'):
		output_file = os.path.join(service_folder, os.path.basename(item).replace('mov', 'mp4'))
		if not os.path.exists(output_file):
			subprocess.run(['ffmpeg', '-i', item, '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', output_file])

os.listdir(service_folder)
~~~
{: language-python}

Success!


