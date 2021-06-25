---
title: "Transcoding One File"
teaching: 20
exercises: 10
questions:
- "How can I use Python to transcode a file"

objectives:
- "Import the `subprocess` module"
- "Transcode and compress a single Quicktime file"

keypoints:
- "Python syntax can be a little tricky, but starting small and building out is a good general strategy"
---

Alice has heard good things about saving space by using lossless compression on preservation master files. Perhaps she could find a way to use Python to generate new, lossless preservation masters. Alice is taking a big leap, copying code from all over the Internet (Stack Exchange, ffmprovisr, random forums). This is something that people with all levels of coding skill do on a regular basis, and while this code may initially not make sense, as you continue to work with it, things will become more clear.

## Python, FFmpeg, and Lossless Transcoding

Let's experiment with transcoding one of our preservation master files to FFV1 and rewrapping it from `mov` to `mkv`.

Leaning on [ffmprovisr](https://amiaopensource.github.io/ffmprovisr/), we can find the following recipe for transcoding to FFV1/MKV:

`ffmpeg -i input_file -map 0 -dn -c:v ffv1 -level 3 -g 1 -slicecrc 1 -slices 16 -c:a copy output_file.mkv`

And as an experiment, let's choose a random file in the `federal_grant` folder. First, let's navigate to that folder (and you do so, try playing around with Jupyter's tab completion):

~~~
cd Desktop/pyforav/federal_grant/
~~~
{: .language-python}

We can double check that we're in the right place by using Python's `getcwd()` command. This is part of the `os` module in Python so we'll need to load the module first.:

~~~
import os
os.getcwd()
~~~
{: .language-python}

~~~
'/Users/USERNAME/Desktop/pyforav/federal_grant'

~~~
{: .output}

Now let's import the `subprocess` module, which will allow us to invoke external programs from within our Python code:

~~~
import subprocess
~~~
{: .language-python}

It's time for some transcoding. Any of the Federal Grant files would do, but let's play with `napl_0371_pres.mov`:

~~~
subprocess.run(['ffmpeg', '-i', 'napl_0371_pres.mov', '-map', '0', '-dn', '-c:v', 'ffv1', '-level', '3', '-g', '1', '-slicecrc', '1', '-slices', '16', '-c:a', 'copy', 'napl_0371_pres.mkv'])
~~~
{: .language-python}

~~~
CompletedProcess(args=['ffmpeg', '-i', 'napl_0371_pres.mov', '-map', '0', '-dn', '-c:v', 'ffv1', '-level', '3', '-g', '1', '-slicecrc', '1', '-slices', '16', '-c:a', 'copy', 'napl_0371_pres.mkv'], returncode=1)

~~~
{: .output}

Take a peek inside the Federal Grant folder and compare the file sizes of napl_0371_pres.mov and napl_0371_pres.mkv. One's much smaller, right?

Super cool, but it's also a little tedious to type out the file path for each individual file. How can we do this for all of the files that we have within our different directories? The first thing we'll need is a list, so let's start there.
