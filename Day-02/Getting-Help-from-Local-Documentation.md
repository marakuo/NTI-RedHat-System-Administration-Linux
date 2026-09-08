# Chapter 3 — Getting Help from Local Documentation

> **Red Hat Enterprise Linux (RHEL) — Linux Fundamentals**
>
> This chapter is about learning how to **find information yourself** from the Linux system instead of memorizing every command.

---


The main tools covered in this chapter are:

```text
man
man -k
less
more
head
tail
wc
file
type
getent
history
!
!!
su
```

---

# 2. Manual Pages — `man`

The `man` command displays the **manual page** (documentation) for a command or other system component.

### Basic syntax

```bash
man COMMAND
```

Example:

```bash
man ls
```

This opens the manual page for `ls`.

You can also use:

```bash
man cp
man mv
man mkdir
man passwd
```

---

## 3. Understanding a Man Page

A typical manual page contains several important sections.

For example:

```text
NAME
       ls - list directory contents

SYNOPSIS
       ls [OPTION]... [FILE]...

DESCRIPTION
       List information about the FILEs...

OPTIONS
       -l     use a long listing format
       -a     do not ignore entries starting with .
```

### Important sections

| Section | Meaning |
|---|---|
| `NAME` | Command name and short description |
| `SYNOPSIS` | Command syntax and how it should be used |
| `DESCRIPTION` | Detailed explanation |
| `OPTIONS` | Available command-line options |
| `EXAMPLES` | Examples, when provided |
| `FILES` | Related files |
| `SEE ALSO` | Related commands or documentation |

### Example

```bash
man ls
```

If you want to know what `-l` does:

```bash
man ls
```

Then search for `-l` or read the **OPTIONS** section.

---

# 4. Man Page Sections

Linux manual pages are divided into numbered sections.

The commonly encountered sections are:

| Section | Contains |
|---:|---|
| `1` | User commands / executable programs |
| `2` | System calls |
| `3` | Library functions |
| `4` | Special files / device files |
| `5` | File formats and configuration files |
| `6` | Games |
| `7` | Miscellaneous / conventions / overviews |
| `8` | System administration commands |
| `9` | Kernel routines (on systems that provide it) |

> The exact availability and contents of sections can vary between Linux distributions and installed documentation.

---

# 5. Command Name + Section Number

Sometimes the same name exists in multiple manual sections.

You can explicitly choose the section:

```bash
man 1 passwd
```

```bash
man 5 passwd
```

These are different:

- `man 1 passwd` → the `passwd` command
- `man 5 passwd` → the `passwd` configuration file format

Another example:

```bash
man 5 crontab
```

Here you are reading the documentation for the **crontab file format**, rather than a command.

### List all matching manual pages

```bash
man -a passwd
```

This can show multiple manual pages with the same name.

---

# 6. Searching Inside a Man Page

Man pages are normally opened using a pager such as `less`.

You can search inside them.

### Search forward

Press:

```text
/word
```

For example:

```text
/options
```

Then press:

```text
Enter
```

The manual page searches forward for that word.

### Search backward

```text
?word
```

### Move between matches

```text
n
```

moves to the next match.

```text
N
```

moves to the previous match.

### Exit the man page

```text
q
```

---

# 7. `less`

`less` is a terminal pager used to read text one screen at a time.

Example:

```bash
less /etc/passwd
```

Useful keys:

| Key | Action |
|---|---|
| `Space` | Next page |
| `b` | Previous page |
| `↑` / `↓` | Move line by line |
| `g` | Go to beginning |
| `G` | Go to end |
| `/word` | Search forward |
| `?word` | Search backward |
| `n` | Next search result |
| `N` | Previous search result |
| `q` | Quit |

`less` is especially useful for large files because it does not require you to display the entire file at once.

---

# 8. `more`

`more` is another pager.

Example:

```bash
more /etc/services
```

It displays the file one screen at a time.

Compared with `less`, `more` traditionally provides fewer navigation features. On modern Linux systems, `less` is generally more flexible.

A useful way to remember them:

```text
more → basic page-by-page viewing
less → more powerful interactive viewing/searching
```

---

# 9. `head`

`head` displays the beginning of a file.

### Basic usage

```bash
head file.txt
```

By default, it normally displays the first **10 lines**.

### Show a specific number of lines

```bash
head -n 5 file.txt
```

This displays the first 5 lines.

You may also see:

```bash
head -5 file.txt
```

### Example

```bash
head -n 3 /etc/passwd
```

This shows the first three lines of `/etc/passwd`.

---

# 10. `tail`

`tail` displays the end of a file.

```bash
tail file.txt
```

By default, it normally displays the last **10 lines**.

### Show a specific number of lines

```bash
tail -n 5 file.txt
```

### Follow a file

One of the most useful options is:

```bash
tail -f logfile
```

`-f` means **follow**.

It keeps the command running and displays new lines as they are appended to the file.

This is useful for monitoring log files:

```bash
tail -f /var/log/messages
```

Press:

```text
Ctrl+C
```

to stop following the file.

