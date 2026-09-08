# Navigating the Linux File-System Hierarchy

Understanding how Linux organizes files and directories is one of the most important foundations for working with the command line.

Unlike Windows, Linux does not have drives such as `C:\` or `D:\` forming separate filesystem trees. Linux has **one main filesystem tree**, and everything starts from a single directory called the **root directory**:

```text
/
```

---

## 1. The Linux Filesystem Tree

Think of the Linux filesystem like an upside-down tree:

```text
/
├── bin
├── boot
├── dev
├── etc
├── home
│   ├── student
│   │   ├── Documents
│   │   ├── Downloads
│   │   └── Pictures
│   └── user2
├── lib
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── tmp
├── usr
│   ├── bin
│   ├── lib
│   ├── local
│   └── share
└── var
    ├── log
    ├── cache
    └── tmp
```

The `/` at the top is the **root directory**.

> **Root directory ****`/`**** is not the same thing as the ****`root`**** user.**

* `/` → the top of the filesystem
* `root` → the administrator account
* `/root` → the home directory of the root user

---

# 2. Important Directories

## `/` — Root Directory

The root directory is the starting point of the entire Linux filesystem.

Everything is located somewhere under `/`.

For example:

```text
/home/student/file.txt
```

starts from:

```text
/
```

then goes to:

```text
home → student → file.txt
```

You can see what is directly inside `/` with:

```bash
ls /
```

---

## `/home` — Users' Home Directories

`/home` normally contains the personal directories of regular users.

Example:

```text
/home
├── student
├── malak
└── user2
```

If the username is `student`, their home directory is usually:

```text
/home/student
```

Inside it, you might have:

```text
/home/student
├── Documents
├── Downloads
├── Pictures
└── Music
```

When you open a terminal, you will usually start in your own home directory.

---

## `/root` — Root User's Home Directory

`/root` is the home directory of the **root user**.

It is different from `/`.

```text
/       → filesystem root
/root   → root user's home directory
```

---

## `/etc` — System Configuration

`/etc` contains system-wide configuration files.

Examples include configuration for:

* users
* networking
* services
* system settings

For example:

```text
/etc/hosts
/etc/passwd
/etc/ssh/
```

---

## `/var` — Variable Data

`/var` contains data that changes frequently while the system is running.

Examples:

```text
/var/log
/var/cache
/var/tmp
```

One particularly important directory is:

```text
/var/log
```

which contains system and application logs.

---

## `/tmp` — Temporary Files

`/tmp` is used for temporary files created by programs and users.

Example:

```text
/tmp/myfile.txt
```

Files in `/tmp` should generally be considered temporary rather than permanent storage.

---

## `/usr` — Userland Programs and Data

`/usr` contains a large amount of the software, libraries, documentation, and other data used by the system.

Common directories include:

```text
/usr/bin
/usr/sbin
/usr/lib
/usr/share
/usr/local
```

For example:

```text
/usr/bin
```

contains many executable programs.

---

## `/bin` — Essential User Commands

Traditionally, `/bin` contains essential commands needed by users and the system.

Examples may include commands such as:

```text
ls
cp
mv
rm
cat
```

### Important note

On many modern Linux distributions, `/bin` is a symbolic link to:

```text
/usr/bin
```

You may therefore see:

```text
/bin → /usr/bin
```

This is called a **symbolic link**.

---

## `/sbin` — System Administration Commands

Traditionally, `/sbin` contains commands mainly used for system administration.

On modern systems, `/sbin` may also be integrated into `/usr/sbin`.

---

## `/dev` — Devices

Linux represents many hardware devices as files under `/dev`.

Examples:

```text
/dev/sda
/dev/null
/dev/tty
```

This is one of the ideas behind:

> **"Everything is a file"**

in Unix/Linux.

---

## `/proc` — Process and Kernel Information

`/proc` is a virtual file-system that provides information about:

* running processes
* CPU
* memory
* kernel information

For example:

```bash
ls /proc
```

You may see directories such as:

```text
/proc/1
/proc/2
/proc/1234
```

The numbers can represent process IDs (PIDs).

---

## `/boot` — Boot Files

Contains files required for booting the Linux system, such as the Linux kernel and bootloader-related files.

---

## `/lib` — Libraries

Contains important shared libraries needed by programs and the system.

On modern distributions, `/lib` may be integrated with `/usr/lib`.

---

## `/media` — Removable Media

Commonly used as a mount point for removable devices such as:

* USB drives
* CDs/DVDs

Example:

```text
/media/student/USB
```

---

## `/mnt` — Temporary Mount Point

`/mnt` is commonly used as a temporary location for mounting filesystems manually.

For example:

```text
/mnt/mydisk
```

---

## `/opt` — Optional Software

Often used for additional or third-party software packages.

Example:

```text
/opt/myprogram
```

---

## `/run` — Runtime Data

Contains temporary runtime information created since the system booted.

For example, information about running services and processes can be stored here.

---

# 3. Quick Filesystem Reference

| Directory | Purpose                                      |
| --------- | -------------------------------------------- |
| `/`       | Root of the entire filesystem                |
| `/home`   | Regular users' home directories              |
| `/root`   | Root user's home directory                   |
| `/etc`    | System configuration                         |
| `/var`    | Variable/changing data                       |
| `/tmp`    | Temporary files                              |
| `/usr`    | Programs, libraries, documentation, and data |
| `/bin`    | Essential user commands                      |
| `/sbin`   | System administration commands               |
| `/dev`    | Device files                                 |
| `/proc`   | Process and kernel information               |
| `/boot`   | Boot-related files                           |
| `/lib`    | Essential libraries                          |
| `/media`  | Removable media mount points                 |
| `/mnt`    | Temporary/manual mount points                |
| `/opt`    | Optional/third-party software                |
| `/run`    | Runtime system data                          |

---

# 4. What Is a Path?

A **path** tells Linux where a file or directory is located.

For example:

```text
/home/student/Documents
```

means:

```text
/
└── home
    └── student
        └── Documents
