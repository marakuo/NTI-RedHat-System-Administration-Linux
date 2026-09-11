# Controlling Access to Files

> **Red Hat Enterprise Linux (RHEL) — Linux System Administration**
>
> This chapter explains how Linux controls access to files and directories using **permissions, ownership, and `umask`**.

---

## 1. Why File Permissions Matter

Linux is a multi-user operating system.

Many users and processes can access the same system, so Linux needs a mechanism to control:

* Who can read a file
* Who can modify a file
* Who can execute a file
* Who owns a file
* Which group is associated with a file
* What permissions new files and directories receive by default

Linux uses three main concepts:

```text
Ownership
    +
Permissions
    +
Default permissions (`umask`)
```

The main commands used in this chapter are:

```bash
ls
chmod
chown
chgrp
umask
```

---

# 2. The Linux File Permission Model

Linux permissions are divided into three **permission classes**:

```text
User      → u
Group     → g
Other     → o
```

And there are three basic permissions:

```text
Read       → r
Write      → w
Execute    → x
```

This gives us:

```text
        Permissions
       /     |      \
    User   Group   Other
     u       g       o
```

For example:

```text
-rwxr-xr--
```

The permissions can be separated into:

```text
User       Group      Other
rwx        r-x        r--
```

---

# 3. Understanding `r`, `w`, and `x`

## Read — `r`

Read permission allows a user to read the contents of a file.

For a regular file:

```text
r → read the contents
```

Example:

```bash
cat file.txt
```

If you do not have read permission, you generally cannot read the file's contents.

---

## Write — `w`

Write permission allows modification of a file's contents.

For example:

```bash
echo "Hello" > file.txt
```

requires the ability to modify the file.

Write permission does **not** automatically mean that you can read the file.

For example, theoretically:

```text
-w-
```

means the file can be modified without granting read permission.

---

## Execute — `x`

For a regular file, execute permission allows the file to be executed as a program or script when otherwise appropriate.

Example:

```bash
./script.sh
```

A script might have:

```text
-rwxr-xr-x
```

where the `x` permissions allow execution.

---

# 4. Permissions on Directories

The meaning of `r`, `w`, and `x` changes slightly when applied to a directory.

### Read (`r`) on a directory

Allows you to list the names of entries inside the directory.

For example:

```bash
ls directory/
```

---

### Write (`w`) on a directory

Allows you to create, delete, and rename entries inside the directory, subject to other permission and system restrictions.

For example:

```bash
touch directory/newfile
```

---

### Execute (`x`) on a directory

Allows you to **access/traverse** the directory.

For example:

```bash
cd directory/
```

and access files inside it when the relevant permissions allow it.

A useful way to remember directory permissions:

```text
r → see the directory's contents
w → modify the directory's entries
x → enter/traverse the directory
```

---

# 5. The Three Permission Classes

Every file has permissions for:

```text
User
Group
Other
```

## User — `u`

The **user/owner** is the account that owns the file.

Example:

```text
-rwx------
```

The owner has:

```text
rwx
```

---

## Group — `g`

The group permissions apply to users who are members of the file's associated group.

Example:

```text
----rwx---
```

The group has:

```text
rwx
```

---

## Other — `o`

"Other" means users who are neither:

* the file owner
* nor members of the file's associated group

Example:

```text
-------rwx
```

These users have:

```text
rwx
```

---

# 6. Reading `ls -l`

Use:

```bash
ls -l
```

Example:

```text
-rw-r--r--. 1 student student 120 Sep 11 19:20 file.txt
```

Let's break it down:

```text
-rw-r--r--.
││  │  │
││  │  └── Other permissions
││  └───── Group permissions
│└──────── User permissions
└───────── File type
```

The complete permission part is:

```text
-rw-r--r--
```

Break it into:

```text
-   rw-   r--   r--
│    │     │     │
│    │     │     └── Other
│    │     └──────── Group
│    └────────────── User
└─────────────────── File type
```

Therefore:

```text
User   → rw-
Group  → r--
Other  → r--
```

---

# 7. The First Character — File Type

