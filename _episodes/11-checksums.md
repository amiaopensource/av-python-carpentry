---
title: "Checksums"
teaching: 0
exercises: 0
questions:
- "How can I use Python to generate checksums?"
- "How can I use Python to validate bags?"
objectives:
- "Use hashlib to generate checksums"
- "Use bagit-python to validate bags"
keypoints:
- "Just because you can in Python, doesn't mean it's the most convenient"
---

## Fixity!

People keep asking Alice about her fixity practices. She knows that the files from the 3 projects have different fixity information. What can she Python about this

She wants to see what she can do with Python.

## Bags

All of the sub-folders of `120th anniversary` are bags.
If you've ever used the `Bagger` application, you might dread the idea of opening each bag, clicking the validate button, waiting for however long, seeing 'Valid!', and then repeating this loop.
But, there is a bagit module for Python.
Let's try it out.

First we install the module.

~~~
pip install bagit
~~~
{: .language-bash}

Then we import the module.
~~~
import bagit
~~~
{: .language-python}

The `bagit` module works as follows.
A folder can be loaded as a bag by sending it's path to `bagit.Bag()`.
Once loaded, the bag object has a function to validate itself.
For example:
~~~
bag = bagit.Bag(os.path.join('Desktop', 'pyforav', '120th_anniversary', '1143'))
bag.validate()
~~~
{: .language-python}

The result of `validate()` will be `True` or `False`.

> ## Automate bag checking
> Knowing how the `bagit` module works, create a `for` loop that checks all of the bags in the `120th anniversary` folder.
>
> First you will need to create a list of all the subfolders of `120th anniversary`
> > ## Solution
> > ~~~
> > bag_paths = glob.glob(os.path.join('Desktop', 'pyforav', '120th_anniversary', '*'))
> > 
> > for bag_path in bag_paths:
> >     bag = bagit.bag(bag_path)
> >     result = bag.validate()
> >     print(bag_path, result)
> > ~~~
> > {: .language-python}
> > For an extra challenge, how would you report these results as a CSV?
> {: .solution}
{: .challenge}

## Files without checksums

There are no checksums to check for the files in `on_demand`.
Instead, checksums can be created.

One way to do this would be to turn the folder into a bag.
The `bagit` module has a `make_bag()` function.

> ## Creating a bag
> How do you expect the `make_bag()` to work?
> > ## Solution
> > `bagit.make_bag()` requires a path as an argument.
> > Once run, `make_bag()` creates a bag in place, moving the contents of a folder into a `data` subdirectory, and writing manifests.
> > Warning: because the following code will move all the files in `on_demand`, you might want to copy this folder, so you can delete the bag you create.
> > ~~~
> > bagit.make_bag(os.path.join('Desktop', 'pyforav', 'on_demand'))
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

Another approach would be create checksum sidecar files like those in the `federal_grant` folder.
For that, we'll need to use the `hashlib` module.

~~~
import hashlib

BLOCKSIZE = 65536

ondemand_paths = glob.glob(os.path.join('Desktop', 'pyforav', 'on_demand', '*mov'))

hasher = hashlib.md5()
with open(ondemand_paths[0], 'rb') as f:
    buffer = f.read(BLOCKSIZE)
    while len(buffer) > 0:
        hasher.update(buffer)
        buffer = f.read(BLOCKSIZE)

sidecar_path = ondemand_paths[0].replace('mov', 'md5')
with open(sidecar_path, 'w') as f:
    f.write(hasher.hexdigest())
~~~
{: .language-python}

It can be a good exercise to read and understand what is happening in this code.
It's also a fair reaction to look at this code, look at another tool for creating checksum sidecar files, and choose the other tool.
For example:

~~~
md5 Desktop/pyforav/on_demand/middlerrequest_1669.mov > Desktop/pyforav/on_demand/middlerrequest_1669.md5
~~~
{: .language-bash}

This code creates nearly the same result without consideration about `BLOCKSIZE`, `buffer`, or reading files.

> ## When to Python
> One of the skills you will build as you use Python is figuring out when you don't want to use Python.
> 
> Maybe you have another tool that already does the job well-enough.
> Maybe the code examples you're finding look like they will take longer to understand than the time you'll save using them.
> 
> Python is a language.
> The more you use it, the more you will change how you engage with it.
{: .callout}