```

There are two main types of paths:

1. **Absolute paths**
2. **Relative paths**

---

# 5. Absolute Paths

An absolute path starts from the root directory:

```text
/
```

It describes the complete location of something.

Example:

```text
/home/student/Documents
```

This path always refers to the same location regardless of your current directory.

For example, if you are currently in:

```text
/tmp
```

you can still do:

```bash
cd /home/student/Documents
```

and Linux knows exactly where you want to go.

### How to recognize an absolute path

It starts with:

```text
/
```

Examples:

```text
/
/home
/home/student
/etc
/var/log
/usr/bin
```

---

# 6. Relative Paths

A relative path does **not** start with `/`.

Instead, Linux interprets it relative to your **current working directory**.

Suppose you are here:

```text
/home/student
```

and the directory structure is:

```text
/home/student
├── Documents
├── Downloads
└── Pictures
```

You can enter Documents using:

```bash
cd Documents
```

Linux interprets this as:

```text
/home/student/Documents
```

because you are currently in:

```text
/home/student
```

---

# 7. `pwd` — Where Am I?

Before navigating, it is useful to know your current location.

Use:

```bash
pwd
```

`pwd` means:

> **Print Working Directory**

Example:

```bash
$ pwd
/home/student
```

Now you know exactly where you are.

---

# 8. `ls` — What Is Here?

Use:

```bash
ls
```

to see the contents of your current directory.

Example:

```bash
$ pwd
/home/student

$ ls
Documents  Downloads  Music  Pictures
```

You can also specify a path:

```bash
ls /etc
```

or:

```bash
ls /home/student/Documents
```

---

# 9. `cd` — Change Directory

The `cd` command means:

> **Change Directory**

Basic syntax:

```bash
cd DIRECTORY
```

Example:

```bash
cd Documents
```

If you are currently in:

```text
/home/student
```

you will move to:

```text
/home/student/Documents
```

---

# 10. The Special Directory `.`

A single dot means:

```text
.
```

> **The current directory**

Suppose:

```text
/home/student
```

is your current directory.

Then:

```bash
.
```

means:

```text
/home/student
```

You can use it with commands:

```bash
ls .
```

which means:

> List the contents of the current directory.

You can also use:

```bash
cd .
```

This means:

> Change to the current directory.

So effectively, nothing changes.

---

# 11. The Special Directory `..`

Two dots mean:

```text
..
```

> **The parent directory**

The parent directory is the directory one level above your current directory.

Suppose you are here:

```text
/home/student/Documents
```

Then:

```text
.
```

means:

```text
/home/student/Documents
```

while:

```text
..
```

means:

```text
/home/student
```

Visualized:

```text
/home
   │
   └── student        ← ..
       │
       └── Documents  ← .