The first character in `ls -l` output indicates the file type.

Common examples:

```text
-    regular file
d    directory
l    symbolic link
```

For example:

```text
-rw-r--r--
```

starts with:

```text
-
```

so it is a regular file.

A directory might look like:

```text
drwxr-xr-x
```

The first character is:

```text
d
```

---

# 8. Permission Representation

Permissions can be represented in two major ways:

### Symbolic

```text
rwxr-xr--
```

### Numeric / Octal

```text
754
```

Both represent the same permission model.

---

# 9. Permission Values

For numeric permissions:

| Permission    | Value |
| ------------- | ----: |
| `r`           |     4 |
| `w`           |     2 |
| `x`           |     1 |
| no permission |     0 |

We add the values together.

### Examples

```text
r-- = 4
-w- = 2
--x = 1
```

For multiple permissions:

```text
rw- = 4 + 2 = 6
r-x = 4 + 1 = 5
rwx = 4 + 2 + 1 = 7
```

---

# 10. Converting Permissions to Octal

Suppose we have:

```text
rwxr-xr--
```

Separate it:

```text
rwx   r-x   r--
```

Calculate each group:

```text
rwx = 4 + 2 + 1 = 7

r-x = 4 + 0 + 1 = 5

r-- = 4 + 0 + 0 = 4
```

Therefore:

```text
rwxr-xr-- = 754
```

---

# 11. Common Permission Values

| Numeric | Symbolic | Meaning                |
| ------: | -------- | ---------------------- |
|     `7` | `rwx`    | Read + Write + Execute |
|     `6` | `rw-`    | Read + Write           |
|     `5` | `r-x`    | Read + Execute         |
|     `4` | `r--`    | Read only              |
|     `3` | `-wx`    | Write + Execute        |
|     `2` | `-w-`    | Write only             |
|     `1` | `--x`    | Execute only           |
|     `0` | `---`    | No permissions         |

---

# 12. `chmod`

`chmod` means **change mode**.

It changes the permissions of files and directories.

Basic syntax:

```bash
chmod [OPTIONS] MODE FILE
```

Example:

```bash
chmod 755 script.sh
```

---

# 13. `chmod` — Symbolic Mode

Symbolic mode uses:

```text
u → user
g → group
o → other
a → all
```

And:

```text
+ → add permission
- → remove permission
= → set exact permissions
```

---

## Add a Permission

Give the owner execute permission:

```bash
chmod u+x script.sh
```

Give the group write permission:

```bash
chmod g+w file.txt
```

Give others read permission:

```bash
chmod o+r file.txt
```

---

## Remove a Permission

Remove write permission from the group:

```bash
chmod g-w file.txt
```

Remove execute permission from others:

```bash
chmod o-x script.sh
```

---

## Set Exact Permissions

You can use `=` to replace the existing permissions for a class.

Example:

```bash
chmod u=rwx file.txt
```

The owner's permissions become exactly:

```text
rwx
```

Another example:

```bash
chmod g=rx file.txt
```

The group's permissions become:

```text
r-x
```

---

# 14. `chmod` with Multiple Classes

You can modify multiple classes in one command.

Example:

```bash
chmod u+x,g+w file.txt
```

This:

```text
adds execute to user
adds write to group
```

Another example:

```bash
chmod u=rwx,g=rx,o=r file.txt
```

This results in:

```text
rwxr-xr--
```

---

# 15. `chmod a`

`a` means **all**:

```text
u + g + o
```

Example:

```bash
chmod a+r file.txt
```

This gives:

```text
user   → read
group  → read
other  → read
```

Another example:

```bash
chmod a-x script.sh
```

removes execute permission from all three classes.

---

# 16. `chmod` — Numeric Mode

Instead of symbolic notation, you can use octal numbers.

Example:

```bash
chmod 755 script.sh
```

This means:

```text
7 → user
5 → group
5 → other
```

Convert:

```text
7 = rwx
5 = r-x
5 = r-x
```

Therefore:

```text
755 = rwxr-xr-x
```

---

# 17. Common `chmod` Examples

## `chmod 644`

