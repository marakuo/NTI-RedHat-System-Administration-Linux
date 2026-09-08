# Managing Files from the Command Line

Linux provides several commands for creating, copying, moving, renaming, and deleting files and directories.

This chapter covers:

* Creating files and directories
* Copying and moving files
* Removing files and directories
* Useful command options such as `-r`, `-p`, `-v`, and `-i`
* Wildcards and globbing
* Hard links and symbolic (soft) links
* Inodes and how links work

---

# 1. Creating Directories — `mkdir`

The `mkdir` command creates directories.

```bash
mkdir directory_name
```

Example:

```bash
mkdir Projects
```

This creates:

```text
Projects/
```

You can create multiple directories at once:

```bash
mkdir Documents Downloads Pictures
```

Result:

```text
.
├── Documents
├── Downloads
└── Pictures
```

---

# 2. Creating Nested Directories — `mkdir -p`

Normally, if the parent directory doesn't exist:

```bash
mkdir Projects/Linux
```

may fail:

```text
mkdir: cannot create directory 'Projects/Linux': No such file or directory
```

You can use:

```bash
mkdir -p Projects/Linux
```

The `-p` option means:

> Create parent directories as necessary.

So:

```bash
mkdir -p Projects/Linux/Week1
```

creates everything:

```text
Projects/
└── Linux/
    └── Week1/
```

even if `Projects` and `Linux` did not previously exist.

### Why is `-p` useful?

Instead of:

```bash
mkdir Projects
mkdir Projects/Linux
mkdir Projects/Linux/Week1
```

you can simply use:

```bash
mkdir -p Projects/Linux/Week1
```

---

# 3. Creating Empty Files — `touch`

The `touch` command is commonly used to create an empty file.

```bash
touch file.txt
```

Now:

```text
file.txt
```

exists.

You can create multiple files:

```bash
touch file1.txt file2.txt file3.txt
```

Result:

```text
file1.txt
file2.txt
file3.txt
```

### Important

`touch` can also update a file's timestamps if the file already exists.

It does **not** normally erase the existing contents.

---

# 4. Copying Files — `cp`

The `cp` command copies files.

Basic syntax:

```bash
cp SOURCE DESTINATION
```

Example:

```bash
cp file.txt backup.txt
```

This creates a copy:

```text
file.txt
backup.txt
```

The original remains unchanged.

---

# 5. Copying a File Into a Directory

Suppose you have:

```text
file.txt
Documents/
```

You can copy the file into Documents:

```bash
cp file.txt Documents/
```

Result:

```text
Documents/
└── file.txt
```

The original file still exists.

---

# 6. Copying Multiple Files

You can copy several files into a directory:

```bash
cp file1.txt file2.txt file3.txt Documents/
```

Result:

```text
Documents/
├── file1.txt
├── file2.txt
└── file3.txt
```

---

# 7. Copying Directories — `cp -r`

By default, `cp` does not copy directories.

For example:

```bash
cp Documents Backup
```

may produce:

```text
cp: -r not specified; omitting directory 'Documents'
```

Use the recursive option:

```bash
cp -r Documents Backup
```

The `-r` means:

> **Recursive**

It tells `cp` to copy the directory and everything inside it.

Example:

```text
Documents/
├── Linux.txt
├── Cpp.txt
└── Projects/
    └── project1.cpp
```

Running:

```bash
cp -r Documents Backup
```

produces:

```text
Backup/
├── Linux.txt
├── Cpp.txt
└── Projects/
    └── project1.cpp
```

---

# 8. Verbose Mode — `-v`

The `-v` option means:

> **Verbose**

It makes a command tell you what it is doing.

Example:

```bash
cp -v file.txt backup.txt
```

You may see:

```text
'file.txt' -> 'backup.txt'
```

With directories:

```bash
cp -rv Documents Backup
```

you can see the files being copied.

`-v` is especially useful when you're learning or when you want to verify what a command actually did.

---

# 9. Interactive Mode — `-i`

