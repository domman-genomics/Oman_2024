# Bash commands reference sheet

Bash (Bourne Again SHell) is a popular command-line interpreter that allows you to interact with your operating system. Here are some basic commands:

## Navigating the File System

- `pwd`: Print Working Directory. Shows the full path of the current directory.
- `cd`: Change Directory. Used to navigate to different directories.
    - `cd ..`: Move up one directory.
    - `cd ~`: Move to the home directory.
- `ls`: List. Shows the contents of the current directory.

## Common Options for the `ls` Command

- `ls -a`: Lists all files, including hidden files (those whose names start with `.`).
- `ls -l`: Lists files in 'long format', which contains lots of useful information, such as the exact size of the file, who owns the file and who has the right to look at it, and when it was last modified.
- `ls -lh`: When used with the -l option, use unit suffixes: Byte, Kilobyte, Megabyte, Gigabyte, Terabyte and Petabyte in order to reduce the number of digits to three or less using base 2 for sizes.
- `ls -r`: Lists files in reverse order.
- `ls -t`: Lists files by modification time, newest first.
- `ls -R`: Lists subdirectories recursively.
- `ls -S`: Sort files by size.
- `ls -1`: Lists one file per line.

Remember, you can always use `man ls` in the terminal to get more information about the `ls` command and its options.


## File and Directory Operations

- `touch`: Creates a new file.
- `mkdir`: Make Directory. Creates a new directory.
- `rm`: Remove. Deletes a file.
    - `rm -r`: Remove recursively. Deletes a directory and its contents.
- `cp`: Copy. Copies files or directories.
- `mv`: Move. Moves or renames files or directories.

## Viewing and Editing Files

- `cat`: Concatenate. Prints the contents of a file to the terminal.
- `less`: Allows you to view the contents of a file page by page.
- `nano`, `vi`, `emacs`: Text editors that can be used to create or modify files.

## Permissions

- `chmod`: Change Mode. Modifies the permissions of a file or directory.
- `chown`: Change Owner. Changes the owner of a file or directory.

## Searching

- `find`: Searches for files and directories.
- `grep`: Searches inside files for specific text.

## Common Options for the `find` Command

- `find . -name "filename"`: Finds files or directories named "filename" within the current directory (.) and its subdirectories.
- `find / -name "filename"`: Finds files or directories named "filename" within the root directory (/) and its subdirectories.
- `find . -iname "filename"`: Similar to `-name`, but the search is case insensitive.
- `find . -type f`: Finds all files in the current directory and its subdirectories.
- `find . -type d`: Finds all directories in the current directory and its subdirectories.
- `find . -empty`: Finds all empty files or directories in the current directory and its subdirectories.
- `find . -mtime -7`: Finds all files in the current directory and its subdirectories that were modified within the last 7 days.
- `find . -size +100M`: Finds all files in the current directory and its subdirectories that are larger than 100 Megabytes.

## Common Options for the `grep` Command

- `grep "pattern" filename`: Search for a pattern within a specific file.
- `grep -i "pattern" filename`: Search for a pattern case insensitively in a file.
- `grep -r "pattern" directory`: Search recursively for a pattern in a directory.
- `grep -v "pattern" filename`: Use the `-v` option to display those lines that do NOT match the pattern.
- `grep -l "pattern" file1 file2 file3`: Use the `-l` option to just give the file name of matching files.
- `grep -n "pattern" filename`: Use the `-n` option to precede each line of output with the number of the line in the text file.
- `grep -c "pattern" filename`: Use the `-c` option to only show a count of matching lines.
- `grep -w "pattern" filename`: Use the `-w` option to force PATTERN to match only whole words.


## Piping commands together

### Example 1: Find all .txt files and count how many lines they contain in total
```bash
find . -name "*.txt" | xargs wc -l
```
In this example, `find . -name "*.txt"` finds all .txt files in the current directory and its subdirectories. The results are then piped into `xargs`, which executes the `wc -l` command for each file. `wc -l` counts the number of lines in a file. The total count of all lines in all .txt files is then output.

### Example 2: List all files in a directory sorted by file size

```bash
ls -l | sort -k5 -n
```

Here, `ls -l` lists all files in long format, which includes file size (among other details). The output is piped into `sort -k5 -n`, which sorts the output based on the fifth field (i.e., file size) in numerical order.

### Example 3: Find all .py files and search for a specific pattern

```bash
find . -name "*.py" | xargs grep "pattern"
```

In this example, `find . -name "*.py"` finds all .py files in the current directory and its subdirectories. The results are then piped into `xargs`, which executes the `grep "pattern"` command for each file. This will search for the string "pattern" within each Python file.

### Example 4: Display the top 5 largest directories or files in the current directory

```bash
du -sh * | sort -hr | head -5
```

`du -sh *` estimates file and directory space usage. The output is piped into `sort -hr`, which sorts the results in human-readable format in reverse order (largest first). Finally, `head -5` takes the top 5 lines from the sorted output, effectively showing the 5 largest files or directories.

##### Remember, the power of the command line comes from the ability to chain commands together to perform complex tasks with a single line of code.