```bash
chmod 644 file.txt
```

Results in:

```text
rw-r--r--
```

Meaning:

```text
User   → read + write
Group  → read
Other  → read
```

This is a common permission set for ordinary files.

---

## `chmod 600`

```bash
chmod 600 private.txt
```

Results in:

```text
rw-------
```

Only the owner can read and write.

---

## `chmod 755`

```bash
chmod 755 script.sh
```

Results in:

```text
rwxr-xr-x
```

The owner can read, write, and execute.

The group and others can read and execute.

---

## `chmod 700`

```bash
chmod 700 private_script.sh
```

Results in:

```text
rwx------
```

Only the owner has permissions.

---

# 18. `chmod` on Directories

Permissions can also be changed on directories.

Example:

```bash
chmod 755 mydir
```

Check the result:

```bash
ls -ld mydir
```

Notice the use of:

```bash
ls -ld
```

instead of:

```bash
ls -l
```

because `-d` tells `ls` to display the directory itself rather than its contents.

---

# 19. Recursive Permissions

`chmod` can operate recursively using:

```bash
-R
```

Example:

```bash
chmod -R 755 project/
```

This applies the permission change throughout the directory tree.

### Important

Be careful with:

```bash
chmod -R
```

because it changes many files and directories at once.

For example, blindly making every item under a directory executable can produce inappropriate permissions for regular files.

Use recursive permission changes deliberately.

---

# 20. File Ownership

Permissions are only one part of access control.

Every file also has:

```text
User owner
Group owner
```

Example:

```text
-rw-r--r--. 1 student developers 120 Sep 11 19:20 file.txt
```

Here:

```text
User owner  → student
Group owner → developers
```

You can see ownership using:

```bash
ls -l
```

---

# 21. `chown`

`chown` means **change owner**.

It changes the user owner of a file.

Basic syntax:

```bash
chown USER FILE
```

Example:

```bash
chown student file.txt
```

This changes the owner to:

```text
student
```

---

# 22. Changing User and Group Together

You can change both owner and group using:

```bash
chown USER:GROUP FILE
```

Example:

```bash
chown student:developers project.txt
```

This sets:

```text
User owner  → student
Group owner → developers
```

You may also see:

```bash
chown student.developers project.txt
```

but the `user:group` form is generally clearer and commonly used.

---

# 23. Changing Only the Group with `chown`

You can also change only the group using:

```bash
chown :developers file.txt
```

This leaves the user owner unchanged.

---

# 24. Recursive `chown`

You can change ownership recursively:

```bash
chown -R student:developers project/
```

This changes ownership throughout the directory tree.

Again, use `-R` carefully because it can affect many files.

---

# 25. `chgrp`

`chgrp` means **change group**.

It changes the group owner of a file.

Basic syntax:

```bash
chgrp GROUP FILE
```

Example:

```bash
chgrp developers project.txt
```

Now the group owner becomes:

```text
developers
```

The user owner remains unchanged.

---

# 26. `chgrp` vs `chown`

### `chown`

Primarily used to change the user owner:

```bash
chown student file.txt
```

It can also change both:

```bash
chown student:developers file.txt
```

### `chgrp`

Used specifically to change the group:

```bash
chgrp developers file.txt
```

Think:

```text
chown → change ownership
chgrp → change group
```

---

# 27. Ownership + Permissions Together

Consider:

```text
-rwxr-x---. 1 alice developers 500 Sep 11 20:00 script.sh
```

We can read it as:

```text
Owner: alice
Group: developers

User permissions:
rwx

Group permissions:
r-x

Other permissions:
---
```

Therefore:

```text
alice
→ read + write + execute

members of developers
→ read + execute

everyone else
→ no permissions
```

This is the key relationship:

```text
                File
                 │
        ┌────────┴────────┐
        │                 │
     Ownership        Permissions
        │                 │
   ┌────┴────┐      ┌─────┼─────┐
   │         │      │     │     │
 User      Group    u     g     o
```

---

# 28. Permission Checking Order

When a user accesses a file, Linux determines which permission class applies to that user.

