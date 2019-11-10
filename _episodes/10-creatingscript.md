---
title: "Creating a Script"
teaching: 15
exercises: 10
questions:
- "How can I create a Python script to run outside of Jupyter?"
- "How can I use command-line arguments?"
objectives:
- "Use argparse"
- "Transform Jupyter notebook code into a script"
keypoints:
- "Once you’ve created code you want to use repeatedly, a script is more useful than a Jupyter Notebook"
- "`argparse` is the Python module for accepting arguments from the command line"
- "`try` and `except` blocks can be used to run code that may not work in every situation"
---

During this workshop we've used Jupyter Notebooks to try out code. Notebooks are really good for doodling, sketching, testing, etc. We can try multiple approaches to a problem and develop one that we really like.

Notebooks are not as good for production usage. Once we have a solution that we like, it would be more useful to run all the code at once instead of hitting `Shift` + `Enter` on every cell in a notebook.

## Python Scripts

To move our code into a production mode, we can create a Python script file. A python script file is a text file with Python code in it. You can create a script file with any plain text editor like (Notepad++, Sublime Text, Atom, Notepad). But, you can't use a document editor like Microsoft Word or Google Docs. These editors re-encode the text into a format that Python can't interpret.

### Creating a Python Script

Anaconda also has a text editor, so we will use that to turn some of our previous file survey code into a script.

To open a text file in Anaconda, click the `+` button in the left sidebar and click on the `Text File` tile in the main tab.
A text editing interface will open.
We will immediately rename this file going to File > Save File As... and giving it the name `filesurvey.py`.

> ## Python Extensions
> 
> A python script is a text file.
> It can use anything for an extension as long as the contents are valid Python code.
> We typically use `.py` to tell other people that the file contains Python code.
{: .callout}

A Python script is that code is read and executed from the top of the script to the bottom.
If you are in a Jupyter Notebook and the first cell contains

~~~
os.path.join('video_dir', 'project_name')
~~~
{: .language-python}

and second cell contains

~~~
import os
~~~
{: .language-python}

You can execute the second cell and then the first cell and everything will work.
If you have a Python script, and the code reads 

~~~
os.path.join('video_dir', 'project_name')
import os
~~~
{: .language-python}

When you try to run this script, it will report an error.

This is a good reason to put all `import` statements at the top of a script.
This ensures that their functions have been loaded when they are used in the script.

> ## Import Statements for `filesurvey.py`
> 
> Fill in the beginning of the script file with the `import` statements that you will need to run the file survey loop that we built in Lesson 6.
>
> > ## Solution
> > We need the following modules.
> > ~~~
> > import os
> > from pymediainfo import MediaInfo
> > import csv
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

With all the necessary `import` statements are at the top of the file, we can copy-paste in the other portions of the loop.
Change any paths so that they reflect your computer's folders.

~~~
video_dir = '/Users/username/Desktop/amia19'

media_list = [ ]
all_file_data = []

for root, dirs, files in os.walk(video_dir):
    for item in files:
        if item.endswith(('.mkv', '.mov', '.wav', '.mp4', '.dv', '.iso', '.flac')):
            item_path = os.path.join(root, item)
            media_list.append(item_path)

for item in media_list:
    media_info = MediaInfo.parse(item)
    for track in media_info.tracks:
        if track.track_type == "General":
            general_data = [
                track.file_name,
                track.file_extension,
                track.format,
                track.file_size,
                track.duration]
        elif track.track_type == "Video":
            video_data = [
                track.codec_id,
                track.bit_rate,
                track.width,
                track.height,
                track.other_display_aspect_ratio,
                track.pixel_aspect_ratio,
                track.frame_rate,
                track.standard,
                track.color_space,
                track.chroma_subsampling,
                track.bit_depth,
                track.compression_mode]
        elif track.track_type == "Audio":
            audio_data = [
                track.codec_id,
                track.bit_rate,
                track.channel_s,
                track.bit_depth,
                track.sampling_rate]
    all_file_data.append(general_data + video_data + audio_data)


with open('/Users/amia19/Desktop/script_output.csv', 'w') as f:
    md_csv = csv.writer(f)
    md_csv.writerow([
        'filename',
        'extension',
        'format',
        'size',
        'duration',
        'video_codec',
        'video_bitrate',
        'width',
        'height',
        'dar',
        'par',
        'framerate',
        'standard',
        'colorspace',
        'chromasubsampling',
        'video_bitdepth',
        'video_compression',
        'audio_codec',
        'audio_bitrate',
        'audio_channels',
        'audio_bitdepth',
        'audio_samplingrate'
    ])
    md_csv.writerows(sorted(all_file_data))
~~~
{: .language-python}

After saving this file, we can run it using terminal.
Again, we'll use Anaconda's built-in terminal.
To open a terminal in Anaconda, click the `+` button in the left sidebar and click on the `Terminal` tile in the main tab.

