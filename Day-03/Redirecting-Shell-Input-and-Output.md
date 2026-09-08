# Redirecting Shell Input and Output

When you run a Linux command, it usually:

* **receives input**
* **produces normal output**
* **may produce error messages**

Linux provides a standard way to handle these three streams using **file descriptors**.

This chapter covers:

* `stdin`, `stdout`, and `stderr`
* File descriptors `0`, `1`, and `2`
* Output redirection: `>` and `>>`
* Error redirection: `2>`, `2>>`, `2>&1`, `&>`
* Input redirection: `<`
* Pipes: `|`
* Stream filters:

  * `grep`
  * `sort`
  * `uniq`
  * `wc`
  * `head`
  * `tail`
  * `tee`
  * `less`

---

# 1. The Big Idea

Normally, when you run a command:

```text
             Input
               │
               ▼
           ┌─────────┐
           │ Command │
           └─────────┘
             │     │
             ▼     ▼
          Output   Errors
```

For example:

```bash
ls
```

The command gets its input from the terminal when needed and sends its output back to the terminal.

Linux represents these streams using **file descriptors**.

---

# 2. What Is a File Descriptor?

A **file descriptor (FD)** is a number that represents an open input/output stream.

The three standard file descriptors are:

|  FD | Name     | Purpose         | Default  |
| --: | -------- | --------------- | -------- |
| `0` | `stdin`  | Standard input  | Keyboard |
| `1` | `stdout` | Standard output | Terminal |
| `2` | `stderr` | Standard error  | Terminal |

Think of them as three pipes connected to every command:

```text
                    COMMAND
                 ┌───────────┐
stdin (0) ──────►│           │──────► stdout (1)
                 │           │
                 │           │──────► stderr (2)
                 └───────────┘
```

---

# 3. `stdin` — Standard Input

`stdin` means:

> **Standard Input**

Its file descriptor is:

```text
0
```

Normally, `stdin` comes from your keyboard.

For example:

```bash
cat
```

`cat` waits for input.

You type:

```text
Hello Linux
```

and press Enter.

It outputs:

```text
Hello Linux
```

You can finish the input with:

```text
Ctrl + D
```

So:

```text
Keyboard
   │
   │ stdin (0)
   ▼
 cat
   │
   │ stdout (1)
   ▼
Terminal
```

---

# 4. `stdout` — Standard Output

`stdout` means:

> **Standard Output**

Its file descriptor is:

```text
1
```

This is where a command normally sends its successful output.

For example:

```bash
ls
```

might produce:

```text
Documents
Downloads
Pictures
```

That is `stdout`.

Normally:

```text
Command
   │
   │ stdout (1)
   ▼
Terminal
```

---

# 5. `stderr` — Standard Error

`stderr` means:

> **Standard Error**

Its file descriptor is:

```text
2
```

Commands use `stderr` for error messages.

For example:

```bash
ls does_not_exist
```

You might get:

```text
ls: cannot access 'does_not_exist': No such file or directory
```

That message is sent through:

```text
stderr (2)
```

not normal `stdout`.

---

# 6. Why Are `stdout` and `stderr` Separate?

This is extremely important.

Imagine:

```bash
ls existing_file missing_file
```

You might get:

```text
existing_file
ls: cannot access 'missing_file': No such file or directory
```

There are actually two different streams:

```text
                 ls
                /  \
               /    \
              ▼      ▼
        stdout (1)  stderr (2)
             │          │
             ▼          ▼
       existing_file   error
```

This separation allows you to redirect normal output without necessarily redirecting errors.

---

# 7. Output Redirection — `>`

The `>` operator redirects **stdout** to a file.

Example:

```bash
ls > files.txt
```

Instead of displaying the output on the terminal, the output goes into:

```text
files.txt
```

Visual:

```text
Before:

ls
 │
 └── stdout ──► Terminal


After:

ls
 │
 └── stdout ──► files.txt
```

---

# 8. Important: `>` Overwrites

Suppose:

```text
files.txt
```

already contains:

```text
old content
```

Then:

```bash
ls > files.txt
```