The important classes are:

```text
User/owner
Group
Other
```

A user's membership in the file's group matters when determining access.

For basic file-permission reasoning, think:

```text
Is the user the owner?
        ↓
       Yes
        ↓
Use USER permissions

If not:
Is the user a member of the file's group?
        ↓
       Yes
        ↓
Use GROUP permissions

If neither:
Use OTHER permissions
```

Do not simply combine all three sets of permissions.

---

# 29. Default File Permissions

When you create a new file or directory, Linux assigns initial permissions.

For example:

```bash
touch file.txt
```

and:

```bash
mkdir directory
```

The exact permissions depend on:

1. The program's requested/default mode
2. The user's `umask`

The `umask` removes permissions from the permissions initially requested for newly created files and directories.

---

# 30. `umask`

`umask` stands for **user file-creation mode mask**.

It controls which permissions are removed from newly created files and directories.

Check the current value:

```bash
umask
```

Example:

```text
0022
```

You may also use:

```bash
umask -S
```

to display the mask symbolically.

Example:

```text
u=rwx,g=rx,o=rx
```

---

# 31. How `umask` Works

A simplified way to understand it is:

```text
Requested/default permissions
             -
           umask
             ↓
      resulting permissions
```

For example, a common `umask` is:

```text
022
```

For a newly created regular file, the initial maximum is commonly:

```text
666
```

because regular files are not normally created with execute permission by default.

Apply:

```text
666
-022
----
644
```

Result:

```text
rw-r--r--
```

So:

```bash
touch file.txt
```

may produce:

```text
-rw-r--r--
```

with a `022` umask.

---

# 32. Directories and `umask`

Directories commonly start from:

```text
777
```

before applying the `umask`.

With:

```text
umask 022
```

we get:

```text
777
-022
----
755
```

Therefore a newly created directory may have:

```text
drwxr-xr-x
```

---

# 33. Why Files Start from `666` and Directories from `777`

The important distinction is execute permission.

Regular files are generally created with a base permission of:

```text
666
```

which means:

```text
rw-rw-rw-
```

before the `umask` is applied.

Directories use:

```text
777
```

which includes execute permission because directory traversal requires it.

The final permissions are then affected by the `umask`.

---

# 34. Setting `umask`

You can temporarily change the current shell's `umask`:

```bash
umask 027
```

Check it:

```bash
umask
```

You should see:

```text
0027
```

Now create a file:

```bash
touch test.txt
```

Check:

```bash
ls -l test.txt
```

With the usual regular-file base mode:

```text
666
-027
----
640
```

So the result is typically:

```text
rw-r-----
```

---

# 35. Symbolic `umask`

You can also set `umask` symbolically.

For example:

```bash
umask u=rwx,g=rx,o=
```

This means the mask is represented as:

```text
User   → rwx
Group  → r-x
Other  → ---
```

Symbolic form can make the intended mask easier to understand.

---

# 36. User vs System `umask`

A user's `umask` can be configured so it is applied to their shell sessions.

System-wide configuration can also establish defaults for users.

Common configuration locations on RHEL systems can include:

```text
/etc/profile
/etc/bashrc
```

and user-specific shell startup files such as:

```text
~/.bash_profile
~/.bashrc
```

The exact effective setting can depend on how the shell/session is initialized and on system configuration.

### Important distinction

```text
System configuration
        ↓
can establish defaults

User configuration
        ↓
can establish user-specific behavior
```

Always check the actual value in your current shell:

```bash
umask
```

---

# 37. Temporary vs Persistent `umask`

If you run:

```bash
umask 027
```

in your current shell, it affects that shell and processes started from it.

It does not necessarily become a permanent setting for every future login.

To make a user-specific setting persistent, it is commonly placed in an appropriate shell startup configuration file.

For example:

```bash
~/.bashrc
```

or:

```bash
~/.bash_profile
```

depending on the shell/session configuration.

System-wide defaults may be configured in files such as:

```bash
/etc/profile
/etc/bashrc
```

---

# 38. Practical Example — Creating a Private File

Start by checking your current `umask`:

```bash
umask
```

Suppose:

```text
0022
```

Create a file:

```bash
touch private.txt
```

Check it:

```bash
ls -l private.txt
```

You may get:

```text
-rw-r--r--. 1 student student 0 Sep 11 20:30 private.txt
```

Now make it private:

```bash
chmod 600 private.txt
```

Check again:

```bash
ls -l private.txt
```

Result:

```text
-rw-------. 1 student student 0 Sep 11 20:30 private.txt
```

Now:

```text
User   → read + write
Group  → no permissions
Other  → no permissions
```

---

# 39. Practical Example — Making a Script Executable

Create a script:

```bash
touch script.sh
```

Give the owner execute permission:

```bash
chmod u+x script.sh
```

Check:

```bash
ls -l script.sh
```

You should see an `x` in the owner's permissions.

You can also use:

```bash
chmod 700 script.sh
```

which gives:

```text
rwx------
```

---

# 40. Practical Example — Shared Group File

Suppose a project is shared by members of the `developers` group.

Create:

```bash
touch project.txt
```

Change the group:

```bash
chgrp developers project.txt
```

Set permissions:

```bash
chmod 660 project.txt
```

Now:

```text
User   → rw-
Group  → rw-
Other  → ---
```

This means:

```text
Owner
→ read + write

Developers group
→ read + write

Everyone else
→ no access
```

This is a common pattern for group collaboration.

---

# 41. Practical Permission Investigation Workflow

When you cannot access a file, do not immediately start changing permissions.

First investigate.

### Step 1 — Check the file

```bash
ls -l file.txt
```

### Step 2 — Identify the owner and group

Look at:

```text
owner
group
```

### Step 3 — Read the permissions

Separate:

```text
user
group
other
```

### Step 4 — Determine which class applies to you

Ask:

```text
Am I the owner?
        ↓
If no:
Am I a member of the file's group?
        ↓
If no:
I fall under "other".
```

### Step 5 — Change only what is necessary

Use:

```bash
chmod
chown
chgrp
```

as appropriate.

---

# 42. Useful Commands for Checking Permissions

### List permissions

```bash
ls -l
```

### List a directory's own permissions

```bash
ls -ld directory/
```

### Change permissions

```bash
chmod
```

### Change user owner

```bash
chown
```

### Change group owner

```bash
chgrp
```

### Check `umask`

```bash
umask
```

### Check symbolic `umask`

```bash
umask -S
```

---

# 43. Common Permission Mistakes

## Mistake 1 — Thinking `x` means "execute" for everything

For regular files:

```text
x → execute
```

For directories:

```text
x → traverse/access
```

---

## Mistake 2 — Thinking directory `w` alone is enough

A directory usually needs appropriate `x` permission as well to access/traverse it.

Remember:

```text
directory:
r → list
w → modify entries
x → traverse
```

---

## Mistake 3 — Using `chmod 777` for everything

You may see people recommend:

```bash
chmod 777 file
```

as a quick fix.

This gives:

```text
rwxrwxrwx
```

to everyone.

That is generally far more permission than necessary.

Instead, determine what access is actually required and grant only that.

---

## Mistake 4 — Confusing ownership with permissions

Changing:

```bash
chown
```

does not directly change the permission bits.

Changing:

```bash
chmod
```

does not change the owner or group.

They control different things:

```text
chown  → ownership
chgrp  → group ownership
chmod  → permissions
```

---

# 44. Quick Reference

| Command            | Purpose                          | Example                    |
| ------------------ | -------------------------------- | -------------------------- |
| `ls -l`            | View permissions and ownership   | `ls -l file.txt`           |
| `ls -ld`           | View directory's own permissions | `ls -ld project/`          |
| `chmod`            | Change permissions               | `chmod 644 file.txt`       |
| `chown`            | Change user owner                | `chown alice file.txt`     |
| `chown user:group` | Change user and group            | `chown alice:dev file.txt` |
| `chgrp`            | Change group owner               | `chgrp dev file.txt`       |
| `umask`            | View/set file-creation mask      | `umask 027`                |
| `umask -S`         | View symbolic mask               | `umask -S`                 |

