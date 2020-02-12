---
layout: post
title:  "My Python Environment"
date:   2020-02-12 21:22:37 +0200
categories: rants python
---

The most common way people go about setting up a Python environment is to do something like this:

```console
$ sudo apt install python3 python3-pip    # installs pip3
$ sudo pip3 install --upgrade pip         # upgrade pip to latest version
```

Congrats, now you're stuck with whatever version ships with the distro. I think we can do better!


# `pyenv`

My recommendation now, is to use `pyenv` with which you can manage multiple versions, virtualenvs through a nice CLI

### Uninstall `pip`/`pip3`
Before proceeding, I'd suggest uninstalling `pip` using `pip uninstall pip` and `sudo apt remove python3-pip`

## Installation

[Using the `pyenv` installer project](https://github.com/pyenv/pyenv-installer)

```console
$ sudo apt install libssl-dev libsqlite-dev libreadline-dev libbz2-dev    # I've observed failures without these libs
$ curl https://pyenv.run | bash
...
WARNING: seems you still have not added 'pyenv' to the load path.

# Load pyenv automatically by adding
# the following to ~/.bashrc:

export PATH="/home/taimoor/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

This should set up `pyenv` for you. Read the instructions printed at the end and follow them! Add the three things to your `~/.bashrc` or `~/.zshrc` or whatever.

## Usage (summarized)

Install Python 3.8.1:

```console
$ pyenv install 3.8.1
```

### Virtualenv
Create a virtualenv
```console
pyenv virtualenv
```