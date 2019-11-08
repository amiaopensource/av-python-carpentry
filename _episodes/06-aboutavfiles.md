---
title: "Finding Out About AV Files"
teaching: 20
exercises: 10
questions:
- "How can I use Python to collect more technical metadata?"
- "How can I use Python to create CSVs?"
Objectives:
- "Use pymediainfo to collect additional (and multiple) pieces of technical metadata"
- "Use the csv module to export that metadata to a file"
keypoints:
- "Domain specific functions will often require custom tools"
---

## Additional MediaInfo Attributes 

File paths, file sizes, and durations are all well and good, but when working with AV files at scale, there are a number of other MediaInfo attributes that can yield insight into our collections and help inform decision-making down the road.  

What are some of these key pieces of technical metadata?
The answer to this question will, of course, vary based upon your institution’s needs and the workflows you’ve devised, but a short-list of relevant information might include:

* File name
* File extension
* File format 
* File size
* File duration
* Video codec
* Video bitrate
* Video width and height
* Display aspect ratio
* Pixel aspect ratio
* Frame rate
* Video standard
* Color space
* Chroma subsampling
* Video bit depth
* Scan type 
* Compression mode
* Audio codec
* Audio bitrate
* Audio channels
* Audio bit depth
* Audio sampling rate

How can we build on the scripts that we’ve already written to retrieve this expanded set of data?
How can we anticipate and compensate for a potentially diverse set of media files (audio, video, mix of formats with different technical characteristics, etc.) that may or may not contain all of these things?
And, after we’ve determined that our code functions as designed, how can we export this information into a more easily parsed form (like CSV)?

## Taking greater advantage of pymediainfo

Let’s begin by returning to our media-specific file path gathering starter code.
We can pick up where we left off, with a script that uses pymediainfo to gather the duration and size of our files:

~~~
media_list = []
sizes = []
durations = []

for root, dirs, files in os.walk(video_dir):
    for item in files:
        if item.endswith(('.mkv', '.mov', '.wav', '.mp4', '.dv', '.iso', '.flac')):
            item_path = os.path.join(root, item)
            media_list.append(item_path)

for item in media_list:
    media_info = MediaInfo.parse(item)
    for track in media_info.tracks:
        if track.track_type == "General":
            durations.append(track.duration)
            sizes.append(track.file_size)
~~~
{: .language-python}

With some adjustments, we can build this script out to collect the full list of the technical attributes described above:

~~~
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
~~~
{: .language-python}

This may look wildly intimidating, but we’re really just making a small expansion on our previous efforts.
First, we've used lists to collect a single piece information about a bunch of files.
Now, we're collecting multiple pieces of information, so we store all of that information for each file that we survey in a list.
In the end, we create `all_file_data`, a “multi-dimensional container,” or a list that contains wihin it a series of lists, one for each file. 

Let's print each item in `all_file_data` to see what this looks like.

~~~
for item in sorted(all_file_data):
    print(item)
~~~
{: .language-python}