replaces the old contents with the output of `ls`.

So:

```text
Before:
files.txt
└── old content

After:
files.txt
└── output of ls
```

### Be careful with `>`

It can overwrite existing files.

---

# 9. Appending With `>>`

The `>>` operator also redirects stdout, but it **appends** instead of overwriting.

Example:

```bash
echo "Hello" > file.txt
```

Now:

```text
Hello
```

Then:

```bash
echo "Linux" >> file.txt
```

The file becomes:

```text
Hello
Linux
```

Compare:

```text
>   → overwrite
>>  → append
```

---

# 10. `echo` + Redirection

`echo` prints text to stdout.

For example:

```bash
echo "Hello Linux"
```

outputs:

```text
Hello Linux
```

You can redirect it:

```bash
echo "Hello Linux" > message.txt
```

Now:

```text
message.txt
└── Hello Linux
```

Append another line:

```bash
echo "Learning redirection" >> message.txt
```

Now:

```text
Hello Linux
Learning redirection
```

---

# 11. Redirecting Errors — `2>`

Remember:

```text
stdin  → 0
stdout → 1
stderr → 2
```

Therefore:

```bash
2>
```

means:

> Redirect `stderr`.

Example:

```bash
ls missing 2> errors.txt
```

The error message goes into:

```text
errors.txt
```

while normal output still goes to the terminal.

Visual:

```text
              ls
             /  \
            /    \
 stdout (1)       stderr (2)
    │                 │
    ▼                 ▼
Terminal          errors.txt
```

---

# 12. `2>>` — Append Errors

Just like `>>`, you can append errors:

```bash
ls missing 2>> errors.txt
```

This adds the error message to the end of:

```text
errors.txt
```

instead of replacing its existing contents.

---

# 13. `2>&1` — Send stderr to stdout

This syntax is very important:

```bash
2>&1
```

It means:

> Send file descriptor `2` (`stderr`) to the same destination as file descriptor `1` (`stdout`).

In simpler words:

> Combine errors with normal output.

Example:

```bash
command > output.txt 2>&1
```

This sends:

```text
stdout → output.txt
stderr → output.txt
```

So both normal output and errors are stored in the same file.

---

# 14. Why Does `&1` Have an `&`?

The `&` tells the shell that:

```text
1
```

is a **file descriptor**, not a filename.

Compare:

```bash
2> 1
```

This means:

> Redirect stderr to a file literally called `1`.

But:

```bash
2>&1
```

means:

> Redirect stderr to file descriptor 1.

This distinction is important.

---

# 15. Order Matters With `2>&1`

These commands are **not equivalent**:

```bash
command > output.txt 2>&1
```

and:

```bash
command 2>&1 > output.txt
```

### Correct/common form

```bash
command > output.txt 2>&1
```

First:

```text
stdout → output.txt
```

Then:

```text
stderr → wherever stdout is currently going
```

So both go to:

```text
output.txt
```

### The other order

```bash
command 2>&1 > output.txt
```

First:

```text
stderr → current stdout destination
```

which is still the terminal.

Then:

```text
stdout → output.txt
```

Result:

```text
stdout → output.txt
stderr → terminal
```

### Remember

```bash
command > output.txt 2>&1
```

is the common way to put **both stdout and stderr into the same file**.

---

# 16. `&>` — Redirect Both

Bash also supports:

```bash
&> output.txt
```

This redirects both:

```text
stdout
stderr
```

to the same file.

So:

```bash
command &> output.txt
```

is commonly equivalent in Bash to:

```bash
command > output.txt 2>&1
```

You can append both streams using:

```bash
command &>> output.txt
```

---

# 17. Input Redirection — `<`

So far we've redirected output.

The `<` operator redirects **stdin**.

Example:

```bash
command < file.txt
```

means:

> Take the contents of `file.txt` as the command's standard input.

---

# 18. Example With `cat`

Normally:

```bash
cat
```

reads from the keyboard.

But:

```bash
cat < file.txt
```

reads from:

```text
file.txt
```

instead.

Visual:

```text
Without redirection:

Keyboard
   │
   ▼
 cat
   │
   ▼
Terminal


With < :

file.txt
   │
   │ stdin (0)
   ▼
 cat
   │
   │ stdout (1)
   ▼
Terminal
```

---

# 19. Input and Output Together

You can redirect both input and output:

```bash
cat < input.txt > output.txt
```

This means:

```text
input.txt
    │
    ▼
  stdin
    │
    ▼
   cat
    │
    ▼
  stdout
    │
    ▼
output.txt
```

So:

```text
input.txt → cat → output.txt
```

---

# 20. Redirection Cheat Sheet

| Syntax | Meaning                             |
| ------ | ----------------------------------- |
| `>`    | Redirect stdout, overwrite          |
| `>>`   | Redirect stdout, append             |
| `2>`   | Redirect stderr, overwrite          |
| `2>>`  | Redirect stderr, append             |
| `2>&1` | Send stderr to stdout's destination |
| `&>`   | Redirect stdout + stderr            |
| `&>>`  | Append stdout + stderr              |
| `<`    | Redirect stdin                      |

---

# 21. Pipes — `|`

A pipe connects the **stdout of one command** to the **stdin of another command**.

Basic form:

```bash
command1 | command2
```

Visual:

```text
command1
   │
   │ stdout
   ▼
  pipe
   │
   │ stdin
   ▼
command2
```

Instead of saving the output to a file, you directly send it to another command.

---

# 22. Simple Pipe Example

Suppose:

```bash
ls
```

produces:

```text
file1.txt
file2.txt
image.png
notes.txt
```

You can pipe it into `grep`:

```bash
ls | grep ".txt"
```

Flow:

```text
ls
 │
 │ stdout
 ▼
grep ".txt"
 │
 ▼
Terminal
```

Output:

```text
file1.txt
file2.txt
notes.txt
```

---

# 23. Pipe vs Redirection

These are different:

```bash
ls > files.txt
```

means:

```text
ls → file
```

while:

```bash
ls | grep txt
```

means:

```text
ls → another command
```

Think:

```text
>   → command → file
|   → command → command
```

---

# 24. `grep` — Search Text

`grep` searches for text matching a pattern.

Basic syntax:

```bash
grep PATTERN file
```

Example:

```bash
grep "Linux" notes.txt
```

This prints lines containing:

```text
Linux
```

---

# 25. `grep` With a Pipe

You can search the output of another command:

```bash
ls | grep ".txt"
```

This means:

1. `ls` produces a list.
2. `|` sends that list to `grep`.
3. `grep` keeps lines containing `.txt`.

---

# 26. `grep -i` — Ignore Case

```bash
grep -i "linux" file.txt
```

matches:

```text
Linux
linux
LINUX
LiNuX
```

because `-i` means:

> Ignore case.

---

# 27. `grep -v` — Invert the Match

```bash
grep -v "Linux" file.txt
```

means:

> Show lines that do **not** contain `Linux`.

So:

```text
Linux is powerful
I use Ubuntu
Linux is open source
Windows is different
```

could become:

```text
I use Ubuntu
Windows is different
```

---

# 28. `sort`

`sort` sorts lines of text.

Example:

```bash
sort names.txt
```

Input:

```text
Ziad
Ali
Malak
Omar
```

Output:

```text
Ali
Malak
Omar
Ziad
```

---

# 29. `sort` With a Pipe

```bash
ls | sort
```

means:

```text
ls → sort → terminal
```

The output of `ls` becomes the input to `sort`.

---

# 30. `uniq`

`uniq` removes **adjacent duplicate lines**.

Example:

```text
apple
apple
banana
banana
orange
```

Running:

```bash
uniq file.txt
```

produces:

```text
apple
banana
orange
```

### Important

`uniq` only detects duplicates that are next to each other.

For example:

```text
apple
banana
apple
```

does **not** become:

```text
apple
banana
```

because the two `apple` lines are not adjacent.

---

# 31. `sort | uniq`

A common combination is:

```bash
sort file.txt | uniq
```

Why?

`sort` puts identical lines together:

```text
apple
apple
banana
banana
```

