---
title: "EffectiveC++No.41"
date: 2019-03-03 23:45:00
categories: c++
---
# linux file system

## linux file system directory structure
- root directory (/ ,Top directory)
- sub directory (home, dev, etc, val, root,)
- home directory : linux system is the system that many people can use this systme simultaneous so OS devides and make each people's directory.
- device directory (dev) : device informations are saved here.
- valiable directory (val) : val directory save temporal information
- etc directory : etc directory save ?

## linux file types
- normal files
    - for data saving
    - text file : ascii code which is translated to binary to execute.
    - binary file : consist of 0/1 for excutable file 
    - 

- directory file
    - when we use ls cmd, we can see the d--- which is the file type, d means directory file.

- symbolic link file
    - direct move file 
    - which linked with original file to execute

- device file
    - when we use hardware system, os manage this hardware as file, this file is device file.

## linux file name rules
- '/' can not be used
- 'space' can not be used
- _,-,., alphabet can used
- 

## linux cmd related with file

### cat 
- print text 

### more
- print text when text size is big.

### touch
- make the files 

### rm
- remove file

### mv
- move file/directory poistion
    - mv aaa bbb ccc ddd : aaa,bbb,ccc file move to .ddd directory
- change the file name
    - 
### cp 
- copy file/directory

### head
- show 10 line from start line of text

### tail
- show 10 line from end line of text