~~~
['napl_0642_pres', 'mov', 'MPEG-4', 13199199, 468, 'v210', 223724851, 720, 486, ['4:3'], '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'in24', 2304000, 2, 24, 48000]
['napl_1777_pres', 'mov', 'MPEG-4', 2832743, 111, 'v210', 223724851, 720, 486, ['4:3'], '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'in24', 2304000, 2, 24, 48000]
['napl_0171_pres', 'mov', 'MPEG-4', 7541223, 267, 'v210', 223724851, 720, 486, ['4:3'], '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'in24', 2304000, 2, 24, 48000]
['napl_1464_pres', 'mov', 'MPEG-4', 16023615, 568, 'v210', 223724851, 720, 486, ['4:3'], '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'in24', 2304000, 2, 24, 48000]
['napl_0458_pres', 'mov', 'MPEG-4', 30174111, 1085, 'v210', 223724851, 720, 486, ['4:3'], '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'in24', 2304000, 2, 24, 48000]
['napl_0411_pres', 'mov', 'MPEG-4', 9429613, 337, 'v210', 223724851, 720, 486, ['4:3'], '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'in24', 2304000, 2, 24, 48000]
['napl_0708_pres', 'mov', 'MPEG-4', 26396185, 935, 'v210', 223724851, 720, 486, ['4:3'], '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'in24', 2304000, 2, 24, 48000]
...
~~~
{: .output}

For each file, we generated technical metadata with pymediainfo and then stored select pieces of metadata in a list (a group of comma-separated values contained in brackets).

Because MediaInfo organizes technical metadata by track, we had to collect each piece of metadata from the appropriate track whether “General,” “Video,"" and “Audio”.
Before, we used a single `if` test to see if our loop was looking at the "General" track.
Now, we added additional tests with `elif` (or if).
If our loop wasn't looking at the "General" track, maybe it was looking at a "Video" or "Audio" track.

When we were looking at the one of these tracks, we were collecting data from that track into a list, either `general_data`, `video_data`, and `audio_data`.
And once our loop has looked at all of these tracks, we combined `general_data`, `video_data`, and `audio_data`.
In Python, we can make a single long list out of multiple smaller lists by using the `+`.
The new trifecta-combo list is appended to our master list of lists `all_file_data`.

It’s complicated, no doubt, but our goal should be clear: we not only do we want to gather MediaInfo data, but we want to do so in a way that creates a structured data set that can easily be exported to allow for further analysis.

The fields we gathered in the code above may or may not meet your needs.
You may need to gather less.
If so, delete those lines.
You may need to gather other pieces of metadata that MediaInfo collects.

In that case, you'll need to figure out what that field's name is in `pymediainfo`.
A good strategy for this, as for many questions, is to print out an entire `pymediainfo` report. So, for example, if we ask python to print out the stream attributes, we’ll receive the following:

~~~
media_info = MediaInfo.parse(media_list[0])
for track in media_info.tracks:
    print(track.track_type, track.to_data().keys())
~~~
{: .language-python}

~~~
General dict_keys(['track_type', 'count', 'count_of_stream_of_this_kind', 'kind_of_stream', 'other_kind_of_stream', 'stream_identifier', 'count_of_video_streams', 'count_of_audio_streams', 'video_format_list', 'video_format_withhint_list', 'codecs_video', 'video_language_list', 'audio_format_list', 'audio_format_withhint_list', 'audio_codecs', 'audio_language_list', 'complete_name', 'folder_name', 'file_name_extension', 'file_name', 'file_extension', 'format', 'other_format', 'format_extensions_usually_used', 'commercial_name', 'format_profile', 'internet_media_type', 'codec_id', 'other_codec_id', 'codec_id_url', 'codecid_version', 'codecid_compatible', 'file_size', 'other_file_size', 'duration', 'other_duration', 'overall_bit_rate_mode', 'other_overall_bit_rate_mode', 'overall_bit_rate', 'other_overall_bit_rate', 'frame_rate', 'other_frame_rate', 'frame_count', 'stream_size', 'other_stream_size', 'proportion_of_this_stream', 'headersize', 'datasize', 'footersize', 'isstreamable', 'file_last_modification_date', 'file_last_modification_date__local', 'writing_application', 'other_writing_application'])
Video dict_keys(['track_type', 'count', 'count_of_stream_of_this_kind', 'kind_of_stream', 'other_kind_of_stream', 'stream_identifier', 'streamorder', 'track_id', 'other_track_id', 'format', 'other_format', 'commercial_name', 'codec_id', 'codec_id_hint', 'duration', 'other_duration', 'bit_rate_mode', 'other_bit_rate_mode', 'bit_rate', 'other_bit_rate', 'width', 'other_width', 'clean_aperture_width', 'other_clean_aperture_width', 'height', 'other_height', 'clean_aperture_height', 'other_clean_aperture_height', 'pixel_aspect_ratio', 'clean_aperture_pixel_aspect_ratio', 'display_aspect_ratio', 'other_display_aspect_ratio', 'clean_aperture_display_aspect_ratio', 'other_clean_aperture_display_aspect_ratio', 'rotation', 'frame_rate_mode', 'other_frame_rate_mode', 'frame_rate', 'other_frame_rate', 'framerate_num', 'framerate_den', 'frame_count', 'standard', 'color_space', 'chroma_subsampling', 'other_chroma_subsampling', 'bit_depth', 'other_bit_depth', 'scan_type', 'other_scan_type', 'compression_mode', 'other_compression_mode', 'bits__pixel_frame', 'stream_size', 'other_stream_size', 'proportion_of_this_stream', 'language', 'other_language'])
Audio dict_keys(['track_type', 'count', 'count_of_stream_of_this_kind', 'kind_of_stream', 'other_kind_of_stream', 'stream_identifier', 'streamorder', 'track_id', 'other_track_id', 'format', 'other_format', 'commercial_name', 'format_settings', 'format_settings__endianness', 'format_settings__sign', 'codec_id', 'codec_id_url', 'duration', 'other_duration', 'bit_rate_mode', 'other_bit_rate_mode', 'bit_rate', 'other_bit_rate', 'channel_s', 'other_channel_s', 'channel_positions', 'channel_layout', 'sampling_rate', 'other_sampling_rate', 'samples_count', 'bit_depth', 'other_bit_depth', 'stream_size', 'other_stream_size', 'proportion_of_this_stream', 'language', 'other_language', 'default', 'other_default', 'alternate_group', 'other_alternate_group'])
~~~
{: .output}

It’s yet another long and wordy list, but again, knowing how pymediainfo organizes information is an important first step in figuring out how to get specific information out of it.

> ## What are possible flaws in this approach?
> Think about your experience with AV files and compare that to the data we generated for each file.
> What types of files would be represented incorrectly with our code?
> > Files with multiple video or audio tracks.
> > Files missing some of the fields we asking for.
> {: .solution}
{: .challenge}

### Trapping errors/exceptions and anticipating diverse collections

The eagle-eyed among you may be wondering: this code gives us the results we’re after—when surveying a controlled set of PAL and NTSC Quicktime files—but what about when we broaden the pool and start to survey media files of different types and different specs?
Video files that don’t contain audio tracks, audio files that don’t contain video tracks, ISOs of DVDs that contain neither—all of these things will reveal that the code we’ve written is brittle, liable to break down when encountering a less-anticipated situation.

To avoid potential problems, we’ll need to enter the realm of error handling; specifically, we’ll need to use what’s called a `try` and `except` block to skip over files that don’t match our “ideal” state.

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
        try:
            if track.track_type == "Video":
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
            else:
                video_data = [None, None, None, None, None, None, None, None, None, None, None, None]
        except:
            continue
        try:
            if track.track_type == "Audio":
                audio_data = [
                    track.format,
                    track.bit_rate,
                    track.channel_s,
                    track.bit_depth,
                    track.sampling_rate]
            else:
                audio_data = [None, None, None, None, None]
        except:
            continue
    all_file_data.append(general_data + video_data + audio_data)
~~~
{: .language-python}

Here we can see a new set of assumptions being made: that all surveyed files will have some form of “General” MediaInfo information, but that they may or may not have additional “Video” and “Audio” streams.
With the use of `try` and `except` blocks (note the careful use of colon and indentation, similar to if/else statements), we’re able to build in a way to avoid potential problems.
When python comes across files that are missing some of these key elements, it will return None (a null data type of its own making) rather than breaking down.

By making this effort, we ensure two things:
1. that our code will continue to function despite the strange files we throw at it,
2. that our MediaInfo data will be consistent from file to file and ready for export.

### Using the python CSV module

If we hope to analyze our collection of media files on a deeper level, beginning to make larger overall comparisons, or if we simply want to transmit that information into a database or some other type of information system, we’ll first need to export that data in a structured form. 

CSV, or comma separated values, is one of many tabular data structures that python supports, but it will serve our purposes nicely as it’s human and machine readable, lightweight, and has fairly extensive tool support. But as with other modules that we’ve encountered, we’ll need to (1) use an import statement to incorporate csv functionality into our code (note that the csv module does not require a separate pip installation), and (2) learn key components of the csv syntax in order to use it properly.

Because we made the effort to create a structured yet accommodating MediaInfo data set within our script (the “list of lists” all_file_data), which, to remind ourselves, looks like this:

~~~
['test21', 'mov', 'MPEG-4', 36773291, 1318, 'v210', 223724851, 720, 486, '1.333', '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'PCM', 2304000, 2, 24, 48000]
['test22', 'mov', 'MPEG-4', 49968309, 1773, 'v210', 223724851, 720, 486, '1.333', '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'PCM', 2304000, 2, 24, 48000]
['test23', 'mov', 'MPEG-4', 26394991, 935, 'v210', 223724851, 720, 486, '1.333', '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'PCM', 2304000, 2, 24, 48000]
['test24', 'mov', 'MPEG-4', 11312141, 401, 'v210', 223724851, 720, 486, '1.333', '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'PCM', 2304000, 2, 24, 48000]
['test25', 'mov', 'MPEG-4', 65053469, 2311, 'v210', 223724851, 720, 486, '1.333', '0.900', '29.970', 'NTSC', 'YUV', '4:2:2', 10, 'Lossless', 'PCM', 2304000, 2, 24, 48000]
~~~

We can easily envision how this will port over to something that would look good in Google Sheets or Microsoft Excel.  But to get from inside our jupyter notebook or terminal/cmd window to a separate standalone file, we’ll need to “open” a new CSV file object and “write” data to it.

There are, as usual, a number of ways to perform this action, but one common approach is to use a “with open” combination, which takes advantage of invisible try/except logic to ensure that the CSV file being written to will close despite any exceptions or issues encountered. After “opening” the CSV file, we’ll use the csv.writer and csv.writerow functions to transform each list of MediaInfo file information into its own separate row of our resulting CSV file.

But before we do any of that, we’ll first want to give our CSV file a header row with our attributes in named order (this will give us the ability to work with this data in an easier way down the road):

~~~
with open('/Users/benjaminturkus/Desktop/kittydata.csv', 'w') as f:
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


{% include links.md %}

> ## List Comprehensions
> In this workshop we use the following pattern a lot:
> ~~~
> results = []
> for item in list_of_items:
>   result = do_something(item)
>   results.append(result)
> ~~~
> {: .language-python}
>
> Python has a more succint way to write small loops like this, the list comprehension.
> ~~~
> results = [do_something(item) for item in list_of_items]
> ~~~
> {: .language-python}
> (see! Python isn't always super verbose!)
{: .callout}