Then `uniq` removes the duplicates:

```text
apple
banana
```

You can also use:

```bash
sort -u file.txt
```

which sorts and removes duplicates in one step.

But understanding:

```bash
sort | uniq
```

is important because it teaches how pipelines work.

---

# 32. `wc` — Count

`wc` means:

> **Word Count**

It can count:

* lines
* words
* bytes

Example:

```bash
wc file.txt
```

Output may look like:

```text
10 25 150 file.txt
```

Meaning:

```text
10 → lines
25 → words
150 → bytes
```

---

# 33. Useful `wc` Options

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

---

# 34. `wc` With a Pipe

You can count the output of another command:

```bash
ls | wc -l
```

This means:

```text
ls
 │
 ▼
wc -l
 │
 ▼
number of lines
```

This is commonly used to count entries.

---

# 35. `head`

`head` displays the beginning of a file.

```bash
head file.txt
```

By default, it displays the first **10 lines**.

You can specify the number:

```bash
head -5 file.txt
```

This shows the first 5 lines.

Or:

```bash
head -n 20 file.txt
```

shows the first 20 lines.

---

# 36. `tail`

`tail` displays the end of a file.

```bash
tail file.txt
```

By default, it displays the last **10 lines**.

Specify the number:

```bash
tail -5 file.txt
```

or:

```bash
tail -n 20 file.txt
```

---

# 37. `tail -f` — Follow a File

One particularly useful option is:

```bash
tail -f logfile.txt
```

`-f` means:

> Follow the file.

It continues displaying new lines as they are added.

This is very useful for watching log files.

For example:

```bash
tail -f /var/log/some-log-file
```

You can stop following with:

```text
Ctrl + C
```

---

# 38. `tee`

`tee` is useful when you want to:

> See output on the terminal **and** save it to a file.

Example:

```bash
ls | tee files.txt
```

Flow:

```text
             ┌──► Terminal
             │
ls ──► tee ──┤
             │
             └──► files.txt
```

Unlike:

```bash
ls > files.txt
```

which sends the output only to the file, `tee` sends it to both places.

---

# 39. `tee -a`

By default:

```bash
tee file.txt
```

overwrites the file.

Use:

```bash
tee -a file.txt
```

to append instead.

The `-a` means:

> Append.

Example:

```bash
echo "Hello" | tee -a log.txt
```

You see:

```text
Hello
```

on the terminal and it is also appended to:

```text
log.txt
```

---

# 40. `less`

`less` lets you view long output one screen at a time.

Example:

```bash
less largefile.txt
```

You can navigate through the file without printing everything at once.

Useful keys:

| Key     | Action             |
| ------- | ------------------ |
| `Space` | Next page          |
| `b`     | Previous page      |
| `↑`     | Up                 |
| `↓`     | Down               |
| `/text` | Search             |
| `n`     | Next search result |
| `q`     | Quit               |

---

# 41. `less` With a Pipe

You can send command output into `less`:

```bash
ls -l /usr/bin | less
```

This is useful when there is a lot of output.

Flow:

```text
ls -l /usr/bin
        │
        ▼
       less
        │
        ▼
     Screen
```

---

# 42. Building Pipelines

The real power of Linux comes from combining small commands.

For example:

```bash
ls | grep ".txt" | sort
```

Flow:

```text
ls
 │
 ▼
grep ".txt"
 │
 ▼
sort
 │
 ▼
Terminal
```

Each command performs one task.

```text
ls    → produce list
grep  → filter
sort  → organize
```

---

# 43. A More Useful Pipeline

Suppose you want to count `.txt` files:

```bash
ls | grep ".txt" | wc -l
```

The flow is:

```text
ls
 │
 ▼
grep ".txt"
 │
 ▼
wc -l
 │
 ▼
number
```

So:

```text
1. List files
2. Keep .txt files
3. Count the lines
```

---

# 44. `sort | uniq | wc`

Suppose a file contains:

```text
Linux
Linux
Windows
Linux
Ubuntu
Windows
```

You can find the number of unique lines:

```bash
sort file.txt | uniq | wc -l
```

Flow:

```text
file.txt
   │
   ▼
 sort
   │
   ▼
 uniq
   │
   ▼
 wc -l
   │
   ▼
 result
```

---

# 45. Combining Redirection and Pipes

You can combine pipes and redirection.

Example:

```bash
ls | grep ".txt" > textfiles.txt
```

This means:

```text
ls
 │
 ▼
grep ".txt"
 │
 │ stdout
 ▼
textfiles.txt
```

The final output goes into the file.

---

# 46. Pipeline + `tee`

You can both save and view the output:

```bash
ls | grep ".txt" | tee textfiles.txt
```

Flow:

```text
ls
 │
 ▼
grep ".txt"
 │
 ▼
tee
 ├──────► Terminal
 │
 └──────► textfiles.txt
```

---

# 47. A Complete Example

Let's create some practice files:

```bash
mkdir ~/redirect-practice
cd ~/redirect-practice
```

Create a file:

```bash
printf "Linux\nLinux\nUbuntu\nWindows\nLinux\nUbuntu\n" > systems.txt
```

Check it:

```bash
cat systems.txt
```

Output:

```text
Linux
Linux
Ubuntu
Windows
Linux
Ubuntu
```

---

## Sort it

```bash
sort systems.txt
```

Output:

```text
Linux
Linux
Linux
Ubuntu
Ubuntu
Windows
```

---

## Remove duplicates

```bash
sort systems.txt | uniq
```

Output:

```text
Linux
Ubuntu
Windows
```

---

## Count unique values

```bash
sort systems.txt | uniq | wc -l
```

Output:

```text
3
```

---

## Save the result

```bash
sort systems.txt | uniq > unique.txt
```

Now:

```text
unique.txt
```

contains:

```text
Linux
Ubuntu
Windows
```

---

# 48. Standard Streams + Pipes

A very important concept:

```text
             Command 1
            ┌──────────┐
stdin (0) ─►│          │
            │          │
            └────┬─────┘
                 │
                 │ stdout (1)
                 ▼
                PIPE
                 │
                 ▼
            ┌──────────┐
            │ Command 2│
            └──────────┘
```

The pipe connects:

```text
stdout of command 1
        ↓
stdin of command 2
```

So:

```bash
command1 | command2
```

is conceptually:

```text
command1 stdout → command2 stdin
```

---

# 49. What Happens to `stderr` in a Pipe?

This is another important detail.

When you run:

```bash
command1 | command2
```

the pipe normally connects only:

```text
stdout (1)
```

of `command1` to:

```text
stdin (0)
```

of `command2`.

`stderr (2)` is **not automatically piped**.

Visual:

```text
             command1
             /      \
            /        \
       stdout        stderr
          │             │
          ▼             ▼
        pipe          Terminal
          │
          ▼
       command2
```

If you want errors to go through the pipe too, you can combine them:

```bash
command1 2>&1 | command2
```

Now both stdout and stderr are sent through the pipe.

---

# 50. Example of `2>&1` With a Pipe

```bash
command 2>&1 | less
```

means:

```text
stdout ────────┐
               ├──► less
stderr ────────┘
```

This is useful when you want to inspect both normal output and errors using `less`.

---

# 51. A Practical Command Chain

Here's a useful example:

```bash
ls -l /etc 2>&1 | less
```

Breaking it down:

```text
ls -l /etc
```

produces normal output and possibly errors.

```text
2>&1
```

combines the error stream with stdout.

```text
|
```

sends the combined output to:

```text
less
```

So you can scroll through everything.

---

# 52. Redirection vs Pipes vs Filters

Think of them as three different ideas.

### Redirection

Moves data between a command and a file:

```bash
ls > files.txt
```

```text
command → file
```

---

### Pipe

Moves data between commands:

```bash
ls | grep txt
```

```text
command → command
```

---

### Filter

A command that processes input and produces modified/selected output.

Examples:

```text
grep
sort
uniq
wc
head
tail
```

---

# 53. Useful Filter Combinations

### Search

```bash
ls | grep ".txt"
```

### Sort

```bash
ls | sort
```

### Count

