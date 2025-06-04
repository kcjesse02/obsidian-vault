# Intro
## What should I get out of this presentation
- Linux has many commands that you can use to interface with the operating system
- you shouldn't try to remember all of the commands, but rather understand what they mean; how linux wants its users to interact with it
- these are only tools in your toolbox, they will be useful in a lot of other cyber-related things but they will mostly augment information about whatever you are doing.
- its ok to ask questions about these syntax type things, no one will be mad at you.
- if you want practice with the syntax of these commands, I highly recommend bandit lab

## Command format
```bash
command [options] [subject]
```
- multiple options can be used, usually in the format of -letter
- 
## SSH
- important for CTFs!
- an ssh connection gives you access to a file system on a remote machine associated with a specific user

a basic ssh command format
```bash
#without port
ssh usr@url
#specify a port
ssh usr@url port
```
## Man
- provides information about specific commands
- gives information about what the command does
- lists options and what they do
- best used a reference for commands you already know

# Basic Commands
some background info
Linux is an operating system made up of files, everything is a file
files are organized into directories (folders), which form a tree-like structure, each node being a directory
## cat
- print out the contents of a file
## ls
- list a file system

## cd
- changes a directory
- .. to go back a directory
## touch
- 
## mv

## find
- explain users and groups
	- can be used to set permissions
	- groups can access a file
## grep
- bandit8


# BanditLab
bandit1: NH2SXQwcBdpmTEzi3bvBHMM9H66vVXjL
- just cat the file
bandit2: rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi
- the filename is just "-"
- add the ./ prefix so it knows you are refering to a file
bandit3: aBZ0W5EmUfAf7kHTQeOwd8bauFJ2lAiG
- there are spaces in the filename
- use quoteations or backslashes
bandit4: 2EW7BBsr6aMMoJ2HjW067dm8EgX26xNe
- cd into directory inhere
- use ls -h to find .hidden
bandit5:lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR
- find the only human readable file in the directory
- use the `file ./*` command to get info about every file in the `inhere` directory
bandit6: P4L4vucdmLnm8I7Vl7jG1ApGSfjYKqJU
- find file that is human-readable, 1033 bytes, and non-executable within a tree of directories
- use `find . -size 1033`
bandit7: z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S
- look for file with user bandit7 and group bandit6 and 33bytes
- `find / -user bandit7 -group bandit6 -size 33c 2> /dev/null` 
- then cat the file it gives you
bandit 8: TESKZC0XvTetK0S9xNwm25STk5iWrBvP
- The password for the next level is stored in the file **data.txt** next to the word **millionth**
- use `grep` to find it: `cat data.txt | grep `
bandit9: EN632PlfYiZbn3PhVK3XOGSlNInNE00t
- some commands require the use of other commands to work
- `sort` sorts lines by lexicographic order
- `uniq -u` filters for unique lines
- piping is used to filter output into the input of a new command
bandit10: G7w8LIi6J3kTb8A7j9LgrywtEUlyyp6s
- the data.txt file consists of base64 encoded data
- `base64 -d data.txt | cat`
- base64 doesn't print out the contents of the file, so you need to pipe the output into cat, which prints out to the terminal
bandit11: 6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM

bandit14
- asks user to use ssh key to log into