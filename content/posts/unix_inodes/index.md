---
title: "UNIX Inodes"
date: 2023-06-18T10:20:22+02:00
draft: false
---

In 1969 at Bell Labs a revolution was brought to the world: the **UNIX** operating system. Talking about Unix itself would not take an entire post but a full dedicated blog. I will only write down about one of the fundamentals of a Unix-like system: **Inodes**.

## What is an inode?
The structure that identifies univocally a file within the filesystem is called inode. Since in Unix-like systems "everything is a file" each file is associated with an inode. The inode stores the metadata of a file: 

- type: regular, directory, symbolic link, char device, block device, FIFO or pipe, socket.
- dimension
- access permissions
- access time
- number of hard links to the file
- ACL
- addresses of disk blocks containing data.

The inode **does not** contains the filepath of a file.

## Directories
In Unix-like systems files are not "contained" in a directory but rather they are stored in a tree-like structure. The directory itself is an inode and is simply a list of inodes. Directories keep file names and the corrispettive inodes. As wrote above the informations of a file in a directory are kept with the file's inode.

## Display the file inode
The informations from the **ls** command comes from the inode. To display the inode associated with a file just type in your terminal
```bash
ls -i
```
Let's see an example:

![inode](/ls_command.png)

The inode of test1.txt file is reported under the first column and is equal to 15882024.

We can search for files by their inode number as well:

![search](/search_inode.png)

## Hard link
When it comes to inodes talking about **hard links** is compulsory. Let's say you have a friend called
Johnatan and you refere to him as John; both names "points" to the same person. It's easy now to understand the concept of hard link: two files pointing to an equal inode are basically the same file.
Each file keeps track of how many references have been made.

Hard links cannot reference files on other filesystems.

The example below shows how to make an hard link then lists the inodes associated with the files

![hardlink](/hardlink2.png)

The inodes of both 'test1.txt' and 'hardtest.txt' are the same; this means everytime you edit one of these file the change will affect both.
It's worth noticing the link count has increased to 2.

## The rm and mv commands
The rm command removes the name associated with an inode and decrement the link count. When the link count reaches the zero only at that point the real data is deleted and the space reclaimed is released.

The mv command doesn't change the data but only the name associated with the file. The inode remains the same.

## Directories and hard links
Unix-like systems prohibits you to make hard links to directories. I report the notes taken from the GNU/Linux Operating Systems I course held by Prof Ian D. Allen back in 2019 at the Algonquin College of Applied Arts and Technology:

- Every directory has a unique parent directory. Hard-linked directories would mean that one directory could have multiple parent directories, one for each hard link to the directory. Where would cd .. lead, if there are multiple choices?

- There is a unique symlink-free absolute pathname for every file system basename. Hard-linked directories would allow multiple different absolute pathnames with different directory hard links for the same basename, e.g. /a/b/c and /a/x/c. Not good.

- The Unix file system is a Directed Acyclic Graph, which means that if a directory contains the name of a sub-directory, other than . or .., that sub-directory is guaranteed not to be an ancestor of the current directory or to lead to any ancestor of the current directory. Hard links would let a sub-directory name be a link to a parent directory, creating a file system “cycle” or loop. (Only the special name .. is allowed to refer to an ancestor directory.)

## Running out of inodes
Each filesystem store a different amount of inodes. To display the current usage of inodes on you system you can type the following command:
```bash
df -i /dev/sda1
```

## Useful links
Prof. Ian D. Allen course: [GNU/Linux Operating Systems I](https://teaching.idallen.com/cst8207/19w/)
