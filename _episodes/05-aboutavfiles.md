---
title: "Finding Out About Files"
teaching: 20
exercises: 10
questions:
- "How can you use Python and pymediainfo to learn more about your media files?"
Objectives:
- “Use pymediainfo to collect additional (and multiple) pieces of technical metadata”
- “Use the csv module to export metadata to a file”
keypoints:
- "Domain specific functions will often require custom tools"
---


{% include links.md %}

> ## List Comprehensions
> In this workshop we use the following pattern a lot.
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