---
title: "Analysis and Visualization"
teaching: 20
exercises: 10
questions:
- "How can I use Python/pandas to analyze data gathered from/about my media collection?"
- "How can I use data visualization tools like matplotlib or seaborn to present my analysis in a clear and digestible way?"
objectives:
- "Use pandas to analyze MediaInfo metadata"
- "Use matplotib to visualize data analysis"
keypoints:
- "Automate your CSV/Excel"
- "Analyze data as you go"
---

## What's Next? Data Analysis and Visualization

Alice is happy with the fruits of her efforts--she's gathered technical metadata from every file contained on her hard drives, she's made copies of existing MP4s, she's created new MP4s, she's losslessly transcoded her uncompressed Quicktime master files, and she's found ways to skip over those files (DV-encoded) that wouldn't benefit from this kind of transformation.

But Alice has realized that while doing the work is one thing, being able to analyze the data surrounding her efforts and communicate that information upstream to senior management at New Amsterdam, is perhaps as important as creating smart, automated workflows to begin with.


> ## pandas: the Google Sheets/Excel replacement you didn't know you were missing
>
> pandas is yet another python add-on, this time an extraordinarily powerful data analysis engine that comes from the world of finance, specifically quantitiave analytics. 
> While it may seem intimidating, pandas is remarkably user friendly and intuitive.
> pandas is excellent at:
> * normalizing data from diverse sources
> * performing calculations on rows or colmns
> * concatenating or joining or grouping by category
> * visualization 
>
> And pandas is really excellent at: reading/writing to a range of metadata formats, including Excel,CSV, JSON, and more!
> 
{: .callout}

## Alice's new adventure: creating a scatterplot of compression ratios 

After learning to work with FFmpeg, and testing the integrity and losslessness of FFV1, Alice is finally ready to present her findings to administrators and make a compelling case for transitioning the Library to lossless encodings for all audio, video, and film digitization. 
Alice can demonstrate checksum comparisons, and she can speak anecdotally about file size savings and ease-of-use, but she has a sense that overarching numbers and, even better, visualizations in the form of charts and graphs, might be more convincing to her harried supervisors who are too busy for the nitty-gritty.

Why a scatterplot? Why not? 
The combination of pandas and matplotlib can generate nearly any form of visualization--including pie charts, bar charts, heat maps, line graphs, histograms--but there is something unique to scatterlots that can highlight the variance inherent in FFV1 compression. 
Bottom line: FFV1 is smart and adaptive, and not all video content compresses in the exact same way, to the exact same degree. 
With a scatterplot, we can see these differences, and possilby learn how to better predict compression ratios in the future. 

## Another set of FFV1/MKV, but this time with a mistaken twist

Let's begin by transcoding another set of FFV1/MKv files from our Quicktime masters, but, to make things more interesting, let's assume that Alice accidentally forgot to include an if statement separating out DV Quicktime files.

~~~
media_list = [ ]

for root, dirs, files in os.walk(video_dir):
    for file in files:
        if file.endswith('.mov'):
            file_path = os.path.join(root, file)
            media_list.append(file_path)
~~~
{: .language-python}

And let's make a new landing pad for our MKVs:

~~~
new_mkv_folder = os.path.join('Desktop/amia19/new_mkvs')
if not os.path.exists(new_mkv_folder):
	os.makedirs(new_mkv_folder)
~~~
{: .language-python}

And let's transcode our set, this time not paying attention to underlying codecs (DV will get bigger, not smaller!):

~~~
for item in media_list:
        output_file = os.path.join(new_mkv_folder, os.path.basename(item).replace('mov', 'mkv'))
        subprocess.call(['ffmpeg', '-i', item, '-map', '0', '-dn', '-c:v', 'ffv1', '-level', '3', '-g', '1', '-slicecrc', '1', '-slices', '16', '-c:a', 'copy', output_file])
~~~
{: .language-python}

> ## Checking our work
>
> If we wanted to see if we had an equal count of MOVs and MKVs, how would we do so?
> 
> > ## Solution
> > len(media_list) returns 102
> > len(new_mkv_folder) returns 23
> > What gives?
> > The len of media_list counts the list of full file paths that we created for the MOVs; the len of new_mkv_folder returns the number of characters in the relative path 'Desktop/amia19/new_mkvs'.
> > Annoying, to be sure, but to get the results we're after, we'll need to take another os.walk around the block.
> > 
> {: .solution}
{: .challenge}

~~~
mkv_list = [ ]

for root, dirs, files in os.walk(new_mkv_folder):
    for file in files:
        if file.endswith('.mkv'):
            file_path = os.path.join(root, file)
            mkv_list.append(file_path)
mkv_list
~~~
{: .language-python}


## The DataFrame--pandas 


~~~
import os
os.listdir()
~~~
{: .language-python}

