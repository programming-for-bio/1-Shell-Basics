# 1-Shell-Basics

Learning to use a bash terminal and the many unix commands that it makes available is key to becoming an efficient and competent programmer, regardless of which programming language you eventually advance to using.   

## I. The bash terminal  

Just like learning any other type of language, learning a programming language requires expanding your vocabulary to know what words exist before you can begin to learn to string them together into sentences, or complex functions. Tutorials are a good place to start, and google is your friend. Our goal in the first part of this tutorial is to learn some of the unix basics (or GNU basics, or bash basics, depending on what we're referencing). If you google 'unix tutorial' you'll find hundreds of good examples, and of course I've provided some examples below as well. But rather than reinvent the wheel, we'll make use a of a very  good existing unix tutorial to learn some of the basics. First, let's get our hands on a 'cheat sheet', another useful term to google. These type of documents usually list very concisely the most commonly used commands for a program. Here's a pretty good one for learning unix tools [https://files.fosswire.com/2007/08/fwunixref.pdf](https://files.fosswire.com/2007/08/fwunixref.pdf). 



When learning a new complex programming tool or language a 


### (optional) Fun games to further test your skills:  
https://www.shortcutfoo.com/app/dojos/command-line



-------------------------------------------------------------------------------------------

### II. Text editors  
There are many popular text editors and everyone has their own preference. They typically vary in the their size and complexity as well as in the type of key-bindings (short-cut commands to perform common functions) that they support by default. It is good to know at least one common 'bare-bones' text editor that can be run from within a terminal (e.g., `nano`, `vim`, or `emacs`) as well as one good GUI based editor which are more combersome (e.g., `atom`, `sublimetext`). Across all of these there are two common key-binding modes, 'vim-mode' and 'emacs-mode'. I learned emacs key-bindings first and have stuck with that ever since, which is easy to use in `neno` and `emacs`, and is supported in `sublime`. Many others prefer vim key-bindings, which are default in `vim`, `atom`, and `sublime`. Either mode is simple enough to learn in about 20-30 minutes, and there are many good tutorials. 


#### Finish at least one of the following tutorials:
**emacs tutorial**: open `emacs` in a terminal by typing `emacs`. This will open up a splash screen that will say "Welcome to GNU Emacs at the top. Below this you should see a highlighted line that says `Emacs Tutorial`. Use the arrow keys to move the cursor down to this link and then hit enter to open the tutorial.  

[learn vim key-bindings online](http://www.openvim.com/)  
[learn emacs key-bindings online](


#### (optional) Fun games to test your skills: 
Learning text editor key-bindings takes practice, practice, practice, but once you learn it the benefits are incredible. You'll feel like a sloth every time you open a word document in the future and are unable to jump to the beginning of a sentence with a simple key stroke, or replace every instance of one word with another without having to click on three menu buttons. Practicing takes time, though, of course. 

https://www.shortcutfoo.com/app/dojos/emacs





# 1-Shell-Basics

Learning to use a bash terminal and the many unix commands that it makes available is key to becoming an efficient and competent programmer, regardless of which programming language you eventually advance to using. Unix programs are typically used to manage and monitor resources on your computer (e.g., disk space, RAM), and to create and modify files and directories, all by manipulating **text files**. Surprisingly, with just a small number of programs very complex tasks can be accomplished by stringing together the input and output of several program calls.

## I. The bash terminal  

Just like learning any other type of language, learning to program requires expanding your vocabulary to know what words exist before you can begin to learn to string them together. Tutorials are a good place to start, and google is your friend. Our goal in the first part of this tutorial is to learn some of the unix basics (or GNU basics, or bash basics, depending on what we're referencing). If you google 'unix tutorial' you'll find hundreds of good examples, and of course I've provided some examples below as well. But rather than reinvent the wheel, we'll make use a of a very  good existing unix tutorial to learn some of the basics. First, let's get our hands on a 'cheat sheet', another useful term to search for. These type of documents usually list very concisely the most commonly used commands for a program. Here's a pretty good one for learning unix tools [https://files.fosswire.com/2007/08/fwunixref.pdf](https://files.fosswire.com/2007/08/fwunixref.pdf). 

### a. The unix filesystem


Depending on your operating system (Linux, OSX, etc.) the exact setup of your filesystem may vary slightly, but in general all Unix based systems will follow a prescribed formula (called POSIX) so that users will know where to expect to find certain types of files. Within this system there are reserved areas that users should typically not mess with, since you might mess up something important. Fortunately, your system protects you from making such mistakes by protecting those files based on *user permissions*. We'll see more about permissions later. 

### b. The root location 
The filesystem is hierarchical starting at the root, which is indicated by a `/` character. All other files in your system are "nested" within folders that are inside this folder. You need to have administrative privileges to mess with files that are close to the root, which is why these are sometimes called "root privileges". However, other parts of the filesystem will be completely under your control, meaning you can create, edit and delete files at will and it will not mess up any of the system files. 

### c. Your location
A very important concept to undertand about working in a terminal is that **your actions are always taking place relative to your location**. That is, you are always located somewhere in the filesystem when a terminal is open. By default, this will typically start in a location called your "home" directory. But you will usually move from there to other places in the filesystem, such as other folders nested within your home folder. It is just like the visual windows that you see when you open folders on your desktop. 

### d. Unix commands to move around the filesystem

We're going to start by learning the following commands: `pwd`, `ls`, `mkdir`, `cd`.  


#### Ask: Where am I?
The `pwd` command will show the current directory where you are located. You may be in "home", or somewhere else like I am below. 


```bash
## the 'pwd' command means 'print working directory'
pwd
```

    /home/deren


The output above shows that my current location is inside a directory called `deren`, which is inside a directory called `home`, which itself is inside the root `/`. Get it? When you are working in a terminal you will almost always have your current working directory located inside your "user" directory, which on linux will be `/home/{username}`, and on OSX will be `/users/{username}`. 

### Create a new directory
Use the `mkdir` command to create a new directory called `PDSB`. This new directory will be created nested inside our current directory (the location we just printed above). 


```bash
## create a new directory
mkdir PDSB
```

#### Ask: What is here?
The `ls` command can be used to list all of the files and directories that are located in your current location, or in some other location. 


```bash
## show items in my current directory
ls
```

    LICENSE  README.md  Untitled.ipynb



```bash
## show items in my curdir as a list (shows a bunch of other info too)
ls -l
```

    total 48
    -rw-rw-r-- 1 deren deren 35147 Jan 21 18:45 LICENSE
    -rw-rw-r-- 1 deren deren  3462 Jan 21 18:45 README.md
    -rw-rw-r-- 1 deren deren  6103 Jan 21 19:05 Untitled.ipynb



```bash
## show items in a different directory in my file system
ls -l /home/deren/scratch
```

    total 44984
    drwxrwxr-x 3 deren deren     4096 Jan 14 12:31 [0m[01;34mdash-heroku-template[0m
    drwxrwxr-x 3 deren deren     4096 Jan 14 17:53 [01;34mdocker-testing[0m
    drwxrwxr-x 2 deren deren     4096 Jan 14 12:28 [01;34mheroku-dash-app[0m
    -rw-rw-r-- 1 deren deren        0 Oct 17 18:00 ipyrad_log.txt
    -rw-rw-r-- 1 deren deren   121326 Oct 18 15:01 likelihood-primer.ipynb
    -rw-rw-r-- 1 deren deren   419819 Oct 18 11:27 QMC-testing.ipynb
    -rw-rw-r-- 1 deren deren 45486209 Oct 18 08:04 quartets.txt
    -rw-rw-r-- 1 deren deren      908 Jan 15 09:35 #scratch.txt#
    -rw-rw-r-- 1 deren deren     1008 Jan 15 09:33 scratch.txt
    -rw-rw-r-- 1 deren deren     1107 Jan 15 09:02 scratch.txt~
    -rw-rw-r-- 1 deren deren      489 Oct 18 08:05 tre.tmp


### c. Changing directories
Move to a different directory. 


```bash

```

When learning a new complex programming tool or language a 


### (optional) Fun games to further test your skills:  
https://www.shortcutfoo.com/app/dojos/command-line


```bash

```
