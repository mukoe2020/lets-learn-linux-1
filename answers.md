# Q1

## What does each command tell you?

* `pwd` displays the current working directory. This helps me know my location in the file system before creating or modifying files.

* `whoami` displays the username of the account I am  using. This helps me know whether I am logged in as the root user or a regular user 

* `uname -a` displays information about the operating system and kernel.lt helps me understand the environment I am working in and identify the kernel version.

## Why does a DevSecOps engineer run these commands first?

A DevSecOps engineer runs these commands first to know where they are, which user they are logged in as, and what operating system and kernel the server is using. This helps them avoid making mistakes and understand the environment they are working in. Knowing the kernel version is important because some kernel versions may have known security vulnerabilities (CVEs), so the engineer can check whether the server needs security updates or patches.


Q2: Listing the root directory and understanding Linux filesystem structure

* ls 

This command lists the contents of the root directory /, which is the top-level of the Linux filesystem

* ls -al

We can also use ls al to show hidden files  those with a dot

## Pick five directories from the output and explain each one's purpose

* /etc → Configuration files  ( network, users, services)
* /home → Personal directories
* /var → Logs, databases, and files
* /bin →  command binaries (ls, cp, mv)
* /usr → User-installed software and system programs 

## What is the Filesystem Hierarchy Standard (FHS) and why does it exist?

It is a set of rules that defines how files and folders are organized in Linux . It ensures all Linux distributions follow a consistent structure so files are always in predictable locations.It exist to keep linux files constanst in all files and to support scripting and  and simplify adminstration

### What breaks on a shared production server without it?

Systems become disorganized. Files end up in random places, causing scripts and applications to break ,configuration files to be hard to find 
and automation tools to fail

Q3 : What is different between the two outputs? What makes a file hidden in Linux — is it a special type? Name two real hidden files found in a home directory and explain what they do.

## What is different between the two outputs?

The first command (ls ~) shows only visible files and folders in the home directory. It gives a simple list without extra information.
The second command (ls -al ~) shows:
Hidden files that start with .
File permissions (read, write, execute)
Owner and group of each file
File size
modified date and time

###  Name two real hidden files found in a home directory and explain what they do.
1. .config
This is a directory used by many applications to store user settings.
2. .dotnet
This is used by the .NET runtime environment.

Q4 :  Which flag did you use and what does it do? What is brace expansion and how did it help here?

I used the -p flag with mkdir.It creates parent directories if they don’t exist ,builds nested folders in one go safely
Brace expansion is a Bash feature that lets you generate multiple  directories in one command

### Could you have done this without it — and would you want to?
Yes l could have done it without the  flags or brace expansions it would mean creating each folder and file one at a time  but noo l wouldnt have done it it wld be slow and alot of typos can occur 


Q5 : What does this command actually do beyond creating files? What happens if you run it on an existing file?
touch does more than just create files:
If the file does not exist it creates an empty file
If the file already exists it updates

If the file already exists ,it is not overwritten contents remain unchanged
Only the last modified time and access time are updated

## Check ls -l before and after — what changes? Why does this behaviour matter in automation scripts?
What changed:
File timestamp (modification time updates)
File size stays the same 
its important because:
Scripts can check timestamps to detect changes
Prevents accidental data loss no overwriting

Q6 : What do the file sizes tell you about the files you just created
Both files show 0 bytes, they're empty — touch creates files but doesn't add content. The files exist, but have no data inside.

##  Break down the permission string on one file — explain every character group
-   = file type. 
`-` means regular file. `d` would mean directory.
rw- = owner permissions: `r`=read, `w`=write, 
`-`=no execute. (You can only  read/write it.)
r-- = group permissions: `r`=read, `-`=no write,
 `-`=no execute. Your group can only read.
r-- = others permissions: `r`=read, `-`=no write, 
`-`=no execute. Everyone else can only read.

Q7 ::How is tree different from ls -R? When would a DevSecOps engineer run tree on a live server — what would they be looking for?
 tree draws lines so you see parent/child folders instantly.
 ls -R just lists each dir and  files one after another. 
 tree gives a summary at the bottom . ls -R doesn't.
 tree is like a map, ls -R is like a stack of folders

### When a DevSecOps engineer runs tree on a live server + what they look for:
They're looking for: unexpected files, wrong permissions, missing directories, or sensitive data in the wrong place.

##Q8 ::: What is the difference between > and >>? Prove it — re-run the first echo with > instead of >>, then read the file. What happened? Restore the file. Why is confusing these two operators dangerous in a production log rotation script?

> = overwrite. Wipes the file, then writes.
>> = append. Adds to the end, keeps existing content.

after overwritting the file now only cointains overwrite  all the 3 logs kines are gone .> deleted everything and put new ones  
To restore the file will run all those echo commands again 
What confusing is  if l mistenly use > instead of >> l might delete the entire logs files  which will cause loss of dat  and impossible to investigate  breaches 


###Q9::: Task: Display the access log using two different reading commands — one from top to bottom, one from bottom to top.

cat/less = reads from line 1 downward. Shows oldest entries first.
tac = "cat" backwards. Reads from last line upward. Shows newest entries first.

### In a real incident, a log has 80,000 lines and the most recent events are at the bottom — which do you reach for first?

tac or tail. In an incident you want the most recent . Scrolling 80k lines from the top wastes time. tac goes to the   commands immediatly

tail  defaults to 10 lines, so tail file works too. for the last commands if you flag it


##Q10 :::  Use heredoc notation to append all four lines into logs/errors/error.log in one operation.
###What is a heredoc and what does it solve over multiple echo commands?
What is the difference between 'EOF' (quoted) and EOF (unquoted)? Set a variable DBNAME=cyphercore and test both — screenshot the outputs and explain what you observe.
Why might someone on a compromised server prefer heredoc over opening nano or vim? What forensic traces does each method leave?

Heredoc  meaning "here document". It pipes a block of text into a command. You define start << DELIM and end DELIM markers. Everything between gets sent as stdin

'EOF' = literal. Prints Database is $DBNAME. Variables don't expand.
EOF = interpreted. Prints Database is cyphercore. Shell expands $DBNAME.

 Heredoc  leaves traces in .bash_history with the full content. No temp files. Fast, less obvious.
nano/vim: Creates swap files .filename.swp, backup files filename~, updates atime/mtime, leaves .viminfo. More disk artifacts.
Attacker prefers heredoc = stealthier. Defender notices vim because of the temp files + longer process runtime.


###Q11 :::  Task: Open the error log using two different pager commands. Navigate and quit each one.

###Key differences between the two? Which loads the entire file into memory first? On a server with 512MB RAM and a 3GB log — which do you use? List three keyboard shortcuts for less: how to search, jump to the end, and quit.

less = scroll up/down with arrows, j/k. more = only forward, can't go back.less = doesn't load full file. more loads more upfront.less = search /pattern, jump G, quit q. more = limited navigation.

more loads more aggressively. less streams the file and loads pages on demand.
less.never use cat or editors on huge logs. less won't OOM the server because it reads chunks. more or vim might crash it.
