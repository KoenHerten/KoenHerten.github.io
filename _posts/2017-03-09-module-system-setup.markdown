---
layout: post
title:  "Enviromental Module System setup"
date:   2017-03-08 13:50:00 +0100
categories: walkthrough
---

#Environment Modules

The Environment Modules package provides for the dynamic modification of a user’s environment via modulefiles.

Each modulefile contains the information needed to configure the shell for an application. Once the Modules package is initialized, the environment can be modified on a per-module basis using the module command which interprets modulefiles. Typically modulefiles instruct the module command to alter or set shell environment variables such as PATH, etc. or load aliases to installed tools. Modulefiles may be shared by many users on a system and users may have their own collection to supplement or replace the shared modulefiles.

Modules can be loaded and unloaded dynamically and atomically, in an clean fashion. Modules are useful in managing different versions of applications. Modules can also be bundled into metamodules that will load an entire suite of different applications.

#Setup on Ubuntu

First install some dependencies:
```bash
apt-get install tcl tcl8.4-dev
```

Download the needed tool:
```bash
wget http://downloads.sourceforge.net/project/modules/Modules/modules-3.2.9/modules-3.2.9c.tar.gz
tar xvvf modules-3.2.9c.tar.gz
```

Create the directories for tool installation (sudo needed). This should be done in a directory which is not affected by an OS upgrade/update. 
On a local PC, at the installation of Ubuntu it is wise to split the OS installation (/) from the data location (/home). In this setup the modules could be installed in /home
```bash
sudo mkdir /home/packages
sudo mkdir /home/modules
```
Now the installation of module:
```bash
cd modules-3.2.9
./configure --with-module-path=/home/modules
make
#sudo may be needed for make install
make install
```

Configure the install directory for the modules: command all lines exept the /home/modules
```bash
vim /usr/local/Modules/3.2.9/init/.modulespath
```

Make sure the module software can be loaded when the shell is loaded:
```bash
cp etc/global/profile.modules /etc/profile.d/modules.sh
```

In your installation directory you will need to link the installed modules version as a default:
```bash
cd /usr/local/Modules
ln -sfn 3.2.9 default
```

As a last edit, you will need to add the module to your bashrc script. This will have to be done for every user, just execute this command:
```bash
/usr/local/Modules/default/bin/add.modules
```

#Installing applications in the module

Installing applications from scratch will become easy and trivial:
Example: installing gcc 4.6.2.

Create a directory with TOOLNAME/VERSION
```bash
cd /home/packages
mkdir gcc/4.6.2
```

Configure the tool, and build it:
```bash
./configure --prefix=/home/packages/gcc/4.6.2
make
make install
```
Now the gcc tool is installed. A module file is needed to be able to load this tool as a module.
Note: for tools that are not build like this, you can create bash scripts, and store these in the bin file. All scripts/software available in the bin file will be loaded by the module.

#Creating module files

First create a directory for the installed tool (only toolname):
```bash
mkdir /home/modules/gcc
```

Create a file with the tool version:
```bash
vim /home/modules/gcc/4.6.2
```

Configure the file like this:
```bash
#%Module1.0
proc ModulesHelp { } {
global dotversion
 
puts stderr "\tGCC 4.6.2 (gcc, g++, gfortran)"
}
 
module-whatis "GCC 4.6.2 (gcc, g++, gfortran)"
conflict gcc
prepend-path PATH /packages/gcc/4.6.2/bin
prepend-path LD_LIBRARY_PATH /packages/gcc/4.6.2/lib64
prepend-path LIBRARY_PATH /packages/gcc/4.6.2/lib64
prepend-path MANPATH /packages/gcc/4.6.2/man
setenv CC gcc
setenv CXX g++
setenv FC gfortran
setenv F77 gfortran
setenv F90 gfortran
```
You can also use this script to generate module files: https://github.com/GenomicsCoreLeuven/vsc_ngs_workshop/blob/master/advanced/generate_modulefile

#Using modules:

Using modules is not that hard. There are only a few commands to remember:

```bash
#give a list of available modules
module av
#load a module
module load modulename
module load modulename/moduleversion
#show a list of all loaded modules
module list
#unload a module
module unload modulename
#unload all modules at once
module purge
#show the help
module help
```


