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
keypoints:
- "Python is powerful"
- "Python can be used to find, move, survey, and transcode AV files"
---

## Introduction: Why should I use Python?

At a high level, computers do four things:
* run programs
* store data
* communicate with each other, and
* interact with us

Computers can interact with us in many different ways, such as through a keyboard and mouse, touch screen interfaces, or using speech recognition systems. While touch and voice interfaces are becoming more commonplace, most interaction is still done using traditional screens, mice, touchpads and keyboards.

The graphical user interface (GUI) is the most widely used way to interact with personal computers. We give instructions (to run a program, to copy a file, to create a new folder/directory) with the convenience of a few mouse clicks. This way of interacting with a computer is intuitive and very easy to learn. But this way of giving instructions to a computer scales very poorly if we are to give a large stream of instructions even if they are similar or identical. For example, if we wanted to create 100 MP4’s from 100 preservation videos stored in 100 directories, it would be a challenge.

This is where we take advantage of the Python - a programming language to make such repetitive tasks consistent and automated. It can take a single instruction and repeat it as is or with some modification as many times as we want. The script for the task in the example above can be written in a few minutes.

> ## Bash and Python
>
> Of course Python isn’t the only possibility.
> Bash (aka Terminal, Shell, Command Line) is another tool you can use.
> Both are good options.
> In fact, they complement each other.
> By the end of this workshop we will both send Bash commands from Python and create Python scripts to run in Bash.
>
> If you are already familiar with Bash, that’s great.
> Hopefully, we can show you some ways to augment what you can do.
>
> If you are not familiar with Bash, we will provide an introduction when we get to those sections
{: .callout}

## What is Python
Python is an interpreted, high-level, general-purpose programming language.(Wiki) Breaking down the definition in reverse order:
* general-purpose - not designed for a specific computer, industry, or topic
* high-level - tries to be more human-friendly than computer-friendly
* interpreted - uses a program called an interpreter to turn human-friendly instructions into code for a computer

As long as you have a Python interpreter, you can run most Python code.

> ## Python 2 and Python 3
> If you have learned Python in the past, you might have learned version 2.
> The Python community has been developing and improving version 3 since 2008, and is ending support for Python 2 on January 1, 2020.
> A lot of the syntax and functions are identical or similar between the two versions.
>
> This workshop uses version 3, and we encourage you to use version 3 as well.
{: .callout}

## Writing Python
Many programs can run a Python interpreter. We will be using Jupyter. Using Jupyter notebooks we can write and edit Python code more like word processor than a traditional integrate development environment or text editor would. 

## Is Python hard to learn?
Writing code is a different model of interacting with a computer than a GUI, and that will take some effort - and some time - to learn. A GUI presents you with choices and you select one. With a Python, the choices are combinations of functions, arguments, and variables, more like words in a language than buttons on a screen. They are not presented to you so you must learn a few, like learning some vocabulary in a new language. But a small number of functions gets you a long way, and we’ll cover those essential few today.

## Alice's Pipeline: A Typical Problem
Alice Riley, an AV Archivist, has just started a new job at the New Amsterdam Public Library. On her first day, she is given a hard drives of old digitization projects and asked to:
1. create service copy MP4's for all of the files on the drive
2. share the duration of each video with a cataloger
3. make sure each file has metadata
4. preserve the files

After mounting the hard drives, Alice finds out that NAPL has completed quite a few digitization projects. She estimates that there are probably 400 video files. And no two projects seem exactly alike. Using a GUI program, she can transcode one video file in about an hour. Transcoding one-at-a-time would take 10 work weeks, if she does not make any mistakes. To report on the duration, she will need to use an open file dialog 400 times. She has heard some good things about lossless compression to help with long-term storage costs, but writing 400 `ffmpeg` commands does not sound very sustainable.

The next few lessons will explore how she could approach these tasks using Python. More specifically, they explain how she can use Python scripts to find files, investigate them, and run `ffmpeg` commands based on their contents.  As a bonus, once she has some scripts, she can reuse them and adapt them for new digitization projects or if more hard drives are found.

{% include links.md %}
