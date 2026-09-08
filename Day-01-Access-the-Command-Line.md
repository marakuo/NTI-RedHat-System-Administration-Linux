# Chapter 2 — Access the Command Line

## 1. Structure of a Linux Command

A Linux command generally follows this structure:

```bash
command [options] [arguments]
```

For example:

```bash
ls -l /etc
```

The command line can be broken into three main parts:

```text
command       options       arguments
  │              │              │
  ▼              ▼              ▼
 ls             -l             /etc
```

### 1. Command

The **command** is the program or instruction that you want the shell to execute.

Example:

```bash
ls
```

`ls` tells Linux to list files and directories.

---

### 2. Options

**Options** modify how a command behaves.

Options usually start with:

* `-` for a short option
* `--` for a long option

#### Short option

```bash
ls -l
```

Here:

```text
ls  → command
-l  → option
```

`-l` tells `ls` to use the long listing format.

#### Long option

Some commands support longer, more descriptive options:

```bash
ls --all
```

Here:

```text
ls       → command
--all    → option
```

`--all` tells `ls` to include hidden files.

### Short vs Long Options

```text
-l
```

is a **short option**, while:

```text
--all
```

is a **long option**.

Some commands allow multiple short options to be combined:

```bash
ls -la
```

This is equivalent to:

```bash
ls -l -a
```

The exact options available depend on the command.

---

### 3. Arguments

An **argument** tells the command what object, file, directory, or value it should operate on.

Example:

```bash
cat /etc/passwd
```

Structure:

```text
cat             → command
/etc/passwd     → argument
```

The command is `cat`, and `/etc/passwd` is the file that `cat` should display.

Another example:

```bash
head -n 3 /etc/passwd
```

Structure:

```text
head            → command
-n 3            → option + option value
/etc/passwd     → argument
```

---

## 2. Complete Command Structure

A more detailed representation is:

```text
command [options] [arguments]
```

For example:

```bash
head -n 3 /etc/passwd
```

Can be understood as:

```text
┌──────────┐   ┌────────────┐   ┌─────────────┐
│ command  │   │   option   │   │   argument  │
├──────────┤   ├────────────┤   ├─────────────┤
│   head   │   │    -n 3    │   │ /etc/passwd │
└──────────┘   └────────────┘   └─────────────┘
```

`-n` is the option, and `3` is the value associated with that option.

The command means:

> Display the first 3 lines of `/etc/passwd`.

---

## 3. Important: Not Every Command Has All Three Parts

The structure is a general pattern, not a requirement.

### Command only

```bash
whoami
```

### Command + argument

```bash
cat /etc/passwd
```

### Command + option

```bash
ls -l
```

### Command + option + argument

```bash
head -n 3 /etc/passwd
```

Some commands can have multiple options and arguments:

```bash
ls -la /etc /home
```

---

# 4. `whoami`

Shows the username of the currently logged-in user.

```bash
whoami
```

Example:

```console
[user@host ~]$ whoami
user
```

---

# 5. Running Multiple Commands

## Using `;`

You can execute multiple commands on the same line using `;`.

```bash
command1 ; command2
```

Both commands will execute regardless of whether the first command succeeds or fails.

Example:

```bash
date ; whoami
```

---

## Using `&&`

```bash
command1 && command2
```

The second command executes **only if the first command succeeds**.

Example:

```bash
mkdir test && cd test
```

If `mkdir test` succeeds, `cd test` runs.

If `mkdir test` fails, `cd test` does not run.

### Difference

```bash
command1 ; command2
```

Run both commands regardless of the result.

```bash
command1 && command2
```

Run the second command only if the first succeeds.

---

# 6. `date`

Displays the current date and time.

```bash
date
```

Example:

```console
[user@host ~]$ date
Tue Mar 10 2026 08:32:42 PM UTC
```

The exact output depends on your system's timezone and locale.

## Date Format Characters

The `date` command uses a plus sign followed by format characters. Format characters are case-sensitive, so uppercase and lowercase letters can produce different results.

### Common Date and Time Formats

```bash
date +%Y
```

Displays the four-digit year:

```text
2026
```

```bash
date +%y
```

Displays the two-digit year:

```text
26
```

```bash
date +%m
```

Displays the month as a two-digit number:

```text
03
```

```bash
date +%B
```

Displays the full month name:

```text
March
```

```bash
date +%b
```

Displays the abbreviated month name:

```text
Mar
```

```bash
date +%d
```

Displays the day of the month as a two-digit number:

```text
10
```

```bash
date +%e
```

Displays the day of the month with a leading space instead of a leading zero:

```text
10
```

```bash
date +%A
```

Displays the full weekday name:

```text
Tuesday
```

```bash
date +%a
```

Displays the abbreviated weekday name:

