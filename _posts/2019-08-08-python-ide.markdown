---
layout: post
title:  "Python IDE"
date:   2019-08-08 08:00:00 +0100
categories: walkthrough
---

# Atom

Atom is a hackable text editor. It has a lot of community maintained packages.
It's easy to install, fast and easy to learn, and has a really high
customization level.

Atom can be used as a text editor/IDE for almost every language.

Atom has a standard installed plugin for git and GitHub.

## Download and install

Atom can be downloaded from [the Atom website](https://atom.io/).

## Installing python

You should install the wanted python version, and check if this is the default
one. For the IDE to work completely this is needed to be installed:
```bash
python -m pip install 'python-language-server[all]'
```

## Install interesting packages

After the Atom installation, packages can be added.
This can be done by going to Edit > Preferences. This opens the Settings menu.
Under Install, you can select packages to install.
Under Packages, you can manage the packages.

### atom-ide-ui

This package adds language support for multiple programming languages
including python and C.

### ide-python

This package makes a real python IDE of Atom. The package contains many other
packages to format coding, detecting errors, enable completion, ...

### linter-mypy

This package displays warnings for PEP484.

### docblock-Python

This package will help documenting the code.

### kite

This package give auto-completion, but also instant documentation.

### python-autopep8

This package can rearrange your code following PEP8.

### minimap

This package give you a fancy little map to find your way through the code.

### atom-file-icons

This package will add icons in front of filenames, to help you identify the
file types

### autocomplete-python

This package adds auto-completion for python.
