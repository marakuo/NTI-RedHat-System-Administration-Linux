# Chapter 4 — Edit Text Files and Control Input/Output

This chapter covers two important parts of working with Linux from the command line:

1. **Editing text files** using `vim` and `nano`.
2. **Controlling command input and output** using redirection, pipes, and stream filters.

---

# 1. Editing Text Files

Linux administrators frequently need to edit configuration files directly from the terminal.

Two common terminal editors are:

* `vim`
* `nano`

---

## 1.1 `vim`

`vim` is a powerful terminal-based text editor commonly available on Linux systems.

Open or create a file:

```bash
vim filename
```

Example:

```bash
vim config.txt
```

If the file does not exist, Vim creates it when the file is saved.

### Vim is a Modal Editor

Unlike simple text editors, Vim has different **modes**. The same key can perform different actions depending on the current mode.

The main modes covered here are:

| Mode    | Purpose                                |
| ------- | -------------------------------------- |
| Normal  | Navigate and perform editing commands  |
| Insert  | Insert and modify text                 |
| Command | Execute commands such as save and quit |
| Visual  | Select text                            |

When Vim starts, it normally opens in **Normal mode**.

---

## 1.2 Insert Mode

To start typing text, enter Insert mode.

Common commands:

```text
i
```

Insert before the cursor.

```text
a
```

Insert after the cursor.

```text
o
```

Create a new line below the current line and enter Insert mode.

To return to Normal mode:

```text
Esc
```

---

## 1.3 Normal Mode

Normal mode is used for navigation and editing operations.

Some useful commands:

| Command    | Action              |
| ---------- | ------------------- |
| `h`        | Move left           |
| `j`        | Move down           |
| `k`        | Move up             |
| `l`        | Move right          |
| `0`        | Beginning of line   |
| `$`        | End of line         |
| `gg`       | Beginning of file   |
| `G`        | End of file         |
| `dd`       | Delete current line |
| `yy`       | Copy current line   |
| `p`        | Paste after cursor  |
| `u`        | Undo                |
| `Ctrl + r` | Redo                |

---

## 1.4 Command Mode

From Normal mode, press:

```text
:
```

This allows you to enter Vim commands.

### Save

```vim
:w
```

Write/save the file.

### Save and Exit

```vim
:wq
```

Save the file and quit Vim.

### Exit Without Saving

```vim
:q!
```

Quit Vim and discard changes.

### Quit

```vim
:q
```

Quit if there are no unsaved changes.

---

## 1.5 Searching in Vim

To search for text:

```text
/pattern
```

Example:

```text
/root
```

Press:

```text
Enter
```

to search.

Use:

```text
n
```

to move to the next match.

Use:

```text
N
```

to move to the previous match.

---

## 1.6 Search and Replace in Vim

Vim can search for a pattern and replace it.

General syntax:

```vim
:%s/old/new/g
```

Example:

```vim
:%s/user/student/g
```

This replaces occurrences of `user` with `student` throughout the file.

Meaning:

```text
:%s      → search the entire file
old      → text to find
new      → replacement
g        → replace all matches on each line
```

---

# 2. `nano`

`nano` is a simpler terminal text editor.

Open a file:

```bash
nano filename
```

Example:

```bash
nano config.txt
```

Unlike Vim, Nano does not use the same modal editing system.

Common shortcuts:

| Shortcut   | Action           |
| ---------- | ---------------- |
| `Ctrl + O` | Save/write file  |
| `Ctrl + X` | Exit             |
| `Ctrl + W` | Search           |
| `Ctrl + K` | Cut current line |
| `Ctrl + U` | Paste            |
| `Ctrl + G` | Show help        |

The `^` symbol shown in Nano means the **Ctrl** key.

For example:

```text
^O
```

means:

```text
Ctrl + O
```

---