The `-i` option means:

> **Interactive**

It asks for confirmation before overwriting or deleting certain files.

For example:

```bash
cp -i file.txt Documents/
```

If a file with the same name already exists, you may see:

```text
cp: overwrite 'Documents/file.txt'?
```

You can answer:

```text
y
```

or:

```text
n
```

This is useful when you want an extra safety check.

---

# 10. Combining Options

You can combine options.

For example:

```bash
cp -rvi Documents Backup
```

means:

```text
-r → recursive
-v → verbose
-i → interactive
```

So you are asking `cp` to:

> Recursively copy the directory, show what it is doing, and ask before overwriting files.

Options can often also be written separately:

```bash
cp -r -v -i Documents Backup
```

Both forms are commonly accepted.

---

# 11. Moving Files — `mv`

The `mv` command moves files and directories.

Basic syntax:

```bash
mv SOURCE DESTINATION
```

Example:

```bash
mv file.txt Documents/
```

The file is moved from the current directory into `Documents`.

Unlike `cp`, the original location no longer contains the file.

---

# 12. `mv` Is Also Used for Renaming

Linux does not have a separate `rename` command for the basic case.

You can rename a file with:

```bash
mv oldname.txt newname.txt
```

Example:

```bash
mv notes.txt linux_notes.txt
```

The file has been renamed.

The same idea works for directories:

```bash
mv old_folder new_folder
```

For example:

```bash
mv S1 Session1
```

renames:

```text
S1/
```

to:

```text
Session1/
```

---

# 13. Moving a Directory

You can move a directory without using `-r`:

```bash
mv Documents /tmp/
```

`mv` handles directories directly.

For example:

```text
Before:

.
├── Documents/
└── Downloads/
```

Run:

```bash
mv Documents Downloads/
```

Result:

```text
Downloads/
└── Documents/
```

---

# 14. Removing Files — `rm`

The `rm` command removes files.

```bash
rm file.txt
```

The file is deleted.

### Be careful

Linux does not normally provide a recycle bin for `rm`.

When you run:

```bash
rm file.txt
```

you should assume the file is immediately removed.

---

# 15. Interactive Remove — `rm -i`

Use:

```bash
rm -i file.txt
```

Linux asks for confirmation:

```text
rm: remove regular file 'file.txt'?
```

You can answer:

```text
y
```

or:

```text
n
```

This is useful when deleting important files.

---

# 16. Removing Directories — `rm -r`

`rm` normally cannot remove a directory containing files.

For example:

```bash
rm Documents
```

may give:

```text
rm: cannot remove 'Documents': Is a directory
```

Use:

```bash
rm -r Documents
```

The `-r` option recursively removes the directory and its contents.

For example:

```text
Documents/
├── file1.txt
├── file2.txt
└── Projects/
    └── code.cpp
```

Running:

```bash
rm -r Documents
```

removes:

```text
Documents/
```

and everything inside it.

### ⚠️ Be careful with `rm -r`

Recursive deletion can remove a large amount of data.

Always check where you are:

```bash
pwd
```

and what you're deleting:

```bash
ls
```

before using it.

---

# 17. `rm -ri`

You can combine recursive and interactive modes:

```bash
rm -ri Documents
```

This means:

```text
-r → recursively remove
-i → ask for confirmation
```

This is much safer for practicing recursive deletion.

---

# 18. Removing Empty Directories — `rmdir`

The `rmdir` command removes **empty directories**.

```bash
rmdir directory
```

Example:

```bash
mkdir Test
rmdir Test
```

The directory is removed.

But if the directory contains a file:

```text
Test/
└── file.txt
```

then:

```bash
rmdir Test
```

will fail.

You may see:

```text
rmdir: failed to remove 'Test': Directory not empty
```

In that case, you could remove the contents first or use:

```bash
rm -r Test
```

---

# 19. `rmdir` vs `rm -r`

