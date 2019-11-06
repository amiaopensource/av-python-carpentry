---
title: "Moving and Transcoding Files"
teaching: 10
exercises: 20
questions:
- "How do you use command line utilities in Python?"
objectives:
- "Use shutil to copy and move files"
- "Use subprocess to call ffmpeg"
keypoints:
- "Collecting filepaths is often the first step of any AV script"
---

Alice had 3 tasks she wanted to finish.

1. create service copy MP4's for all of the files on the drive
2. share the duration of each video with a cataloger
3. preserve the files

She'll need to create a transcoding script to do the first step.
You might have noticed that the "120th Anniversary" project included service files.
So why not take advantage of that work first?

## Moving Files

Unfortunately, when we look at the folders that those files are in, it looks like they're all in BagIt bags.
Moving the files out of the bags would render the bag invalid.
To avoid that issue, we can make a copy of those files.

For any kind of local file moving or copying, the `shutil` module is a good starting place.
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

~~~
ERROR: something
~~~
{: .output}

Using the `os` module, we can use Python to make this folder.
Before doing that, it's good practice to make sure the folder doesn't already exist.
Among python users, this is called Looking Before You Leap (LBYP).

~~~
if not os.path.exists(service_folder):
	os.makedirs(service_folder)
~~~
{: .language-python}

And now that the destination actually exists, we should be able to copy files there.

~~~
shutil.copy(qckitty_path, service_folder)
~~~
{: .language-python}

Now we can unleash a loop on this problem.

~~~
for item in media_list:
	if item.endswith('mp4'):
		shutil(item, service_folder)

os.listdir(service_folder)
~~~
{: .language-python}

That's a big chunk of the task done for minimal work.
For the next part we'll need to invoke some transcoding.

## Using ffmpeg from Python

Some command-line tools like MediaInfo have Python module that make them easier to use in a script.
Unfortunately, `ffmpeg` the workhorse utility for media transcoding doesn't.
However, we can still Python to work with `ffmpeg` or any command line program using the `subprocess` module.

~~~
import subprocess
~~~
{: .language-python}

We'll use the `subprocess.call()` function.

~~~
help(subprocess.call())
~~~
{: .language-python}

We can try out the example code to see how this works.

~~~
subprocess.call(['ls', '-l'])
~~~
{: .language-python}

If you've used command line tools, you might recognize `ls -l` as the way to return the contents of a directory as a list.
`subprocess.call()` structures that command differently.
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

Just like with `shutil.copy()`, we need to provide two source and destination paths to this command.
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
We now need the filename to have an 'mp4' extension instead of 'mov'.
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

For a final step, we can join the new filename to the `service_folder` path, all in the context of the `ffmpeg` command and `subprocess` call.

~~~
subprocess.call(['ffmpeg', '-i', media_list[0], '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', os.path.join(service_folder, os.path.basename(media_list[0]).replace('mov', 'mp4'))])
~~~
{: language-python}

~~~
0
~~~
{: .output}

## Looking Before You Transcode

It might be tempting to wrap this all in a `for` loop and celebrate, but let's think through what might happen when we run this code across all the files in `media_list`

> ## Problems with Transcoding Everythin
> What issues would face if you tried to transcode every file in `media_list` right now?
> What steps would you take to avoid those issues?
> > You would transcode files that you already have service files for, like `napl1777.mov`.
> > You would double-transcode the service files you have, like the 120th Anniversary service files.
> > To avoid this, you would need create some `if` statements to skip transcoding for those files.
> {: .solution}
{: .challenge} 


Let's just focus on untranscoded mov's.

~~~
for item in media_list:
	if item.endswith('mov'):
		output_file = os.path.join(service_folder, os.path.basename(item).replace('mov', 'mp4'))
		if os.path.exists(output_file):
			subprocess.call(['ffmpeg', '-i', item, '-c:v', 'libx264', '-pix_fmt', 'yuv420p', '-c:a', 'aac', output_file])

os.listdir(service_folder)
~~~
{: language-python}

Success!


