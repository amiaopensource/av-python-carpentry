---
title: "Creating a Script"
teaching: 15
exercises: 15
questions:
- "How do you go from experimentation to production?"
objectives:
- "Use argparse"
- "Transform Jupyter notebook code into a script"
keypoints:
- "Once you’re happy with automation, a script makes it easier to use"
---


### Trapping errors/exceptions and anticipating diverse collections

The eagle-eyed among you might be beginning to wonder: this code gives us the results we’re after—when surveying a controlled set of PAL and NTSC Quicktime files—but what about when we broaden the pool, and start to survey media files of different types and different specs?
Video files that don’t contain audio tracks, audio files that don’t contain video tracks, ISOs of DVDs that contain neither—all of these things will reveal that the code we’ve written is brittle, liable to break down when encountering a less-anticipated situation.

To avoid potential problems, we’ll need to enter the realm of error handling, and more specifically, we’ll need to use what’s called a `try` and `except` block to skip over files that don’t match our “ideal” state.

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