```bash
ls | wc -l
```

### First results

```bash
ls | head
```

### Last results

```bash
ls | tail
```

### Search + count

```bash
ls | grep ".txt" | wc -l
```

### Sort + remove duplicates

```bash
sort file.txt | uniq
```

### Sort + unique + count

```bash
sort file.txt | uniq | wc -l
```

### View long output

```bash
ls -l /usr/bin | less
```

---

# 54. `>` vs `>>`

This is one of the most important things to remember:

```bash
command > file
```

**Overwrites** the file.

```bash
command >> file
```

**Appends** to the file.

Example:

```bash
echo "one" > file.txt
echo "two" >> file.txt
```

Result:

```text
one
two
```

But:

```bash
echo "three" > file.txt
```

results in:

```text
three
```

because `>` replaced the previous contents.

---

# 55. `>` vs `2>`

These are also different:

```bash
command > output.txt
```

redirects:

```text
stdout (1)
```

while:

```bash
command 2> errors.txt
```

redirects:

```text
stderr (2)
```

You can send them to separate files:

```bash
command > output.txt 2> errors.txt
```

Result:

```text
stdout → output.txt
stderr → errors.txt
```

---

# 56. Redirect Everything to One File

You can combine both:

```bash
command > output.txt 2>&1
```

or in Bash:

```bash
command &> output.txt
```

Result:

```text
stdout ──┐
         ├──► output.txt
stderr ──┘
```

---

# 57. The Most Important Commands

| Command | Purpose                        |
| ------- | ------------------------------ |
| `grep`  | Search/filter lines            |
| `sort`  | Sort lines                     |
| `uniq`  | Remove adjacent duplicates     |
| `wc`    | Count lines/words/bytes        |
| `head`  | Show beginning                 |
| `tail`  | Show end                       |
| `tee`   | Display and save output        |
| `less`  | View long output interactively |

---

# 58. Quick Cheat Sheet

## Standard streams

```text
0 → stdin
1 → stdout
2 → stderr
```

## Redirection

```bash
command > file
```

stdout → file, overwrite.

```bash
command >> file
```

stdout → file, append.

```bash
command 2> file
```

stderr → file.

```bash
command 2>> file
```

stderr → file, append.

```bash
command > file 2>&1
```

stdout + stderr → file.

```bash
command &> file
```

stdout + stderr → file.

```bash
command < file
```

file → stdin.

---

## Pipes

```bash
command1 | command2
```

stdout of `command1` → stdin of `command2`.

---

## Filters

```bash
grep "text" file
sort file
uniq file
wc file
head file
tail file
```

---

## Interactive viewing

```bash
less file
```

---

## Display + save

```bash
command | tee file
```

Append:

```bash
command | tee -a file
```

---

# 59. Final Mental Model

The easiest way to remember everything is:

```text
                         ┌───────────────┐
                         │    COMMAND    │
                         └───────────────┘
                           ▲     │     │
                           │     │     │
                    stdin  │     │     │  stderr
                     (0)   │     │     │   (2)
                           │     │     │
                           │ stdout    │
                           │   (1)     │
                           │     │     │
                           │     ▼     ▼
                         Input   Output Error
```

Then remember the operators:

```text
<       → file → stdin
>       → stdout → file (overwrite)
>>      → stdout → file (append)
2>      → stderr → file
2>>     → stderr → file (append)
2>&1    → stderr → stdout's destination
&>      → stdout + stderr → file
|       → stdout → stdin of another command
```

And finally, think of Linux commands as small building blocks:

```text
        PRODUCE          FILTER          PROCESS          VIEW
          │                │                │               │
          ▼                ▼                ▼               ▼

         ls ────────────► grep ──────────► sort ──────────► less
                            │
                            ▼
                           wc
```

The power comes from combining them:

```bash
ls | grep ".txt" | sort | tee results.txt
```

Read it from left to right:

```text
ls
 ↓
list files

grep
 ↓
keep .txt files

sort
 ↓
sort them

tee
 ↓
show them AND save them
```

This is the fundamental idea behind **Linux pipelines and shell redirection**.