```text
Tue
```

```bash
date +%H
```

Displays the hour in 24-hour format:

```text
20
```

```bash
date +%I
```

Displays the hour in 12-hour format:

```text
08
```

```bash
date +%M
```

Displays the minute:

```text
32
```

```bash
date +%S
```

Displays the seconds:

```text
42
```

```bash
date +%N
```

Displays nanoseconds:

```text
123456789
```

The exact nanosecond value changes every time the command runs.

```bash
date +%p
```

Displays the locale's uppercase AM or PM indicator:

```text
PM
```

```bash
date +%P
```

Displays the locale's lowercase am or pm indicator:

```text
pm
```

```bash
date +%z
```

Displays the numeric timezone offset:

```text
+0000
```

```bash
date +%Z
```

Displays the timezone abbreviation:

```text
UTC
```

```bash
date +%s
```

Displays the number of seconds since the Unix Epoch:

```text
1773174762
```

The value changes continuously and represents the current time.

## Useful Combined Formats

Display the date in year-month-day format:

```bash
date +%Y-%m-%d
```

Example:

```text
2026-03-10
```

Display the time in 24-hour format:

```bash
date +%H:%M:%S
```

Example:

```text
20:32:42
```

Display the time in 12-hour format:

```bash
date +%I:%M:%S%p
```

Example:

```text
08:32:42PM
```

Display the date and time:

```bash
date "+%Y-%m-%d %H:%M:%S"
```

Example:

```text
2026-03-10 20:32:42
```

Display the weekday, date, and time:

```bash
date "+%A, %B %d, %Y %H:%M:%S %Z"
```

Example:

```text
Tuesday, March 10, 2026 20:32:42 UTC
```

### Common Format Reference

| Format | Meaning                         | Example                    |
| ------ | ------------------------------- | -------------------------- |
| `%Y`   | Four-digit year                 | `2026`                     |
| `%y`   | Two-digit year                  | `26`                       |
| `%m`   | Month number                    | `03`                       |
| `%B`   | Full month name                 | `March`                    |
| `%b`   | Abbreviated month name          | `Mar`                      |
| `%d`   | Day of month with leading zero  | `10`                       |
| `%e`   | Day of month with leading space | `10`                       |
| `%A`   | Full weekday name               | `Tuesday`                  |
| `%a`   | Abbreviated weekday name        | `Tue`                      |
| `%H`   | Hour in 24-hour format          | `20`                       |
| `%I`   | Hour in 12-hour format          | `08`                       |
| `%M`   | Minute                          | `32`                       |
| `%S`   | Seconds                         | `42`                       |
| `%N`   | Nanoseconds                     | `123456789`                |
| `%p`   | Uppercase AM or PM              | `PM`                       |
| `%P`   | Lowercase am or pm              | `pm`                       |
| `%z`   | Numeric timezone offset         | `+0000`                    |
| `%Z`   | Timezone abbreviation           | `UTC`                      |
| `%s`   | Seconds since the Unix Epoch    | `1773174762`               |
| `%R`   | Time in `HH:MM` format          | `20:32`                    |
| `%T`   | Time in `HH:MM:SS` format       | `20:32:42`                 |
| `%D`   | Date in `MM/DD/YY` format       | `03/10/26`                 |
| `%F`   | Date in `YYYY-MM-DD` format     | `2026-03-10`               |
| `%x`   | Locale's date representation    | `03/10/26`                 |
| `%X`   | Locale's time representation    | `20:32:42`                 |
| `%c`   | Locale's complete date and time | `Tue Mar 10 20:32:42 2026` |

### Important Difference Between Uppercase and Lowercase

The following format characters are different:

```bash
date +%Y
date +%y
```

* `%Y` → four-digit year, such as `2026`
* `%y` → two-digit year, such as `26`

```bash
date +%H
date +%I
```

* `%H` → hour in 24-hour format
* `%I` → hour in 12-hour format

```bash
date +%M
date +%m
```

* `%M` → minutes
* `%m` → month number

```bash
date +%S
date +%s
```

* `%S` → seconds within the current minute
* `%s` → seconds since the Unix Epoch

```bash
date +%p
date +%P
```

* `%p` → uppercase AM or PM
* `%P` → lowercase am or pm

```bash
date +%Z
date +%z
```

* `%Z` → timezone abbreviation
* `%z` → numeric timezone offset

---

# 7. `pwd`

`pwd` stands for **Print Working Directory**.

It displays the absolute path of the directory you are currently working in.

```bash
pwd
```

Example:

```console
[user@host ~]$ pwd
/home/user
```

If you change to another directory:

```bash
cd /etc
pwd
```

The output is:

```text
/etc
```

`pwd` does not change your location. It only shows your current working directory.

---

# 8. `passwd`