---

# 11. `wc` — Word Count

`wc` stands for **word count**, but it can count more than words.

```bash
wc file.txt
```

Typical output:

```text
20  100  650 file.txt
```

The values represent:

```text
lines   words   bytes
```

### Count lines

```bash
wc -l file.txt
```

### Count words

```bash
wc -w file.txt
```

### Count bytes

```bash
wc -c file.txt
```

### Example

```bash
wc -l /etc/passwd
```

This tells you how many lines are in the file.

---

# 12. `file` — Determine What a File Really Is

One important Linux concept is:

> **A file's type is not determined simply by its filename extension.**

Linux does not rely on extensions in the same way many other operating systems do.

For example:

```text
photo.jpg
```

does not automatically mean the file is actually a JPEG image.

You can check the file using:

```bash
file photo.jpg
```

Example output might be:

```text
photo.jpg: JPEG image data
```

If someone renames the file:

```bash
mv photo.jpg notes.txt
```

the contents do not magically become text.

You can still run:

```bash
file notes.txt
```

and Linux can identify the actual file type from its contents.

---

## 13. How Does `file` Determine the Type?

The `file` command examines information inside the file.

One important mechanism is **magic numbers / magic patterns**.

Many file formats begin with characteristic byte sequences that identify the format.

For example:

```text
File extension:
    report.txt

Actual content:
    might be an image, archive, executable, etc.
```

The `file` command examines the content and compares it with known file signatures and other characteristics.

Therefore:

```bash
file something
```

is much more reliable than simply looking at:

```text
something.jpg
something.txt
something.zip
```

---

# 14. `type` — What Kind of Command Is This?

Do not confuse:

```bash
file
```

with:

```bash
type
```

### `file`

Tells you what **kind of file** something is.

```bash
file myfile
```

### `type`

Tells you how the shell interprets a **command name**.

```bash
type ls
```

Possible output:

```text
ls is aliased to `ls --color=auto'
```

or, depending on the system:

```text
ls is /usr/bin/ls
```

Another example:

```bash
type cd
```

You may get:

```text
cd is a shell builtin
```

This means `cd` is built into the shell rather than being a separate executable.

You can also check:

```bash
type pwd
type echo
type history
```

---

# 15. `getent`

`getent` retrieves entries from databases configured through the system's **Name Service Switch (NSS)** configuration.

Basic syntax:

```bash
getent database [key]
```

For example:

```bash
getent passwd
```

This displays user-account entries available through the configured NSS sources.

To look up a specific user:

```bash
getent passwd root
```

You can also query groups:

```bash
getent group
```

or a specific group:

```bash
getent group wheel
```

### Why is `getent` useful?

It can query information through the system's configured name-service mechanisms rather than simply reading one local file.

For example, user information might come from local files, LDAP, or other configured sources.

---

# 16. Finding Documentation by Keyword

Sometimes you know **what you want to do**, but you do not know the command.

For example:

> "I need a command related to copying files."

You can search manual page descriptions.

```bash
man -k copy
```

Example:

```bash
man -k password
```

This searches the manual-page database for matching keywords.

### Important idea

```text
man command
    ↓
I know the command → show its documentation

man -k keyword
    ↓
I know the task/topic → help me find relevant commands
```

---

# 17. `history`

The shell keeps a history of previously executed commands.

Display the history:

```bash
history
```

Example:

```text
  101  pwd
  102  ls
  103  cd /etc
  104  cat hosts
```

This is useful when you want to remember or reuse something you executed earlier.

---

# 18. `!` — History Expansion

The `!` character can be used to execute commands from shell history.

### Execute a command by its history number

Suppose:

```text
101  pwd
102  ls
103  whoami
```

You can run command 102 again:

```bash
!102
```

The shell expands it to the corresponding historical command.

---

## 19. `!!` — Repeat the Last Command

`!!` refers to the previous command.

Example:

```bash
apt update
```

If you need to repeat it:

```bash
!!
```

This executes the previous command again.

A common example is:

```bash
sudo !!
```

This means:

```text
Run the previous command with sudo.
```

For example, if you previously ran:

```bash
dnf update
```

you could use:

```bash
sudo !!
```

which expands to approximately:

```bash
sudo dnf update
```

> Whether a command actually requires `sudo` depends on the operation and system configuration.

---

# 20. `su` — Switch User

`su` stands for **switch user** (historically "substitute user").

It allows you to run a shell as another user.

### Switch to another user

```bash
su username
```

Example:

```bash
su student
```

You will normally be asked for the target user's password.

### Switch to root

```bash
su -
```

The `-` requests a login shell, so the environment is initialized more like a normal login for the target user.

You may also see:

```bash
su - root
```

### Exit the switched-user shell

```bash
exit
```

or:

```text
Ctrl+D
```

---

# 21. `su` vs `sudo`

These commands are related but serve different purposes.

### `su`

Changes to another user's account/shell:

```bash
su - username
```

### `sudo`

Runs a particular command with elevated privileges:

```bash
sudo command
```

Example:

```bash
sudo dnf update
```

Conceptually:

```text
su
→ become another user / open another user's shell

