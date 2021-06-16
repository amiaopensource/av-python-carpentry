---
title: Setup
---

# Setting up your Computer for the Workshop

## Introduction
Every computer is different and every computer user sets up their computer differently. To create a shared environment for all of us, this workshop uses Anaconda to install python, ffmpeg, and the other tools we'll be using, without affecting other parts of your computer.

It’s important to take a little time to set up your computer before this workshop. It will take about 30-60 minutes with a fast Internet connection. If you run into errors, please contact your workshop coordinators.

If you already have Anaconda installed, jump down to the cloning, virtual environment setup and testing towards the bottom of the page.

## Objectives
You will be able to:
* Use a package manager like Homebrew (masOS/Linux) or Scoop (Windows)
* Use pyenv, the simple Python version management system
* Add packages to yopur Python environment
* Launch Jupyter Lab

## Computer Prerequisites
You’re going to need a computer. It should be running a recent (last 4-5 years) version of MacOS, Windows or Linux, and ideally it should have 8Gb of RAM and at least 20Gb free hard drive space. You'll need to be able to install software to this computer (administrative privileges).

Assuming you have a computer that meets the requirements, let’s start by getting pyenv installed.

## Installing Homebrew or Scoop

The easiest way to get create a self-contained working environment for Python and Jupyter Lab is to install pyenv, a version management system which allows you to toggle between multiple versions of Python. But to get pyenv installed, we'll first need to install what's called a "package manager," a program that allows you to install custom software that not that;'s included in your operating system out fo the box.

Let’s go through the installation instructions for the two common package managers - Homebrew (macOS/Linux) and Scoop (Windows).

#### Homebrew
Go [here](https://brew.sh/) and copy the long line of code under the heading "Installation."

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Paste that in a macOS Terminal or Linux shell prompt and hit Return when prompted.

#### Scoop

TBD

## Installing pyenv

Now that you've got a package manager up and running, it's time to get pyenv installed

#### macOS/Linux

```
brew install pyenv
```
```
brew install pipenv
```

#### Windows

```
scoop install pyenv
```
```
scoop install pipenv
```

## Creating a workshop folder and installing Python 3.9.4

The value of working with pyenv is two-fold: (1) it reduces the headaches associated with multiple, possibly conflicting Python installations, and grants us a greater degree of control; and (2) it allows us to easily remove  installations at the close of the workshop, leaving your computer in an untouched, original state.

We'll be installing Python 3.9.4 and various other programs/packages in one specific folder on your Desktop, so let's go ahead and make that now.

Create a folder called `dpoeworkshop` on your Desktop now.

Open terminal or command prompt, and navigate to that directory (`cd Desktop/dpoeworkshop`)

Once there, run the following command to install Python 3.9.4

```
pyenv install 3.9.4
```
And to ensure that  3.9.4 will be your default version of Python, run the following:

```
pyenv local 3.9.4
```

## Installing other Jupyter Lab and other AV-centric programs

To install all of the other needed programs, we'll run a series of `pipenv` commands:

```
pipenv jupyter
pipenv pymediainfo
pipenv matplotlib
pipenv hurry.filesize
pipenv pandas
TBD
```
## Testing your Installation

TBD

## Downloading the Workshop Files

Download all of the files from this [Google Drive](https://drive.google.com/drive/u/0/folders/1QqII7T8oRvwAVdBmZjcNh5DlDc93tR6s), unzip them, and move them to your Desktop.

## Summary

Congratulations! If you've gotten this far and everything's worked, you've a great baseline setup for the workshop!

## Uninstalling everything

TBD


{% include links.md %}
