---
title: "3. Transcoding 1 File"
teaching: 20
exercises: 10
questions:
- "How can I use Python to transcode a file"

objectives:
- ""

keypoints:
- ""
---

Alice has heard good things about saving space by using lossless compression on preservation master files. Perhaps she could find a way to use Python to generate new, lossless preservation masters. Alice is taking a big leap, copying code from all over the Internet (Stack Exchange, ffmprovisr, random forums). This is something that people with all levels of coding skill do on a regular basis, and while this code may initially not make sense, as you continue to work with it, things will become more clear.

## Python, FFmpeg, and Lossless Transcoding

Let's experiment with transcoding our preservation master files to FFV1 and rewrapping them from `mov` to `mkv`.

Leaning again on [ffmprovisr](https://amiaopensource.github.io/ffmprovisr/), we can find the following recipe for transcoding to FFV1/MKV:

`ffmpeg -i input_file -map 0 -dn -c:v ffv1 -level 3 -g 1 -slicecrc 1 -slices 16 -c:a copy output_file.mkv`

And as an experiment, let's choose a random file in the `federal_grant` folder.

~~~
subprocess.run(['ffmpeg', '-i', 'pyforav/federal_grant/napl_0371_pres.mov', '-map', '0', '-dn', '-c:v', 'ffv1', '-level', '3', '-g', '1', '-slicecrc', '1', '-slices', '16', '-c:a', 'copy', ~/Desktop/napl_0371_pres.mkv'])
~~~
{: .language-python}

~~~
CompletedProcess(args=['ffmpeg', '-i', '/Users/benjaminturkus/Desktop/pyforav/federal_grant/napl_0371_pres.mov', '-map', '0', '-dn', '-c:v', 'ffv1', '-level', '3', '-g', '1', '-slicecrc', '1', '-slices', '16', '-c:a', 'copy', 'Desktop/pyforav/mkv/napl_0371_pres.mkv'], returncode=0)
~~~
{: .output}

Check out your Desktop. Take a look at the file size of napl_0371_pres.mkv, and compare to napl_0371_pres.mov .

Super cool, but also very annoying to type out the file path for each inidividual file. How do we do this for all of the files that we have? The first thing we'll need is a list, so let's start there.
