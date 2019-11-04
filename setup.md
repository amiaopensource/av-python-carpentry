---
title: Setup
---

# Setting up your Computer for the Workshop

## Introduction
Every computer is different and every computer user sets up their computer differently. To create a shared environment for all of us, this workshop uses Anaconda to install python, ffmpeg, and the other tools we'll be using, without affecting other parts of your computer.

It’s important to take a little time to set up your computer before this workshop. It will take at 30-60 minutes with a fast Internet connection. If you run into errors, please contact your workshop coordinators.

If you already have Anaconda installed, jump down to the cloning, virtual environment setup and testing towards the bottom of the page.

## Objectives
You will be able to:
* Install Anaconda
* Launch Jupyter Lab
* Add packages to an Anaconda Environment

## Computer Prerequisites
You’re going to need a laptop. It should be running a recent (last 4-5 years) version of MacOS, Windows or Linux, and ideally it should have 8Gb of RAM and at least 20Gb free hard drive space. You'll need to be able to install software to this computer.

Assuming you have a computer that meets the requirements, let’s start by getting Anaconda installed.

## Installing Python and Jupyter Lab via Anaconda

The easiest way to get set up with Python and Jupyter Lab so you can start coding is to install the Anaconda distribution. Let’s go through the install instructions  for the two most common operating systems - Windows and MacOS.