| Command            | What it removes                                 |
| ------------------ | ----------------------------------------------- |
| `rmdir directory`  | Empty directory only                            |
| `rm file`          | File                                            |
| `rm -r directory`  | Directory and its contents                      |
| `rm -ri directory` | Directory and contents, asking for confirmation |

A useful rule:

```text
rmdir → empty directories
rm -r → directories containing things
```

---

# 20. Wildcards and Globbing

Linux shells support **pathname expansion**, commonly called **globbing**.

Globbing lets you use patterns instead of typing every filename manually.

The most common patterns are:

```text
*
?
[]
{}
```

These are especially useful with commands such as:

```bash
ls
cp
mv
rm
```

---

# 21. `*` — Matches Anything

The `*` wildcard matches **zero or more characters**.

Suppose you have:

```text
file1.txt
file2.txt
file3.txt
notes.txt
image.png
```

Run:

```bash
ls *.txt
```

The shell expands it to something similar to:

```bash
ls file1.txt file2.txt notes.txt
```

So only `.txt` files are selected.

---

## `*` at the beginning

```bash
ls *.txt
```

means:

> Anything ending in `.txt`

Matches:

```text
file.txt
notes.txt
linux.txt
```

---

## `*` at the end

```bash
ls file*
```

could match:

```text
file.txt
file1.txt
file2.txt
file_backup
```

---

## `*` in the middle

```bash
ls file*.txt
```

could match:

```text
file.txt
file1.txt
file2.txt
file_backup.txt
```

---

# 22. Example: Files Containing `1`

Suppose:

```text
file1.txt
file2.txt
linux1.pdf
notes.txt
project10.cpp
```

You can use:

```bash
ls *1*
```

This means:

> Match anything containing `1`.

Possible matches:

```text
file1.txt
linux1.pdf
project10.cpp
```

This is useful when you don't know the complete filename.

---

# 23. `?` — Exactly One Character

The `?` wildcard matches **exactly one character**.

Suppose:

```text
file1.txt
file2.txt
file10.txt
file20.txt
```

Then:

```bash
ls file?.txt
```

matches:

```text
file1.txt
file2.txt
```

but not:

```text
file10.txt
file20.txt
```

because `?` represents exactly one character.

---

# 24. `[]` — Character Sets

Square brackets let you specify a set or range of characters.

Suppose:

```text
file1.txt
file2.txt
file3.txt
file4.txt
file5.txt
```

You can select files 1, 2, and 3:

```bash
ls file[123].txt
```

This matches:

```text
file1.txt
file2.txt
file3.txt
```

---

## Ranges

You can specify a range:

```bash
ls file[1-3].txt
```

This means:

```text
1 through 3
```

So it matches:

```text
file1.txt
file2.txt
file3.txt
```

You can also use letters:

```bash
ls [a-c]*
```

This matches names beginning with:

```text
a
b
c
```

---

# 25. `{}` — Brace Expansion

Brace expansion is slightly different from globbing.

It generates multiple strings based on the values inside `{}`.

For example:

```bash
echo file{1,2,3}.txt
```

produces:

```text
file1.txt file2.txt file3.txt
```

You can use it with commands.

For example:

```bash
touch file{1,2,3}.txt
```

creates:

```text
file1.txt
file2.txt
file3.txt
```

You can also use ranges:

```bash
touch file{1..5}.txt
```

creates:

```text
file1.txt
file2.txt
file3.txt
file4.txt
file5.txt
```

### Important difference

```text
* ? [] → pattern matching / globbing
{}     → brace expansion
```

Brace expansion happens before pathname expansion.

---

# 26. Wildcard Cheat Sheet

| Pattern  | Meaning                    | Example          |
| -------- | -------------------------- | ---------------- |
| `*`      | Zero or more characters    | `*.txt`          |
| `?`      | Exactly one character      | `file?.txt`      |
| `[abc]`  | One character from the set | `file[123].txt`  |
| `[a-z]`  | One character in a range   | `[a-c]*`         |
| `{a,b}`  | Generate alternatives      | `file{1,2}.txt`  |
| `{1..5}` | Generate a sequence        | `file{1..5}.txt` |