```

Therefore:

```bash
cd ..
```

moves you **one directory up**.

---

# 12. Going Up Multiple Levels

If you are here:

```text
/home/student/Documents/Linux/Week1
```

you can move up one level:

```bash
cd ..
```

Result:

```text
/home/student/Documents/Linux
```

Move up two levels:

```bash
cd ../..
```

Result:

```text
/home/student/Documents
```

Move up three levels:

```bash
cd ../../..
```

Result:

```text
/home/student
```

### The pattern

```text
..       → up 1 level
../..    → up 2 levels
../../.. → up 3 levels
```

---

# 13. What Does `../` Mean?

`..` means:

> Parent directory

The `/` means:

> Continue navigating into something inside that directory.

So:

```text
../
```

means:

> Go to the parent directory.

For example:

```bash
cd ../Documents
```

means:

1. Go up one level.
2. Enter `Documents`.

Suppose you are here:

```text
/home/student/Downloads
```

Then:

```bash
cd ../Documents
```

takes you to:

```text
/home/student/Documents
```

Visual:

```text
/home/student
├── Documents      ← destination
└── Downloads      ← current directory
```

From `Downloads`:

```text
.. → /home/student
../Documents → /home/student/Documents
```

---

# 14. Combining `..` With More Directories

You can chain multiple path components.

Suppose:

```text
/home/student/Projects/Linux/Week1
```

You want to reach:

```text
/home/student/Documents
```

You could use:

```bash
cd ../../../../Documents
```

But you should first count carefully:

```text
Week1
  ↑ ..
Linux
  ↑ ..
Projects
  ↑ ..
student
  └── Documents
```

Therefore:

```bash
cd ../../../Documents
```

Let's count:

```text
Week1 → Linux       = ..
Linux → Projects    = ../..
Projects → student  = ../../..
student → Documents = ../../.. / Documents
```

So:

```bash
cd ../../../Documents
```

---

# 15. `~` — Your Home Directory

The tilde character:

```text
~
```

means:

> **The current user's home directory**

If your username is `student`:

```text
~
```

represents:

```text
/home/student
```

Therefore:

```bash
cd ~
```

takes you directly to your home directory.

You can also use:

```bash
cd ~/Documents
```

which means:

```text
/home/student/Documents
```

---

# 16. `cd` With No Argument

You can simply type:

```bash
cd
```

with nothing after it.

This also takes you to your home directory.

So these are equivalent:

```bash
cd
```

```bash
cd ~
```

```bash
cd /home/student
```

The first two are more convenient because they work without typing your username.

---

# 17. `-` — Previous Directory

The dash:

```text
-
```

has a special meaning with `cd`.

It means:

> **The previous working directory**

This is extremely useful when switching between two directories.

Suppose you start here:

```text
/home/student
```

You run:

```bash
cd /etc
```

Now you are in:

```text
/etc
```

Run:

```bash
cd -
```

and Linux takes you back to:

```text
/home/student
```

Run:

```bash
cd -
```

again:

```text
/etc
```

So you can switch back and forth:

```text
/home/student
      ↓
     /etc
      ↓
/home/student
      ↓
     /etc
```

Example:

```bash
$ cd /etc

$ cd -
/home/student

$ cd -
/etc
```

---

# 18. Important Difference: `..` vs `-`

These two are often confused.

### `..`

Means:

> Go to the parent directory.

### `-`

Means:

> Go to the previous directory you were working in.

Example:

```text
Current:
/home/student/Documents

Previous:
/etc
```

Then:

```bash
cd ..
```

goes to:

```text
/home/student
```

while:

```bash
cd -
```

goes to:

```text
/etc
```

They are completely different concepts.

---

# 19. `../..` vs `..` vs `../../`

These are all related:

```text
..       → parent directory
../      → parent directory, then continue the path
../..    → parent of the parent
../../   → same idea, with a trailing slash
```

For example, if you are in:

```text
/home/student/Documents/Linux
```

then:

```bash
cd ..
```

→

```text
/home/student/Documents
```

```bash
cd ../..
```

→

```text
/home/student
```

```bash
cd ../../..
```

→

```text
/home
```

---

# 20. What About `../~`?

This is an important detail.

It is tempting to think:

```bash
cd ../~
```

means:

> Go up, then go to home.

But **it does not work that way**.

The `~` is expanded by the shell when it appears in the appropriate position, typically at the beginning of a word.

For example:

```bash
cd ~/Documents
```

works.

But:

```bash
cd ../~
```

does **not** mean:

```text
parent → home
```

If your goal is simply to go home, use:

```bash
cd ~
```

or:

```bash
cd
```

---

# 21. What About `../-`?

Similarly:

```bash
cd ../-
```

does **not** mean:

> Go to the parent, then go to the previous directory.

The special `-` behavior belongs to `cd` itself:

```bash
cd -
```

It is not a normal directory name that you can combine with `..` in this way.

If you want to go to the previous directory:

```bash
cd -
```

---

# 22. Combining `~` and `..`

You can use `..` inside a path that starts from your home directory.

For example:

```bash
cd ~/Documents/..
```

If:

```text
~ = /home/student
```

then:

```text
~/Documents/..
```

means:

```text
/home/student/Documents/..
```

`..` takes you back to the parent:

```text
/home/student
```

So:

```bash
cd ~/Documents/..
```

ends up in:

```text
/home/student
```

---

# 23. Absolute and Relative Paths Together

Suppose you have:

```text
/home/student
├── Documents
│   └── Linux
│       └── Week1
└── Downloads
```

If you are currently in:

```text
/home/student/Documents/Linux/Week1
```

You can reach `Downloads` using an absolute path:

```bash
cd /home/student/Downloads
```

Or using a relative path:

```bash
cd ../../../../Downloads
```

Or using the home shortcut:

```bash
cd ~/Downloads
```

All three reach the same location.

```text
Absolute:
 /home/student/Downloads