#### Windows
Go [here](https://www.anaconda.com/download/#windows) and click on the "download" button for the Python 3.x (currently 3.7) version of Anaconda.

A window may pop up asking if you want to give Anaconda your information in return for a cheat sheet - you do not need to do so unless you want to.

You should see in the bottom of your browser window that a .exe file is being downloaded. When it finishes downloading, click on the arrow to the right of the name of the file in the bottom left corner of your browser, and select "open".

![screen-16](http://curriculum-content.s3.amazonaws.com/data-science/screen-16.png)

If you don’t see the file in your browser, you can also just open up Windows Explorer, navigate to the "Downloads" directory and double click on the Anaconda file in the list to open it.

![screen-17](http://curriculum-content.s3.amazonaws.com/data-science/screen-17.png)

That will open the Anaconda installer which will install the software for you on your computer.

![screen-18](http://curriculum-content.s3.amazonaws.com/data-science/screen-18.png)

Click "next", then "I agree" to accept the license, and you can install for "just me", clicking next. Then select the destination folder (the default should work for most people).

![screen-19](http://curriculum-content.s3.amazonaws.com/data-science/screen-19.png)

On the next screen, click "install".

This step may take up to 30 minutes.

![screen-21](http://curriculum-content.s3.amazonaws.com/data-science/screen-21.png)

Once the installation is complete,

![screen-22](http://curriculum-content.s3.amazonaws.com/data-science/screen-22.png)

Hit "next". Skip the Visual Code Studio installation

![screen-23](http://curriculum-content.s3.amazonaws.com/data-science/screen-23.png)

And then finally click "finish".

![screen-24](http://curriculum-content.s3.amazonaws.com/data-science/screen-24.png)

It’ll open up a browser window which you can just close down.

![screen-25](http://curriculum-content.s3.amazonaws.com/data-science/screen-25.png)

And that’s the process of installing Anaconda. The next step is to test your installation.

#### Mac
Go [here](https://www.anaconda.com/download/#macos) and click on the "download" button for the Python 3.x (currently 3.7) version of Anaconda.

![screen-26](http://curriculum-content.s3.amazonaws.com/data-science/screen-26.png)

You should see in the bottom of your browser window that a .pkg file is being downloaded. When it finishes downloading, click on the arrow to the right of the name of the file in the bottom left corner of your browser, and select "open".

![screen-27](http://curriculum-content.s3.amazonaws.com/data-science/screen-27.png)

If you don’t see the file in your browser, you can also just open up the finder, navigate to the "Downloads" directory and double click on the Anaconda file in the list to open it.

![screen-28](http://curriculum-content.s3.amazonaws.com/data-science/screen-28.png)

You’ll be informed that the package will run a program to see whether the software can be installed. Click "continue".

![screen-29](http://curriculum-content.s3.amazonaws.com/data-science/screen-29.png)

You’ll then see a wizard that will run you through the installation process. Click continue on the first screen.

![screen-30](http://curriculum-content.s3.amazonaws.com/data-science/screen-30.png)

Then look at the read me, and click "continue" again.

![screen-31](http://curriculum-content.s3.amazonaws.com/data-science/screen-31.png)

You’ll then need to accept the license. Start by clicking "continue"

![screen-32](http://curriculum-content.s3.amazonaws.com/data-science/screen-32.png)

And then click on "agree" in the dialog that comes up and asks you to accept the license.

![screen-33](http://curriculum-content.s3.amazonaws.com/data-science/screen-33.png)

Then click on "install" to install the software.

![screen-34](http://curriculum-content.s3.amazonaws.com/data-science/screen-34.png)

And you’ll have to enter an administrative username and password for your computer to finally install the software.

![screen-35](http://curriculum-content.s3.amazonaws.com/data-science/screen-35.png)

The wizard will let you know next that it’s preparing the install, and then it’ll take a couple of minutes to install all of the necessary software.

![screen-36](http://curriculum-content.s3.amazonaws.com/data-science/screen-36.png)

You’ll be given the option to install Microsoft VSCode. For now, you can skip that option by clicking "continue".

![screen-37](http://curriculum-content.s3.amazonaws.com/data-science/screen-37.png)

You should then see a final window informing you that the software was installed successfully. Click close to finish the installation.

![screen-38](http://curriculum-content.s3.amazonaws.com/data-science/screen-38.png)

If you’re asked whether you’d like to move the installer to trash, click the "Move to trash" button.

![screen-39](http://curriculum-content.s3.amazonaws.com/data-science/screen-39.png)

## Testing your installation

To test your installation, on Windows, click on Start and then Anaconda Navigator in the program list (or search for Anaconda in the search bar and select Anaconda Navigator). On a Mac, open up the finder, and in the Applications folder, double click on Anaconda-Navigator.

From now on, screenshots will be from a Mac, but we’ll highlight any material differences in the experience between the OS’s.

The Anaconda Navigator is one of the ways you’ll be able to run Jupyter Lab. Click on the "launch" button in the Jupyter Lab tile.

![screen-40](http://curriculum-content.s3.amazonaws.com/data-science/screen-40.png)

On a mac you’ll see a terminal window pop up.

![screen-41](http://curriculum-content.s3.amazonaws.com/data-science/screen-41.png)

On both Windows and a Mac you’ll see a window in your web browser that allows you to open existing Jupyter notebooks or create a new one.

![screen-42](http://curriculum-content.s3.amazonaws.com/data-science/screen-42.png)

Click on the "Python 3" tile under Notebook in the tab.

![screen-43](http://curriculum-content.s3.amazonaws.com/data-science/screen-44.png)

When you do, you’ll see a new notebook in your browser window that looks something like this:

![screen-44](http://curriculum-content.s3.amazonaws.com/data-science/screen-43.png)

To make sure it’s working, click in the cell and type the following:

```
import sys
print(sys.version)
```

Then hold down the shift key and hit enter to run the code in the cell. You should see an output something like this.

![screen-45](http://curriculum-content.s3.amazonaws.com/data-science/screen-45.png)

Don’t worry if the version number or date is slightly different. If you get a similar output (something that isn’t an error message), congratulations! You’ve got Anaconda, Python and the Jupyter notebook installed successfully!

To shut down Jupyter notebook, just close the tabs in your browser containing the notebook and the list of noteboks. On a mac you should also click on the terminal window and hit "ctrl-C" to close the notebook.

![screen-46](http://curriculum-content.s3.amazonaws.com/data-science/screen-46.png)

You’ll then have to hit "y" and return to confirm that you want to close down Jupyter notebook.

## Setting up the Conda Environment

In Anaconda Navigator, click on the Environment tab on left side of the window.

Click on the Play button next to `base (root)` and select 'Open Terminal'.

Then type (or better still, cut and paste) `conda install -c conda-forge ffmpeg`

When the command console asks to proceed, type `y` then press Enter.

## Summary

Congratulations! If you've gotten this far and everything has worked, you have a great baseline setup for the workshop!


{% include links.md %}