---

# 27. Using Globbing With `cp`

Suppose:

```text
file1.txt
file2.txt
file3.txt
image.png
```

You want to copy all `.txt` files:

```bash
cp *.txt Documents/
```

The shell expands:

```text
*.txt
```

into:

```text
file1.txt file2.txt file3.txt
```

so `cp` effectively receives:

```bash
cp file1.txt file2.txt file3.txt Documents/
```

---

# 28. Using Globbing With `rm`

Suppose:

```text
test1.txt
test2.txt
test3.txt
notes.txt
```

You can remove the test files:

```bash
rm test*.txt
```

This matches:

```text
test1.txt
test2.txt
test3.txt
```

### ⚠️ Be careful

Before using `rm` with a wildcard, check what the pattern matches:

```bash
ls test*.txt
```

Then delete:

```bash
rm test*.txt
```

This is a good habit.

---

# 29. Why Did `cp *1*` Fail Sometimes?

Suppose you run:

```bash
cp *1* .
```

and get:

```text
cp: cannot stat '*1*': No such file or directory
```

This usually means the shell could not find any filename in the current directory matching:

```text
*1*
```

So the pattern was not expanded.

First check:

```bash
pwd
```

Then:

```bash
ls
```

Then:

```bash
ls *1*
```

If there are no matching files, `*1*` has nothing to expand to.

Also remember:

> Globbing normally matches entries in the **current directory** unless you specify a path.

---

# 30. Links in Linux

Linux allows multiple directory entries to refer to the same underlying file.

There are two important types:

1. **Hard links**
2. **Symbolic links (soft links)**

To understand them, you need to understand **inodes**.

---

# 31. What Is an Inode?

An **inode** is a data structure used by a Linux filesystem to store information about a file.

An inode can contain information such as:

* File type
* Permissions
* Owner
* Group
* File size
* Timestamps
* References to the file's data blocks

The inode does **not** normally store the filename itself.

Think of it like this:

```text
Filename
   │
   ▼
Directory Entry
   │
   ▼
Inode
   │
   ▼
File Data
```

The filename is associated with an inode.

---

# 32. A Simple Inode Analogy

Imagine a library.

The book is the actual data.

The library's internal record is the inode.

The label on a shelf is the filename.

You can have two labels pointing to the same internal record:

```text
file1
   │
   ▼
[ inode 1234 ]
   │
   ▼
  DATA

file2
   │
   └───────► [ inode 1234 ]
```

Both names refer to the same underlying file.

That is the basic idea behind a **hard link**.

---

# 33. Creating a Hard Link — `ln`

Basic syntax:

```bash
ln ORIGINAL LINK_NAME
```

Example:

```bash
touch original.txt
ln original.txt hardlink.txt
```

Now:

```text
original.txt
hardlink.txt
```

both refer to the same inode.

You can verify this using:

```bash
ls -li
```

You might see:

```text
123456 -rw-r--r-- 2 student student 0 Sep 8 15:00 hardlink.txt
123456 -rw-r--r-- 2 student student 0 Sep 8 15:00 original.txt
```

Notice the same inode number:

```text
123456
```

That tells you both directory entries refer to the same inode.

---

# 34. Hard Link Behavior

Suppose:

```text
original.txt ──┐
               ├──> inode 1234 ──> data
hardlink.txt ──┘
```

If you modify:

```text
original.txt
```

the change is visible through:

```text
hardlink.txt
```

because they refer to the same underlying data.

---

# 35. What Happens When You Delete the Original?

Suppose:

```text
original.txt ──┐
               ├──> inode 1234 ──> data
hardlink.txt ──┘
```

You run:

```bash
rm original.txt
```

The data does **not** disappear because:

```text
hardlink.txt
```

still points to the inode.

Now:

```text
hardlink.txt ──> inode 1234 ──> data
```

The file's data remains accessible through the hard link.

---

# 36. Link Count