---

# 45. Permission Cheat Sheet

## Symbolic

```text
u = user
g = group
o = other
a = all

+ = add
- = remove
= = set exactly
```

Examples:

```bash
chmod u+x file
chmod g-w file
chmod o+r file
chmod a+x script.sh
chmod u=rwx,g=rx,o=r file
```

---

## Numeric

```text
r = 4
w = 2
x = 1
```

Common combinations:

```text
7 = rwx
6 = rw-
5 = r-x
4 = r--
0 = ---
```

Examples:

```bash
chmod 644 file.txt
chmod 600 private.txt
chmod 755 script.sh
chmod 700 private_script.sh
chmod 660 shared.txt
```

---

# 46. Mini Practice Lab

Use a practice directory so you can experiment safely.

```bash
mkdir ~/permissions-lab
cd ~/permissions-lab
```

Create some files:

```bash
touch file1.txt file2.txt script.sh
```

Create a directory:

```bash
mkdir project
```

Check everything:

```bash
ls -l
```

---

## Exercise 1 — Symbolic Permissions

Give `script.sh` execute permission for the owner:

```bash
chmod u+x script.sh
```

Check:

```bash
ls -l script.sh
```

Remove it:

```bash
chmod u-x script.sh
```

---

## Exercise 2 — Numeric Permissions

Set:

```text
file1.txt → 644
```

Run:

```bash
chmod 644 file1.txt
```

Check:

```bash
ls -l file1.txt
```

Then set:

```text
file2.txt → 600
```

```bash
chmod 600 file2.txt
```

Compare the two files.

---

## Exercise 3 — Directory Permissions

Set:

```bash
chmod 755 project
```

Check:

```bash
ls -ld project
```

Notice that the first character is:

```text
d
```

because it is a directory.

---

## Exercise 4 — Ownership

Check the current owner and group:

```bash
ls -l
```

Change the group of a practice file if you have an appropriate group available:

```bash
chgrp GROUP file1.txt
```

Then verify:

```bash
ls -l file1.txt
```

---

## Exercise 5 — `umask`

Check your current value:

```bash
umask
```

Temporarily change it:

```bash
umask 027
```

Create a file:

```bash
touch umask-test.txt
```

Check:

```bash
ls -l umask-test.txt
```

Create a directory:

```bash
mkdir umask-dir
```

Check:

```bash
ls -ld umask-dir
```

Compare the permissions of the file and directory.

---

# 47. Final Mental Model

When looking at a file, think in this order:

```text
                    FILE
                      │
             ┌────────┴────────┐
             │                 │
         Ownership         Permissions
             │                 │
       ┌─────┴─────┐      ┌────┼────┐
       │           │      │    │    │
      User        Group    u    g    o
                             
                         r   w   x
                         │   │   │
                       read write execute
```

For example:

```text
-rwxr-x---
  │   │
  │   └── group
  └────── user

other → ---
```

And:

```text
rwx = 7
r-x = 5
--- = 0
```

Therefore:

```text
rwxr-x--- = 750
```

---

# 48. Chapter Summary

Linux controls access to files using **permissions and ownership**.

The three basic permissions are:

```text
r → read
w → write
x → execute
```

They are assigned to:

```text
u → user/owner
g → group
o → other
```

View permissions with:

```bash
ls -l
```

Change permissions with:

```bash
chmod
```

Change ownership with:

```bash
chown
```

Change group ownership with:

```bash
chgrp
```

Check the default permission mask with:

```bash
umask
```

The two ways to use `chmod` are:

```text
Symbolic:
chmod u+x file

Numeric:
chmod 755 file
```

The numeric values are:

```text
r = 4
w = 2
x = 1
```

Finally, remember:

```text
chmod → permissions
chown → owner
chgrp → group
umask → default permission restrictions
```

The goal is not simply to memorize commands like `chmod 755`.

The real skill is being able to look at:

```text
-rwxr-x---
```

and understand exactly **who can do what**, why they can do it, and how to change that access when necessary.
