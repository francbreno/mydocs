# Linux Command Line Interface (CLI) Fundamentals (Pluralsight)

#### Andrew Mallett

## 1. Introduction

## 2. Working on the Command Line

### Physical Consoles

- Normally tty1 - tty6 (graphical normally is tty7)
- control + alt + f[id]
- in console (logout or ctrl + d => exit)
- who: who (all) is logged in the current terminal
- tty: terminal device

### Pseudo Consoles

- virtual consoles
- /dev/pts/x
- represent remote connections (SSH (encrypted) or TELNET (not encrypted))
- Os connections from the GUI
- w: show info about logged in users

### Others shells

- chsh -l: list all available shells
  - content from file /etc/shells
    default shell: /etc/passwd

### ZSH

- change default shell
  - chsh -s [path-to-shell]

### Bash History

- ~/.bash_history
- ![expr]: run the last command starting with expr
  - !$: the last argument
- !?[expr]: execute last command with expr anywhere
- ctrl + r: search in history

## 3. Analyze Text Files

### cat & tac:

- cat: join multiple files
  - with only one file as param: show file content
  - cat -vet [file]: show EOL markers
- tac: cat, but in reverse lines order
- show linux runtime config data:
  - proc file systema: /proc dir
    - number dirs are the processes
    - other files with configs

### head and tail

- top n lines of a file
- [tail|head] -n [num-lines][file] or tail -[num-lines][file]
- [tail|head] -f [file]: watch

### sort

- ordering
- -r: reverse order
- -u: unique results
- -t: delimiter
- -n:
- k[number]: column

### cut

- filter columns
  - option -d: especify the field split

### more and less

- page through
- more: for longer files
- less: "less is more"
  - allows pg up, pg down and search
- use less

## 4. Basic File Management

### cp and mv

- cp: copy
- mv: move
- params:
  - -i: interactive
  - -R: recursive
  - -a: maintain file user permissions

### rm and rmdir

- params:
  -rf:  
  -i:

### ls

- -a: all files
- -t: order by last date
- -d: directories only
- -l: list mode

### alias

- alias ls='ls --lha'

## 5. Command Line Streams and Pipes

### Redirect Output

#### standard output

Redirect standard output to file1 file (create if not exists. Else, overwrites it).

```bash
ls /home > file1
# same as
ls /home 1> file1
```

`1>` indicates the standard output.

Appending content to a file.

```bash
ls /home >> file1
```

#### standard error

redirect standard error output to the file error.log

```bash
ls /home /notexistentdir 2> error.log
```

#### combining standard output with standard error

```bash
ls /home /notexistentdir > file1 2> error.log
```

Appending log errors.

```bash
ls /home /notexistentdir > file1 2>> error.log
```

We cand send standard and error to the same destiny file.

```bash
ls /home /notexistentdir > file1 2>&1
```

If we don't need the output:

```bash
ls /home /notexistentdir > /dev/null 2>&1
```

**/dev/null** is a device file that doesn't exist.

### Noclobber

A shell option that prevent us overwriting files by accident.

```bash
ls /home > file1
set -o noclobber
ls /home > file1
bash: file exists: file1
```

We still can append content.

```bash
ls /home >> file1
```

If we need to overwrite the file:

```bash
ls /home >| file1
```

### Read from Standard input

### Pipes

Send output from one command to another command input.

We will use `|`, the **pipe character**, to acomplish the task.

```bash
ls -l /home | grep username > users.txt
```

Some usefull examples

Paginating over a long list of files

```bash
ls /etc | less
```

Filtering a content input

```bash
ls -l /etc | grep network
```

In the examples up above we created a pipe called **_unamed pipe_**. It's a pipe that is created for the current execution only.

Let's create another pipe. This pipe will be persisted in the file system. It's called **_named pipe_** and is created with the command `mkfifo`

`mkfifo` creates file of type _*pipe*_. It allows one way to the system to execute **inter-process communication**

```bash
mkfifo /tmp/piedpipper
ls -F /tmp/piedpipper
/tmp/piedpipper|
```

You can see the pipe character at the end of the output
of the command `ls -F /tmp/piedpipper` indicating that this file is of type **Pipe**.

Lets execute some task with the created pipe

First, let send something to the pipe. The process will stop waiting for someone to consume the pipe content

```bash
ls /etc > /tmp/piedpipper
```

Now, lets consume the pipe.

Open another terminal and execute the following command to read from the pipe:

```bash
wc -l < /tmp/piedpipper
```

After consuming the pipe output the processes are done.

### the command tee

`tee` allows us to send the output to a file and the standard output at the same time.

```bash
ls /tmp | tee tmpfile.txt
```

## 6. Create, Kill and Monitor Processes

## 7. Search Text Files Using Regular Expressions

## 8. Using vi to Edit Files From the CLI