When you run:

```bash
ls -l
```

you may see something like:

```text
-rw-r--r-- 2 student student 0 Sep 8 15:00 original.txt
```

The number:

```text
2
```

is the **hard-link count**.

If there are two directory entries pointing to the inode:

```text
original.txt
hardlink.txt
```

the link count is typically:

```text
2
```

After removing one:

```bash
rm original.txt
```

the count becomes:

```text
1
```

The inode and data are eventually released when the link count reaches zero and no process is still using the file.

---

# 37. Symbolic Links — Soft Links

A symbolic link, or **symlink**, is different.

Create one using:

```bash
ln -s ORIGINAL LINK_NAME
```

Example:

```bash
touch original.txt
ln -s original.txt softlink.txt
```

Now:

```text
softlink.txt → original.txt
```

A symbolic link contains a **path** to another file rather than being another directory entry pointing directly to the same inode.

---

# 38. Visualizing a Symbolic Link

Hard link:

```text
original.txt ──┐
               ├──> inode 1234 ──> DATA
hardlink.txt ──┘
```

Symbolic link:

```text
original.txt ──> inode 1234 ──> DATA

softlink.txt ──> "original.txt"
```

The symlink has its **own inode**.

---

# 39. Identifying a Symbolic Link

Run:

```bash
ls -l
```

You might see:

```text
-rw-r--r-- 1 student student 10 Sep 8 15:00 original.txt
lrwxrwxrwx 1 student student 12 Sep 8 15:01 softlink.txt -> original.txt
```

The first character:

```text
l
```

means:

> Symbolic link

And:

```text
softlink.txt -> original.txt
```

shows where the link points.

---

# 40. What Happens If You Delete the Original?

Suppose:

```text
original.txt ──> DATA

softlink.txt ──> original.txt
```

Delete:

```bash
rm original.txt
```

Now:

```text
softlink.txt ──> original.txt
                         X
                    doesn't exist
```

The symbolic link becomes a **broken/dangling symlink**.

Unlike a hard link, it does not keep the original file's data alive.

---

# 41. Hard Link vs Symbolic Link

| Feature                     | Hard Link         | Symbolic Link             |
| --------------------------- | ----------------- | ------------------------- |
| Created with                | `ln`              | `ln -s`                   |
| Points to                   | Same inode        | A pathname                |
| Own inode?                  | No, shares inode  | Yes                       |
| Same inode number?          | Yes               | No                        |
| Can cross filesystems?      | Usually no        | Yes                       |
| Can link to directories?    | Generally no      | Yes                       |
| Survives original deletion? | Yes               | No                        |
| Can become dangling?        | No                | Yes                       |
| Can point to another link?  | Directly to inode | Can point to another path |

---

# 42. Why Hard Links Usually Cannot Cross Filesystems

An inode belongs to a particular filesystem.

For example:

```text
Filesystem A
    inode 1234
```

and:

```text
Filesystem B
    inode 1234
```

are not the same inode.

Therefore, a hard link generally cannot point to an inode located on another filesystem.

For example:

```bash
ln /home/student/file.txt /mnt/usb/file.txt
```

may fail if `/home` and `/mnt/usb` are different filesystems.

A symbolic link can cross filesystem boundaries because it stores a **path**:

```text
/mnt/usb/file.txt
```

rather than directly referencing the inode.

---

# 43. Hard Links and Directories

Hard links to directories are generally prohibited for ordinary users.

Why?

Because allowing arbitrary directory hard links could create complicated filesystem cycles.

For example:

```text
A
└── B
    └── A
        └── B
            └── ...
```

This would make filesystem traversal problematic.

Instead, symbolic links can point to directories:

```bash
ln -s Documents Docs
```

Now:

```text
Docs → Documents
```

---

# 44. Checking Inodes With `ls -i`

Use:

```bash
ls -i
```

to display inode numbers.

Example:

```bash
touch file.txt
ln file.txt hard.txt
ln -s file.txt soft.txt
```

Then:

```bash
ls -li
```

might show:

```text
12345 -rw-r--r-- 2 student student 0 file.txt
12345 -rw-r--r-- 2 student student 0 hard.txt
12346 lrwxrwxrwx 1 student student 8 soft.txt -> file.txt
```

Notice:

```text
file.txt  → inode 12345
hard.txt  → inode 12345
soft.txt  → inode 12346
```

This demonstrates the fundamental difference.

---

# 45. A Complete Example

Let's create a small practice environment:

```bash
mkdir -p ~/linux-links
cd ~/linux-links
```

Create a file:

```bash
touch original.txt
```

Create a hard link:

```bash
ln original.txt hard.txt
```

Create a symbolic link:

```bash
ln -s original.txt soft.txt
```

Now:

```bash
ls -li
```

You should see that:

```text
original.txt
hard.txt
```

have the same inode number, while:

```text
soft.txt
```

has a different inode.

You can inspect the symbolic link with:

```bash
ls -l soft.txt
```

---

# 46. Command Options Cheat Sheet

## `mkdir`

```bash
mkdir directory
mkdir -p a/b/c
```

```text
-p → create parent directories
```

---

## `cp`

```bash
cp file destination
cp -r directory destination
cp -v file destination
cp -i file destination
```

```text
-r → recursive
-v → verbose
-i → interactive
```

---

## `mv`

```bash
mv old new
```

Used for:

```text
moving
renaming
```

---

## `rm`

```bash
rm file
rm -r directory
rm -i file
rm -ri directory
```

```text
-r → recursive
-i → interactive
```

---

## `rmdir`

```bash
rmdir directory
```

Removes empty directories.

---

## `touch`

```bash
touch file.txt
```

Creates an empty file if it doesn't exist.

---

## `ln`

Hard link:

```bash
ln original hardlink
```

Symbolic link:

```bash
ln -s original symlink
```

---

# 47. The Most Important Concepts

### Creating

```text
mkdir → directories
touch → files
```

### Copying

```text
cp → copy files/directories
```

### Moving/Renaming

```text
mv → move or rename
```

### Removing

```text
rm → remove files
rm -r → remove directories recursively
rmdir → remove empty directories
```

### Wildcards

```text
*     → zero or more characters
?     → exactly one character
[]    → character set/range
{}    → brace expansion
```

### Links

```text
ln     → hard link
ln -s  → symbolic link
```

### Inodes

```text
Hard links → same inode
Symlinks   → different inode + path to target
```

---

# 48. Final Mental Model

Think about Linux files like this:

```text
                 DIRECTORY
                     │
              filename / name
                     │
                     ▼
                   INODE
                     │
              ┌──────┴──────┐
              │             │
          metadata       file data
```

A hard link gives another name to the **same inode**:

```text
name1 ──┐
        ├──> inode ──> DATA
name2 ──┘
```

A symbolic link creates a separate file containing a path:

```text
name1 ──> inode ──> DATA

name2 ──> "name1"
```

That explains why:

* Hard links share the same inode.
* Hard links continue working after the original filename is removed.
* Symbolic links have their own inode.
* Symbolic links can become broken.
* Hard links generally cannot cross filesystems.
* Symbolic links can cross filesystems.

---

# Quick Practice

Try these commands in a safe practice directory:

```bash
mkdir -p ~/linux-practice/files
cd ~/linux-practice/files

touch file1.txt file2.txt file3.txt
mkdir Documents

cp *.txt Documents/

ls Documents/

mv file1.txt renamed.txt

ln renamed.txt hardlink.txt
ln -s renamed.txt softlink.txt

ls -li

rm -i hardlink.txt
rmdir Documents
```

Before using recursive deletion, always check:

```bash
pwd
ls
```

And when using wildcards with `rm`, first check what will match:

```bash
ls *.txt
```

Then, if the result is what you expect:
rm *.txt

This habit prevents many accidental deletions.

```bash
rm *.txt
```

This habit prevents many accidental deletions.
