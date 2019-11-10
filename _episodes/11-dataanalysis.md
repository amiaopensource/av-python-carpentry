---
title: "Analysis and Visualization"
teaching: 20
exercises: 10
questions:
- "How can I use Python/pandas to analyze the data that I've gathered about my media collection?"
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

But Alice has come to realize that while doing the work is one thing, being able to analyze the data surrounding her efforts, and being able to communicate that information upstream to senior management at New Amsterdam, is perhaps as important as creating smart, automated workflows to begin with.


> ## pandas: the Google Sheets/Excel replacement you didn't know you were missing
>
> pandas is yet another python add-on, this time an extraordinarily powerful data analysis engine that comes from the world of finance, specifically quantitiave analytics. 
> While it may seem intimidating, pandas is remarkably user friendly and intuitive.
> pandas is excellent at:
> * normalizing data from diverse sources
> * performing calculations on rows or colmns
> * concatenating or joining or grouping by category
> * visualization 
> And pandas is really excellent at: reading/writing to a range of metadata formats, including Excel,CSV, JSON, and more!
> 
{: .callout}

## Alice's new adventure: creating a scatterplot of compression ratios 



