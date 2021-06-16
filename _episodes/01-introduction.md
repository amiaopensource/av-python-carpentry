---
title: "What is Python?"
teaching: 20
exercises: 10
questions:
- "What is Python?"
- "What is the command line?"
- "Why should I use it?"
objectives:
- "Explain the basics of Python"
- "Explain why and how to use the command line"
- "Introduce the sample data used for the workshop"
keypoints:
- "Learning to use Python is like learning a new language. It takes practice."
- "Python code is text that a computer interprets as instructions. You can write it with a text editor and run it separately. Or, you can use an integrated tool like Jupyter to write and run it in the same environment."
- "Python can be used to find, move, survey, and transcode AV files."
---

## Introduction: Why should I use Python?

At a high level, computers do four things:
* run programs
* store data
* communicate with each other
* interact with us

Computers can interact with us in a variety of ways, such as through a keyboard and mouse, touch screen interface, or speech recognition system. While touch and voice interfaces are becoming more common, most interaction is still performed by using traditional screens, mice, touchpads, and keyboards.

The graphical user interface (GUI) is the most popular way of interacting with personal computers. The user provides instructions (to run a program, copy a file, create a new folder/directory) with the convenience of a few mouse clicks. This way of interacting with a computer is intuitive and very easy to learn. But it scales very poorly if we need to perform an action repeatedly, or with slight permutations. For example, if we wanted to create 100 MP4s from 100 preservation master video files stored in 100 directories without having to navigate to each master file individually, it would be a distinct challenge.

This is where we can take advantage of the Python - a programming language designed to make repetitive tasks consistent and automated. Python can receive a single instruction and perform a set task, or, with slight modifications, perform a series of tasks in a repetitive manner as often as we would like. The script (set of instructions for Python) for the task described above (locating and generating a number of derivative video files) can be written in just a few minutes.

> ## Bash and Python
>
> Of course Python isn’t the only possibility.
> Bash (aka Terminal, Shell, Command Line) is another tool that you can use.
> Both are good options.
> In fact, they complement each other.
>
> By the end of this workshop, we will have initiated Bash commands from inside Python, and created Python scripts to run in Bash.
>
> If you are already familiar with Bash, that’s great.
> Hopefully, we can show you some ways to augment what you already know how to do.
>
> If you are not familiar with Bash, we will provide an introduction when we get to those sections.
{: .callout}

## What is Python
[Python](https://en.wikipedia.org/wiki/Python_(programming_language)) is an interpreted, high-level, general-purpose programming language.
Let's break this down in reverse order:
* general-purpose - not designed for a specific computer, industry, or topic
* high-level - tries to be more human-friendly than computer-friendly
* interpreted - uses a program called an interpreter to turn human-friendly instructions into code for a computer

As long as you have a Python interpreter, you can run most Python code.
And a really useful thing is that there are Python interpreters for Linux, MacOS, and Windows.

> ## Python 2 and Python 3
> If you have learned Python in the past, you might have learned version 2.
> The Python community has been developing and improving version 3 since 2008, and is ending support for Python 2 on January 1, 2020.
> A lot of the syntax and functions are identical or similar between the two versions.
>
> This workshop uses version 3, and we encourage you to use version 3 as well.
{: .callout}

## Writing Python
Python can be written and run using many pieces of software.
You may be familiar with tools like text editors (Atom, Sublime Text, Notepad++, etc) or integrated development environments (PyIDLE, VSCode, etc).

For this workshop, we will use an environment called Jupyter.
In Jupyter, we can write and run Python code in small chunks.
We can also rewrite and rerun code, similar to editing a document in a word processing program.
During this workshop, we will all make small mistakes and work together to find solutions.
Jupyter is a good environment for this kind of experimentation.

## Is Python hard to learn?
Writing code is a different model of interacting with a computer than a GUI.
It will take some effort - and time - to learn.
A GUI displays options, and you choose your action from the presented options.
With Python code, you compose the action, or set of actions, by combining functions, arguments, and variables.
If a GUI is like using an ATM, a scripting language is more like having a conversation with the bank teller.
The options are not presented to you directly, so you are responsible for learning what is possible.
But a small bit of knowledge can get you a long way, and we’ll cover some essential tidbits today.

## Alice's Pipeline: A Typical Problem
Alice Riley, an AV Archivist, has just started a new job at the New Amsterdam Public Library. On her first day, she is given a hard drive of old digitization projects and asked to:
1. create service copy MP4s for all of the files on the drive
2. share the duration of each video with a cataloger
3. preserve the files

After mounting the hard drive, Alice finds out that NAPL has completed a number of digitization projects.
She estimates that there are probably 400 video files.
And no two projects seem exactly alike.
Using a GUI program, she can transcode one video file in about an hour.
Transcoding one-at-a-time would take 10 work weeks, if she does not make any mistakes.
To report on the duration, she will need to use an open file dialog 400 times.
She has heard that lossless compression could help with long-term storage costs, but writing 400 `ffmpeg` commands does not sound very doable either.

The next few lessons will explore how she could approach these tasks using Python.
They will explain how she can use Python scripting to find files, investigate them, and run `ffmpeg` commands based on their contents. 
As a bonus, once she has Python scripts available to her, Alice will be able to reuse them or adapt them for new digitization projects.

> ## Exploring the Digitization Projects
> Take a few minutes to browse through the files you downloaded for this class.
> They're probably in a folder on your Desktop called `pyforav`.
>
> What kind of files are you finding?
> How would you describe their organization? 
> 
> > ## Solution
> > Most of the AV files appear to be MP4s and MOVs.
> > It's not clear what codecs are used inside those wrappers.
> > There are additional related to the videos.
> > 
> > Based on the file and folder names, file seem to be roughly organized by projects although there doesn't seem to be a standard method.
> > 
> {: .solution}
{: .challenge} 