Relative:
 ../../../../Downloads

Home shortcut:
 ~/Downloads
```

---

# 24. `cd .` vs `cd ..` vs `cd ~` vs `cd -`

A useful cheat sheet:

| Command           | Meaning                                     |
| ----------------- | ------------------------------------------- |
| `cd .`            | Stay in the current directory               |
| `cd ..`           | Go up one level                             |
| `cd ../..`        | Go up two levels                            |
| `cd ~`            | Go to your home directory                   |
| `cd`              | Go to your home directory                   |
| `cd -`            | Go to the previous directory                |
| `cd /`            | Go to the root directory                    |
| `cd ~/Documents`  | Go to Documents inside your home            |
| `cd ../Documents` | Go to Documents inside the parent directory |

---

# 25. A Navigation Example

Imagine this filesystem:

```text
/
└── home
    └── student
        ├── Documents
        │   └── Linux
        │       └── Week1
        ├── Downloads
        └── Pictures
```

Start here:

```text
/home/student
```

### Enter Documents

```bash
cd Documents
```

Now:

```text
/home/student/Documents
```

### Enter Linux

```bash
cd Linux
```

Now:

```text
/home/student/Documents/Linux
```

### Enter Week1

```bash
cd Week1
```

Now:

```text
/home/student/Documents/Linux/Week1
```

### Go back one level

```bash
cd ..
```

Now:

```text
/home/student/Documents/Linux
```

### Go back two levels

```bash
cd ../..
```

Now:

```text
/home/student/Documents
```

### Go directly to Downloads

```bash
cd ../../Downloads
```

Starting from:

```text
/home/student/Documents
```

`../` → `/home/student`

then:

```text
Downloads
```

result:

```text
/home/student/Downloads
```

---

# 26. Navigating Using `~`

From anywhere in the filesystem:

```bash
cd ~/Documents
```

takes you to:

```text
/home/student/Documents
```

This is useful because you don't need to know your username or type the complete absolute path.

For example:

```bash
cd /home/student/Documents
```

can become:

```bash
cd ~/Documents
```

---

# 27. Navigating to the Root Directory

To go to the very top of the filesystem:

```bash
cd /
```

Then:

```bash
pwd
```

will show:

```text
/
```

You can see the main directories:

```bash
ls
```

Example:

```text
bin
boot
dev
etc
home
lib
media
mnt
opt
proc
root
run
sbin
tmp
usr
var
```

---

# 28. Understanding `/` in Different Situations

The `/` character can have different roles depending on where it appears.

### At the beginning

```text
/home/student
```

means an **absolute path** starting from root.

### Between directories

```text
/home/student/Documents
```

`/` separates directory names.

### Alone

```text
/
```

means the root directory.

### At the end

```text
/home/student/
```

The trailing `/` indicates that the path refers to a directory.

In many commands, the trailing slash is optional:

```bash
cd /home/student
```

and:

```bash
cd /home/student/
```

usually refer to the same directory.

---

# 29. `cd` Syntax Patterns

Here are the most useful patterns to remember:

```bash
cd DIRECTORY
```

Go into a directory relative to your current location.

```bash
cd /PATH/TO/DIRECTORY
```

Use an absolute path.

```bash
cd ../DIRECTORY
```

Go up one level, then enter a directory.

```bash
cd ../../DIRECTORY
```

Go up two levels, then enter a directory.

```bash
cd ~/DIRECTORY
```

Start from your home directory.

```bash
cd ..
```

Go up one level.

```bash
cd -
```

Return to the previous directory.

```bash
cd ~
```

Go home.

```bash
cd /
```

Go to the filesystem root.

---

# 30. A Simple Mental Model

When using `cd`, ask yourself:

### 1. Where am I?

```bash
pwd
```

### 2. What's around me?

```bash
ls
```

### 3. Do I want to go down?

Use the directory name:

```bash
cd Documents
```

### 4. Do I want to go up?

Use:

```bash
cd ..
```

### 5. Do I want to go up multiple levels?

Use:

```bash
cd ../..
```

### 6. Do I want to go home?

Use:

```bash
cd ~
```

or:

```bash
cd
```

### 7. Do I want to return to where I was?

Use:

```bash
cd -
```

### 8. Do I want to go somewhere regardless of where I currently am?

Use an absolute path:

```bash
cd /home/student/Documents
```

---

