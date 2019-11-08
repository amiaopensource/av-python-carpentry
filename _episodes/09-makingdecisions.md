---
title: "Lossless Compression and Codec Decisions"
teaching: 15
exercises: 15
questions:
- "How can I use Python to losslessly compress files?"
- "How can I use Python to move files by codec?"
objectives:
- "Use conditionals to perform different actions"
keypoints:
- "Small variations"
---

The service file transcoding was a huge success. One command and tens of service copies created. Alice has been hearing some good things about lossless compression. Maybe the service file code can be used for preservation files.

## Python, ffmpeg, and lossless transcoding

For this workshop, we'll experiment with transcoding video to FFV1 and rewrapping the file in an MKV.
First, we'll mirror the setup for service files.

~~~
mkv_folder = os.path.join('Desktop/amia19/mkv')
if not os.path.exists(mkv_folder):
	os.makedirs(mkv_folder)
~~~
{: .language-python}

From ffmprovisr we can find the following recipe:
`ffmpeg -i input_file -map 0 -dn -c:v ffv1 -level 3 -g 1 -slicecrc 1 -slices 16 -c:a copy output_file.mkv`

In this case, let's not look before we leap.

~~~
# /federal_grant/napl_0368_pres.mov
test_mkv = os.path.join(mkv_folder, os.path.basename(media_list[12]).replace('mov', 'mkv'))
subprocess.call(['ffmpeg', '-i', media_list[12], '-map', '0', '-dn', '-c:v', 'ffv1', '-level', '3', '-g', '1', '-slicecrc', '1', '-slices', '16', '-c:a', 'copy', test_mkv])
~~~
{: .language-python}

~~~
0
~~~
{: .output}

> ## How Much Compression Did Get?
> How would calculate the file size difference between the original and transcoded file?
> > os.stat(output_path).st_size
> {: .solution}
{: .challenge}

> ## What?
> If the results aren't what you expected, what are your next steps to understand what happened?
> > Check mediainfo
> > Ask a colleague
> {: .solution}
{: .challenge}

FFV1 is a great codec for losslessly compressing uncompressed video.
However, if a video is already encoded with a lossy codec, like DV or born-digital videos, transcoding to FFV1 will increase the file size.
For comparison, it's like converting a JPEG image file to TIFF or JPEG2000.
The lossy original was designed to encode that information as efficiently as it could.
Encoding it with a different codec will just encode the existing data in a more verbose format.

Before we jump into building our transcoding loop, let's get rid of this bad file.

~~~
os.remove(test_mkv)
os.listdir(mkv_folder)
~~~
{: .language-python}

## Adding Conditionals to a Lossless Transcoder

To create service files, we checked if an output file already existed.
To do lossless encoding, we'll need to check the format of the original.

~~~
for item in media_list:
    if item.endswith('mov'):
        output_file = os.path.join(mkv_folder, os.path.basename(item).replace('mov', 'mkv'))
        media_info = MediaInfo.parse(item)
        for track in media_info.tracks:
            if track.track_type == "General":
                if not track.format == "DV":
                    print(output_file, item)
~~~
{: language-python}

> ## What?
> The ffmprovisr recipe for lossless compression is basic.
> What additional arguments would you be interested in adding to a preservation transcoding?
> > ???
> > pal/ntsc?
> > flac audio
> > more embedded stuff?
> {: .solution}
{: .challenge}

