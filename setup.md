---
title: Setup
---

# Setting up your Computer for the Workshop

## Introduction
Every computer is different and every computer user sets up their computer differently. To create a shared environment for all of us, this workshop uses package managers and virtual environments to install the tools we'll be using without affecting other parts of your computer.

A _package manager_ is a program that allows you to install custom software that not that's included in your operating system out of the box.
Most package managers are accessed via the command line and are specific to an operating system.

A _virtual environment_ is a tool to create a custom installation of software that does not affect the rest of the computer.
For example, you may need to have a specific version of Python installed for work purposes, but want to test out the latest release. To do this, you can create a virtual environment, and install the latest version of Python in that environment.
When you use your computer normally, it does not know that the virtual environment exists.
When you tell your computer to use that environment, it will use the version of Python installed there.
If you decide to delete the virtual environment, you will not have touched your original setup.

It’s important to take a little time to set up your computer before this workshop. It will take about 30-60 minutes with a fast Internet connection. If you run into errors, please contact your workshop coordinators.

## Objectives
You will be able to:
* Use a package manager like Homebrew (masOS/Linux) or Chocolatey (Windows)
* Use pyenv, the simple Python version management system
* Use pipenv, a Python virtual environment manager
* Add packages to yopur Python environment
* Launch Jupyter Lab

## Computer Prerequisites
You’re going to need a computer. It should be running a recent (last 4-5 years) version of MacOS, Windows or Linux, and ideally it should have 8Gb of RAM and at least 20Gb free hard drive space. You'll need to be able to install software to this computer (administrative privileges).

Assuming you have a computer that meets the requirements, let’s start by getting pyenv installed.

## Installing Homebrew or Scoop

The easiest way to get create a self-contained working environment for Python and Jupyter Lab is to install pyenv, a version management system which allows you to toggle between multiple versions of Python. To do this, we'll first need to install a package manager. We also want to make sure that we don't disrupt any current installations of Python on your computer.

Let’s go through the installation instructions for the two common package managers - Homebrew (macOS/Linux) and Scoop (Windows).

