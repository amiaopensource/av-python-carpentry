---
title: "Lossless Compression and Codec Decisions"
teaching: 10
exercises: 10
questions:
- "How can I use Python to losslessly compress files?"
- "How can I use Python to make decisions based upon codec?"
objectives:
- "Use conditionals to perform different actions"
keypoints:
- "Transcoding lossy codecs to lossless can yield an increase in size"
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

Alice's effort to copy pre-existing service files and transcode new service files was a big success. A couple of commands and she ended up a single folder containing a large number of lightweight files ready to deploy. But Alice has also heard good things about saving space by using lossless compression on preservation master files. Perhaps she could adapt the service file code that she used previously to generate new, lossless preservation masters.

## Python, FFmpeg, and Lossless Transcoding

Let's experiment with transcoding our preservation master files to FFV1 and rewrapping them from `mov` to `mkv`.
First, we'll need a place to put our newly created mkv files. Do you remember how to create a new directory using Python?

> ## Creating a New Folder
> What code would you run to create a new folder inside `pyforav` called `mkv`?
> > ## Solution
> >
~~~
mkv_folder = os.path.join('Desktop', 'pyforav', 'mkv')
if not os.path.exists(mkv_folder):
	os.makedirs(mkv_folder)
~~~
> > {: .language-python}
> >
> >
> > As with the `service` folder, it's good working practice to use an `if` statement to ensure that the folder being created doesn't already exist and that we don't overwrite any data. 
> {: .solution}
{: .challenge} 

Leaning again on [ffmprovisr](https://amiaopensource.github.io/ffmprovisr/), we can find the following recipe for transcoding to FFV1/MKV:

`ffmpeg -i input_file -map 0 -dn -c:v ffv1 -level 3 -g 1 -slicecrc 1 -slices 16 -c:a copy output_file.mkv`

And as an experiment, let's choose a random file in the `federal_grant` folder and NOT look before we leap.

~~~
# /federal_grant/napl_0368_pres.mov
test_mkv = os.path.join(mkv_folder, os.path.basename(sorted(mov_list)[42]).replace('mov', 'mkv'))
subprocess.run(['ffmpeg', '-i', sorted(mov_list)[42], '-map', '0', '-dn', '-c:v', 'ffv1', '-level', '3', '-g', '1', '-slicecrc', '1', '-slices', '16', '-c:a', 'copy', test_mkv])
~~~
{: .language-python}

~~~
CompletedProcess(args=['ffmpeg', '-i', '/Users/benjaminturkus/Desktop/pyforav/federal_grant/napl_0371_pres.mov', '-map', '0', '-dn', '-c:v', 'ffv1', '-level', '3', '-g', '1', '-slicecrc', '1', '-slices', '16', '-c:a', 'copy', 'Desktop/pyforav/mkv/napl_0371_pres.mkv'], returncode=0)
~~~
{: .output}

> ## How Much Compression Did We Get?
> How would you calculate the file size difference between the original and transcoded file?
> > ## Solution
> > Browse to the folders for each file, collect their file informtion and compare them.
> >
> > Later we will learn how to collect file size information with Python. Here's a preview.
> > ~~~
> > os.stat(sorted(mov_list)[42]).st_size - os.stat(test_mkv).st_size
> > ~~~
> > {: .language-python}
> >
> > ~~~
> > -1782275
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}

> ## What?
> If the results aren't what you expected, what are your next steps to understand what happened?
> > ## Solution
> > Check MediaInfo
> > Ask a colleague
> {: .solution}
{: .challenge}

FFV1 is a great codec for losslessly compressing uncompressed video.
However, if a video is already encoded with a lossy codec, like DV or born-digital videos, transcoding to FFV1 will increase the file size.
As a comparison, it would similar to converting a JPEG image file to TIFF or JPEG2000.
The lossy original was designed to encode that information as efficiently as it could.
Encoding it with a different lossless codec will just store the existing information in a more verbose format.

## Transcoding Everything to MKV/FFV1
For now, let's go ahead and transcode everything just to see the difference.

~~~
for item in mov_list:
    mkv_path = os.path.join(mkv_folder, os.path.basename(item).replace('mov', 'mkv'))
    subprocess.run(['ffmpeg', '-i', item, '-map', '0', '-dn', '-c:v', 'ffv1', '-level', '3', '-g', '1', '-slicecrc', '1', '-slices', '16', '-c:a', 'copy', mkv_path])
~~~
{: language-python}

> ## How Much Compression Did We Get?
> How would calculate the file size difference between all of the original and transcoded files?
> > ## Solution
> > For this, using the file browser would be pretty difficult in most situations.
> > Instead we will need Python for this.
> > Read through the following code to see what parts you understand.
> > ~~~
> > original_size = 0
> > for item in mov_list:
> >     original_size = original_size + os.stat(item).st_size
> > compressed_size = 0
> >
> > mkv_list = glob.glob(os.path.join(mkv_folder, '*mkv'))
> > for item in mkv_list:
> >     compressed_size = compressed_size + os.stat(item).st_size
> > 
> > print(original_size, compressed_size)
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

> ## Keep on improving
> This ffmprovisr recipe for lossless compression is a good starting point.
> What additional arguments would you be interested in adding to a preservation transcoding?
> > ## Solution
> > You might want to create slightly different recipes for PAL and NTSC video.
> > You might want to use a different audio codec like flac.
> {: .solution}
{: .challenge}