The syntax for running a python script is `python path/to/script.py` (or `python.exe path/to/script.py` in Windows Command).

~~~
python filesurvey.py
~~~
{: .language-bash}

After running this command a new CSV named `script_output.csv` is saved to the desktop.

## Increasing Script Flexibility

By creating a script we automated some of the effort of running our Python code.
But, the current script can only gather data from the `amia19` folder and save a CSV to `script_output.csv`.
To make this more flexible, we can accept arguments from the command line.

If you have not used the command line much in the past, commands work a lot like functions in Python.
* The first thing in a command is the tool's name, e.g. `conda`
* After that, there might be subtools, e.g. `conda install`
* After that, there might be arguments that will be used by the tool, e.g. `conda install -c conda-forge ffmpeg`

If the above example was a line of Python it might look like this: `conda.install(c="conda-forge", "ffmpeg")`.

> ## Command-Line Syntax
> 
> On the command line, every tool, subtool, argument, and flag is separated by a space.
> 
> Strings typically do not have to be surrounded by quotation marks `"`.
{: .callout}

To make it easier to apply to survey other folders and save the output to new files, we will add another module to our `filesurvey.py` script, `argparse`.

First, we have to import the module at the beginning of our script.

~~~
import argparse
~~~
{: .language-python}

Because `argparse` is more abstract than other components we have used so far, we will include working code immediately.
Pasted this into script immediately after the import statements.

~~~
parser = argparse.ArgumentParser()
parser.description = "survey a directory for AV files and report on technical metadata"
parser.add_argument("-d", "--directory",
                    "required" = True,
                    help = "Path to a directory of AV files")
parser.add_argument("-o", "--output",
                    "required" = True,
                    help = "Path to the save the metadata as a CSV")
args = parser.parse_args()

print(args.directory, args.output)
~~~
{: .language-python}

Save the script.

> ## Using the new arguments
> 
> What happens when you run the following command?
> ~~~
> python filesurvey.py -d Desktop/amia19/federal_grant -o Desktop/federal_grant_files.csv
> ~~~
> {: .language-bash}
> Why didn't it create the `federal_grant_files.csv`? How would you change the script to make that happen?
>
> > ## Solution
> > 
> > Replace the hard-coded paths with the argparse arguments.
> > For example, replace
> > ~~~
> > with open('/Users/amia19/Desktop/script_output.csv', 'w') as f:
> > ~~~
> > {: .language-python}
> > with
> > ~~~
> > with open(args.output, 'w') as f:
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

`argparse` also creates help dialogues for our command-line tool.
Try:

~~~
python filesurvey.py -h
~~~
{: .language-bash}

## Trapping errors/exceptions and anticipating diverse collections

As we move our code from the niceties of the sample workshop files to the broader pool of material you might see in real life, we also need to look out for exceptions.

> ## What happens we survey non-AV files?
>
> What happens if we run the script we just created on the entire Desktop?
> ~~~
> python filesurvey.py -d Desktop -o desktop_survey.csv
> ~~~
> {: .language-bash}
> > ## Solution
> > An error is printed to the terminal, and no CSV is created.
> {: .solution}
{: .challenge}

This is where we can use the programming concept of `try` and `except`.
First we try a bit of code, and if an error results, we try a different piece of code that should work.
For example, if we come across video files that don't have all the attributes that we expect,
1. our code errors out
2. the `for` loop stops
3. the script doesn't finish

Instead, we'll ask the code to try and collect those attributes.
When python comes across files that are missing some of these key elements, it will return None (a null data type of its own making) rather than breaking down.

~~~
for item in media_list:
    media_info = MediaInfo.parse(item)
    for track in media_info.tracks:
        if track.track_type == "General":
            general_data = [
                track.file_name,
                track.file_extension,
                track.format,
                track.file_size,
                track.duration]
        if track.track_type == "Video":
            try:
                video_data = [
                    track.codec_id,
                    track.bit_rate,
                    track.width,
                    track.height,
                    track.display_aspect_ratio,
                    track.pixel_aspect_ratio,
                    track.frame_rate,
                    track.standard,
                    track.color_space,
                    track.chroma_subsampling,
                    track.bit_depth,
                    track.compression_mode]
            except:
                video_data = [None, None, None, None, None, None, None, None, None, None, None, None]    
        if track.track_type == "Audio":
            try:
                audio_data = [
                    track.format,
                    track.bit_rate,
                    track.channel_s,
                    track.bit_depth,
                    track.sampling_rate]
            except:
                audio_data = [None, None, None, None, None]
    all_file_data.append(general_data + video_data + audio_data)
~~~
{: .language-python}

By making this effort, we ensure two things:
1. that our code will continue to function despite the strange files we throw at it,
2. that our MediaInfo data will be consistent from file to file and ready for export. 