sudo
→ run a specific command with elevated privileges
```

The exact permissions depend on the system configuration.

---

# 22. A Practical Documentation Workflow

When you encounter an unfamiliar command, use this workflow.

### Step 1 — Check what the command is

```bash
type command
```

Example:

```bash
type ls
```

This helps determine whether it is an alias, shell builtin, function, or executable.

### Step 2 — Read the manual

```bash
man command
```

Example:

```bash
man tar
```

### Step 3 — Search inside the manual

```text
/keyword
```

Then:

```text
n
```

to find the next match.

### Step 4 — Search by keyword if you do not know the command

```bash
man -k keyword
```

---

# 23. Example: "I Need to Find Something About Users"

Imagine you need information about Linux users but do not remember the exact command.

Start with:

```bash
man -k user
```

You may find several relevant commands.

Then inspect a candidate:

```bash
man getent
```

You can investigate user databases:

```bash
getent passwd
```

and a specific user:

```bash
getent passwd root
```

The important skill is not memorizing one command.

It is knowing how to **discover the command and read its documentation**.

---

# 24. Example: Investigating an Unknown File

Suppose you receive:

```text
download.txt
```

Do not automatically assume it is a text file.

First:

```bash
file download.txt
```

If it reports that it is an archive, image, executable, etc., you now know the actual format.

If it is text, you can inspect it:

```bash
less download.txt
```

Look at the beginning:

```bash
head download.txt
```

Look at the end:

```bash
tail download.txt
```

Count its contents:

```bash
wc download.txt
```

This gives you several useful pieces of information without opening the entire file in an editor.

---

# 25. Quick Command Reference

| Command | Purpose | Example |
|---|---|---|
| `man` | Read manual pages | `man ls` |
| `man -k` | Search man pages by keyword | `man -k network` |
| `less` | View text interactively | `less file.txt` |
| `more` | View text page by page | `more file.txt` |
| `head` | Show beginning of file | `head file.txt` |
| `tail` | Show end of file | `tail file.txt` |
| `tail -f` | Follow appended output | `tail -f log` |
| `wc` | Count lines/words/bytes | `wc -l file.txt` |
| `file` | Identify file type | `file image.jpg` |
| `type` | Identify how shell interprets command | `type cd` |
| `getent` | Query NSS databases | `getent passwd root` |
| `history` | Show command history | `history` |
| `!N` | Execute history entry N | `!102` |
| `!!` | Repeat previous command | `!!` |
| `su` | Switch user | `su - username` |

---

# 26. Important Concepts to Remember

### 1. `man` is your first reference

```bash
man command
```

### 2. Man sections matter

```bash
man 1 command
man 5 command
man 8 command
```

The number tells `man` which manual section to open.

### 3. You can search inside a man page

```text
/keyword
n
N
q
```

### 4. You can find commands by description

```bash
man -k keyword
```

### 5. Linux files are not defined by extensions

Use:

```bash
file filename
```

to investigate the actual file type.

### 6. `type` is about commands, not ordinary files

```bash
type ls
type cd
```

### 7. Use pagers for large text

```bash
less file
more file
```

### 8. Inspect only parts of a file when appropriate

```bash
head file
tail file
```

### 9. Count file contents

```bash
wc file
```

### 10. Use history to save time

```bash
history
!! 
!number
```

### 11. `getent` queries system databases through NSS

```bash
getent passwd
getent group
```

---

# 27. Mini Practice Lab

Try the following commands in your RHEL virtual machine.

### Documentation

```bash
man ls
```

Inside the man page:

```text
/-l
```

Then press:

```text
n
```

and finally:

```text
q
```

### Search for commands

```bash
man -k password
```

```bash
man -k directory
```

### Inspect files

```bash
file /etc/passwd
```

```bash
head /etc/passwd
```

```bash
tail /etc/passwd
```

```bash
wc /etc/passwd
```

```bash
less /etc/passwd
```

### Investigate commands

```bash
type ls
type cd
type echo
```

### Query users/groups

```bash
getent passwd
```

```bash
getent passwd root
```

```bash
getent group
```

### Practice history

```bash
history
```

Then repeat the previous command:

```bash
!!
```

Try executing a specific history entry:

```bash
!<number>
```

### Practice switching users

```bash
su - username
```

When finished:

```bash
exit
```

---

# 28. Chapter Summary

The main lesson of this chapter is:

> **You do not need to memorize Linux. You need to know how to find the information Linux already provides.**

When you forget a command:

```text
Know the command?
        ↓
      man
        ↓
  Search inside it
```

When you do not know the command:

```text
Know the task/topic?
        ↓
man -k
        ↓
Find possible commands
        ↓
man command
```

For files:

```text
file  → identify type
head  → beginning
tail  → end
less  → browse/search
more  → page through
wc    → count
```

For commands and shell history:

```text
type    → identify command type
history → see previous commands
!N      → run history entry N
!!      → repeat previous command
su      → switch user
```