Changes the password of the current user.

```bash
passwd
```

Example:

```console
[user@host ~]$ passwd
Changing password for user user.
Current password:
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

When typing a password, Linux normally does **not display anything on the screen**.

To change another user's password, administrator privileges are generally required:

```bash
sudo passwd username
```

---

# 9. `file`

Displays the type of a file.

```bash
file filename
```

Example:

```bash
file /etc/passwd
```

Output:

```text
/etc/passwd: ASCII text
```

Directories can also be checked:

```bash
file /home
```

Output:

```text
/home: directory
```

Linux does not determine a file's type simply from its filename extension. The `file` command examines the file's contents and identifies its type.

---

# 10. `cat`

Displays the contents of a file.

```bash
cat filename
```

Example:

```bash
cat /etc/passwd
```

It can also display multiple files:

```bash
cat file1 file2
```

The contents are displayed sequentially.

`cat` is most convenient for relatively small files. For large files, commands such as `less`, `head`, and `tail` are often more useful.

---

# 11. `head`

Displays the beginning of a file.

```bash
head filename
```

By default, it displays the first **10 lines**.

Example:

```bash
head /etc/passwd
```

To display a specific number of lines:

```bash
head -n 3 /etc/passwd
```

This displays the first 3 lines.

---

# 12. `tail`

Displays the end of a file.

```bash
tail filename
```

By default, it displays the last **10 lines**.

Example:

```bash
tail /etc/passwd
```

To display a specific number of lines:

```bash
tail -n 3 /etc/passwd
```

This displays the last 3 lines.

### Summary

| Command          | Purpose        |
| ---------------- | -------------- |
| `head file`      | First 10 lines |
| `head -n 3 file` | First 3 lines  |
| `tail file`      | Last 10 lines  |
| `tail -n 3 file` | Last 3 lines   |

---

# 13. `wc`

`wc` stands for **word count**.

It can count:

* Lines
* Words
* Bytes

Basic usage:

```bash
wc filename
```

Example:

```bash
wc /etc/passwd
```

Output:

```text
41  98  2338 /etc/passwd
```

The values represent:

```text
lines   words   bytes   filename
```

### Count lines

```bash
wc -l /etc/passwd
```

### Count words

```bash
wc -w /etc/passwd
```

### Count bytes

```bash
wc -c /etc/passwd
```

Multiple files can also be used:

```bash
wc -c /etc/group /etc/hosts
```

---

# 14. `\` — Line Continuation

A backslash `\` can be used to continue a long command onto another line.

Example:

```bash
head -n 3 \
/usr/share/dict/words \
/usr/share/dict/linux.words
```

The shell interprets this as one command:

```bash
head -n 3 /usr/share/dict/words /usr/share/dict/linux.words
```

This is useful when a command is long and you want to make it easier to read.

The `\` must be the **last character on the line**.

---

# 15. `history`

Displays previously executed commands.

```bash
history
```

Example:

```text
23  clear
24  who
25  pwd
26  ls /etc
27  uptime
28  ls -l
29  date
30  history
```

Each command has a history number.

---

## Re-run a command using `!`

### `!number`

Runs the command with the specified history number.

```bash
!26
```

If command 26 was:

```bash
ls /etc
```

then `!26` executes:

```bash
ls /etc
```

### `!string`

Runs the most recent command that begins with the specified string.

```bash
!ls
```

If the most recent command beginning with `ls` was:

```bash
ls -l
```

then:

```bash
!ls
```

runs:

```bash
ls -l
```

---

# 16. Command-Line Shortcuts

| Shortcut             | Description                                |
| -------------------- | ------------------------------------------ |
| `Ctrl + A`           | Move to the beginning of the command line  |
| `Ctrl + E`           | Move to the end of the command line        |
| `Ctrl + U`           | Delete from the cursor to the beginning    |
| `Ctrl + K`           | Delete from the cursor to the end          |
| `Ctrl + Left Arrow`  | Move to the beginning of the previous word |
| `Ctrl + Right Arrow` | Move to the end of the next word           |
| `Ctrl + R`           | Search command history                     |
| `Ctrl + C`           | Cancel or interrupt the current command    |
| `Ctrl + L`           | Clear the terminal screen                  |
| `Up Arrow`           | Show the previous command                  |
| `Down Arrow`         | Move forward through command history       |
| `Tab`                | Auto-complete commands, files, and paths   |

---

# 17. `Tab` Completion

The `Tab` key can automatically complete commands, filenames, and paths.

For example:

```bash
cd /usr/sh
```

Press `Tab`, and the shell may complete it to:

```bash
cd /usr/share/
```

If there are multiple possible completions, pressing `Tab` twice can display the available choices.

This is extremely useful when working with long filenames and paths.

---

# 18. `Ctrl + R` — History Search

Press:

```text
Ctrl + R
```

Then type part of a previous command.

For example, searching for:

```text
ssh
```

can find a previous command containing `ssh`.

Press `Ctrl + R` again to search through older matches.

Press `Enter` to execute the displayed command.

---

# 19. `Ctrl + C`

Interrupts the currently running command.

For example, if a command keeps running and you need to stop it:

```text
Ctrl + C
```

This sends an interrupt signal to the running process.

---

# 20. `Ctrl + L`

Clears the visible terminal screen.

```text
Ctrl + L
```

It is similar to running:

```bash
clear
```

It does **not** delete the command history.

---

# 21. Command Exit Status

Linux commands return an **exit status** after execution.

Generally:

```text
0        → Success
non-zero → Failure/Error
```

You can check the exit status of the previous command using:

```bash
echo $?
```

Example:

```bash
mkdir test
echo $?
```

If the directory was successfully created:

```text
0
```

This is directly related to `&&`.

For example:

```bash
mkdir test && cd test
```

The `cd` command runs because `mkdir` returned a successful exit status.

---

# 22. Quick Reference

| Command / Operator | Purpose                                                    |
| ------------------ | ---------------------------------------------------------- |
| `whoami`           | Show current user                                          |
| `date`             | Show date and time                                         |
| `date +%Y`         | Show the four-digit year                                   |
| `date +%y`         | Show the two-digit year                                    |
| `date +%m`         | Show the month number                                      |
| `date +%B`         | Show the full month name                                   |
| `date +%b`         | Show the abbreviated month name                            |
| `date +%d`         | Show the day of the month                                  |
| `date +%A`         | Show the full weekday name                                 |
| `date +%a`         | Show the abbreviated weekday name                          |
| `date +%H`         | Show the hour in 24-hour format                            |
| `date +%I`         | Show the hour in 12-hour format                            |
| `date +%M`         | Show the minutes                                           |
| `date +%S`         | Show the seconds                                           |
| `date +%p`         | Show uppercase AM or PM                                    |
| `date +%P`         | Show lowercase am or pm                                    |
| `date +%z`         | Show the numeric timezone offset                           |
| `date +%Z`         | Show the timezone abbreviation                             |
| `date +%R`         | Show time in `HH:MM` format                                |
| `date +%T`         | Show time in `HH:MM:SS` format                             |
| `date +%F`         | Show date in `YYYY-MM-DD` format                           |
| `date +%x`         | Show the locale's date representation                      |
| `date +%X`         | Show the locale's time representation                      |
| `pwd`              | Show current working directory                             |
| `passwd`           | Change password                                            |
| `file`             | Identify file type                                         |
| `cat`              | Display file contents                                      |
| `head`             | Display beginning of file                                  |
| `tail`             | Display end of file                                        |
| `wc`               | Count lines, words, and bytes                              |
| `history`          | Display command history                                    |
| `!number`          | Re-run a history command                                   |
| `!string`          | Re-run the latest command beginning with a string          |
| `;`                | Run commands sequentially regardless of success            |
| `&&`               | Run the next command only if the previous command succeeds |
| `\`                | Continue a command onto another line                       |
| `Tab`              | Auto-completion                                            |
| `Ctrl + R`         | Search command history                                     |
| `Ctrl + C`         | Interrupt the current command                              |
| `Ctrl + L`         | Clear the terminal                                         |
| `echo $?`          | Show the previous command's exit status                    |

---

# 23. What You Should Practice

In your RHEL VM, make sure you can perform these without looking at the notes:

```bash
whoami
date
date +%Y
date +%y
date +%m
date +%B
date +%b
date +%d
date +%A
date +%a
date +%H
date +%I
date +%M
date +%S
date +%p
date +%P
date +%z
date +%Z
date +%R
date +%T
date +%F
date +%x
date +%X
date "+%Y-%m-%d %H:%M:%S %Z"
pwd
file /etc/passwd
cat /etc/passwd
head /etc/passwd
head -n 3 /etc/passwd
tail /etc/passwd
tail -n 3 /etc/passwd
wc /etc/passwd
wc -l /etc/passwd
history
```

Then practice:

```bash
date ; whoami
```

```bash
mkdir test && cd test
```

```bash
echo $?
```

And practice the keyboard shortcuts, especially:

```text
Tab
Ctrl + R
Ctrl + C
Ctrl + L
Ctrl + A
Ctrl + E
```

The goal is not just to memorize these commands. You should be able to look at a command such as:

```bash
date "+%A, %B %d, %Y %H:%M:%S %Z"
```

and identify the command, the format option, and each date and time format character. You should also be able to look at:

```bash
head -n 5 /etc/passwd
```

and immediately identify the command, option, option value, and argument.