### Homebrew (macOS/Linux)
1. Open a macOS Terminal window or Linux shell prompt.
2. Go [here](https://brew.sh/) and copy the long line of code under the heading "Installation."  
  ```
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
  ```
3. Paste that into the macOS Terminal or Linux shell prompt and hit Return when prompted. You may be prompted for a password. Enter your login password at this point.
4. Check your installation.  
  ```
  brew --version
  ```

### Chocolatey (Windows)

1. Open a Powershell Administrator session by typing `powershell` into Start Menu, right-clicking on the icon, and choosing Administrator.
2. Check that you have the necessary security permissions to install chocolatery.  
  ```
  Get-ExecutionPolicy
  ```
  * If it returns Restricted, then run `Set-ExecutionPolicy Bypass -Scope Process`.
4. Copy the following from the [Chocolatey instructions](https://chocolatey.org/install).  
  ```
  Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
  ```
5. Paste that into the Powershell window and hit Return when prompted.
6. Check your installation.  
  ```
  choco --version
  ```

## Installing pyenv

Now that you've got a package manager up and running, it's time to get pyenv installed

### macOS/Linux

You can use the same Terminal/shell session from the previous step or open a new window.

1. Check your current system version of Python.  
  ```
  python --version
  ```
2. Install pyenv.  
  ```
  brew install pyenv
  ```
3. Update your Terminal/shell profile to use pyenv. This will depend if your Terminal uses bash or zsh.
  ```
  #If your Terminal session says zsh in the title bar, run the following.  

  echo 'eval "$(pyenv init)"' >> ~/.zshrc
  source ~/.zshrc
  ```
  ```
  #If your Terminal session says bash in the title bar.  

  echo 'eval "$(pyenv init)"' >> ~/.bash_profile
  source ~/.bash_profile
  ```
4. Check that pyenv was installed.
  ```
  pyenv versions
  ```
5. Check your current python version
  ```
  python --version
  ```

### Windows

You can use the same Powershell session from the previous step or open a new window.

1. Check if you have a version of Python installed. 
  ```
  python --version
  ```
  ```
  #If you don't anything installed, you will see red text. This is fine.
  ```
  ```
  #If you do have a version installed, you will see a version number like 3.5.4. Save this number.
  # Then check where this version is installed. Save the filepath from this command.
  
  Get-Command python | Select-Object -ExpandProperty Definition
  ```
2. Install pyenv
  ```
  choco install pyenv
  ```
3. Check that pyenv was installed.
  ```
  pyenv versions
  ```
4. If you had a version of python install, register it with pyenv. In the following command, replace `#.#.#` with the number from 1.1 and `path\to\version` with the output from 1.2
  ```
  cmd /c mklink /D %USERPROFILE%\.pyenv\pyenv-win\versions\#.#.# path\to\versions
  pyenv global #.#.#
  ```


## Creating a workshop folder and installing Python 3.9.4

The value of working with pyenv is two-fold: (1) it reduces the headaches associated with multiple, possibly conflicting Python installations, and grants us a greater degree of control; and (2) it allows us to easily remove  installations at the close of the workshop, leaving your computer in an untouched, original state.

We'll be installing Python 3.9.4 and various other programs/packages in one specific folder on your Desktop, so let's go ahead and make that now.

1. Create a folder called `workshoppython` on your Desktop now.
2. Open terminal or command prompt.
3. Navigate to the directory you created
  ```
  cd ~/Desktop/workshoppython
  ```
4. Run the following command to install Python 3.9.4
  ```
  pyenv install 3.9.4
  ```
5. Set 3.9.4 as the default version for this folder.
  ```
  pyenv local 3.9.4
  ```

## Installing other Jupyter Lab and other AV-centric programs

We will use a virtual environment to install a set of Python just for this workshop.

1. Open terminal or command prompt.
2. Navigate to the directory you created
  ```
  cd ~/Desktop/workshoppython
  ```
3. Install pipenv
  ```
  python -m pip install pipenv
  ```
4. Install the Python modules we'll use in this workshop.
  ```
  pipenv jupyterlab
  pipenv pymediainfo
  pipenv matplotlib
  pipenv hurry.filesize
  pipenv pandas
  ```

## Testing your Installation

1. Open terminal or command prompt.
2. Navigate to the directory you created
  ```
  cd ~/Desktop/workshoppython
  ```
3. Run Jupyter Lab, the Python interpreter we use for the workshop.
  ```
  pipenv shell jupyter lab
  ```

## Install ffmpeg

We will use ffmpeg to transcode videos during the workshop. If you don't have it installed, complete the following.

### macOS/Linux

1. Open terminal or command prompt.
2. Install ffmpeg.
```
brew install ffmpeg
```
3. Check your installation.
```
ffmpeg -version
```

### Windows

1. Open terminal or command prompt.
2. Install ffmpeg.
```
choco install ffmpeg
```
3. Check your installation.
```
ffmpeg -version
```

## Downloading the Workshop Files

Download the pyforav.zip from [Google Drive](https://drive.google.com/drive/u/0/folders/1QqII7T8oRvwAVdBmZjcNh5DlDc93tR6s), unzip it, and move the `pyforav` folder to your Desktop.

## Summary

Congratulations! If you've gotten this far and everything's worked, you've a great baseline setup for the workshop!

## Uninstalling everything

### macOS/Linux

1. Delete the `workshoppython` and `pyforav` folders from your Desktop.
2. Open terminal or command prompt.
4. Uninstall pyenv.
```
brew uninstall pyenv
```
4. Check your current python version. It should report 2.7.# or your previous 3.#.# version.
```
python --version
```

### Windows

1. Delete the `workshoppython` and `pyforav` folders from your Desktop.
2. Open terminal or command prompt.
3. Install pyenv.
```
choco uninstall pyenv
```
4. Check your current python version. It should report your previous #.#.# version or an error for no version.
```
python --version
```


{% include links.md %}
