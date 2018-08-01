# Linux Command Line Interface (CLI) Fundamentals (Pluralsight)

#### Instructor: Andrew Mallett

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

```bash
echo "marco" > file1
echo "polo" > file2
cat file1 file2
marco
polo
```

with only one file as param: show file content

```bash
cat notes.txt
this is the content of notes.txt
```

cat -vet [file]: show EOL markers

```bash
cat -vet doc.txt
line1$
another line$
and another one$
```

- tac: cat, but in reverse lines order

```bash
cat myfile
this is line 1
this is line 2
tac myfile
this is line 2
this is line 1
```

- show linux runtime config data:
  - proc file systema: /proc dir
    - dirs named as numbers are the processes
    - other files are config files

### head and tail

first/last n lines of a file

[tail|head] -n [num-lines][file] or tail -[num-lines][file]

```bash
head -2 myfile
I'm line 1
And I'm line 2
tail -2 myfile
I'm line n-1
I'm line n
```

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

```bash
cut -d: -f1 file
```

### more and less

- page through
- `more`: for longer files
- `less`: "less is more"
  - allows pg up, pg down and search
- use `less` in almost all cases

## 4. Basic File Management

### cp and mv

`cp`: copy

```bash
ls
file1.txt readme.md index.js
cp file1.txt /home/user1/report.txt
ls
file1.txt readme.md index.js
```

`mv`: move

```bash
ls
file1.txt readme.md index.js
mv index.js /tmp
ls
file1.txt readme.md
```

- params:
  - `-i`: interactive
  - `-R`: recursive
  - `-a`: !!! **maintain file user permissions** !!!

### rm and rmdir

- params:
  -rf:  
  -i:

### ls

- `-a`: all files
- `-t`: order by last date
- `-d`: directories only
- `-l`: list mode
- `-h`: human readable

```bash
ls
d2       f1  f2.gz  f4.gz  forscript.sh  myscript.sh
destiny  f2  f3.gz  file3  mydocs

ls -l
total 44
drwxr-xr-x 2 fbbs fbbs 4096 jul 29 19:59 d2
drwxr-xr-x 2 fbbs fbbs 4096 jul 29 19:58 destiny
-rw-r--r-- 1 fbbs fbbs    6 jul 31 21:48 f1
-rw-r--r-- 1 fbbs fbbs    5 jul 31 21:48 f2
-rw-r--r-- 1 fbbs fbbs   33 jul 28 19:57 f2.gz
-rw-r--r-- 1 fbbs fbbs   33 jul 28 19:57 f3.gz
-rw-r--r-- 1 fbbs fbbs   33 jul 28 19:57 f4.gz
-rw-r--r-- 1 fbbs fbbs   13 jul 31 21:56 file3
-rwxr-xr-x 1 fbbs fbbs   74 jul 28 21:42 forscript.sh
drwxr-xr-x 6 fbbs fbbs 4096 jul 31 22:04 mydocs
-rwxr-xr-x 1 fbbs fbbs  337 jul 28 21:36 myscript.sh

ls -a
.   d2       f1  f2.gz  f4.gz  forscript.sh  myscript.sh
..  destiny  f2  f3.gz  file3  mydocs .hidden_file

ls -lh
total 44K
drwxr-xr-x 2 fbbs fbbs 4,0K jul 29 19:59 d2
drwxr-xr-x 2 fbbs fbbs 4,0K jul 29 19:58 destiny
-rw-r--r-- 1 fbbs fbbs    6 jul 31 21:48 f1
-rw-r--r-- 1 fbbs fbbs    5 jul 31 21:48 f2
-rw-r--r-- 1 fbbs fbbs   33 jul 28 19:57 f2.gz
-rw-r--r-- 1 fbbs fbbs   33 jul 28 19:57 f3.gz
-rw-r--r-- 1 fbbs fbbs   33 jul 28 19:57 f4.gz
-rw-r--r-- 1 fbbs fbbs   13 jul 31 21:56 file3
-rwxr-xr-x 1 fbbs fbbs   74 jul 28 21:42 forscript.sh
drwxr-xr-x 6 fbbs fbbs 4,0K jul 31 22:04 mydocs
-rwxr-xr-x 1 fbbs fbbs  337 jul 28 21:36 myscript.sh
```

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

`mkfifo` creates files of type _*pipe*_. It allows the system a simple way to execute **inter-process communication**

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

### procps package

debian procps package content:

```bash
dpkg -L procps
```

### uptime

Tell us how long the system has been running.

`uptime` reads:

- **/proc/uptime**: to know how log the system is up and also how log is idle
  - 2 numbers: uptime seconds and idle seconds
- **/proc/loadavg**: to know load averages (system processing load) and num. processes running
  - has the load values, the numbers of executing processes / active processes and the **last process id created**

### Jobs and Background tasks

Limitation: One console to run many tasks at the same time

- list all background jobs:

```bash
jobs
```

run a process in the background:

```bash
sleep 180
^Z # ctrl + Z to pause current execution
[1]+ 18747 suspended sleep 180
bg
[1]+ 18747 continued sleep 180
ps
PID TTY     TIME   CMD
......................
......................
................... ps
```

`bg` background the command that has focus (the `+` symbol). `[1]` indicates the job number. Without bg, the job will stay suspended

we can directly execute the command in the bakcground with `&`:

```bash
sleep 180 &
```

It's possible to select what job to be continued from the suspended ones:

```bash
jobs
[1]    suspended  sleep 20
[2]    suspended  sleep 20
[3]  - suspended  sleep 30
[4]  + suspended  sleep 50
bg %3
[3]    20597 continued  sleep 30
[3]    20597 done       sleep 30
jobs
[1]    suspended  sleep 20
[2]  - suspended  sleep 20
[4]  + suspended  sleep 50
```

we use `bg %[job id]` to continue the job execution in the background

To put a job back to the foreground we use the command `fg`:

```bash
sleep 10
^Z
[1]  + 21335 suspended  sleep 10
fg
[1]    21335 continued  sleep 10
```

We can indicate wich job to send back to the foreground. Similar to `bg`:

```bash
sleep 10
^Z
[1]  + 21519 suspended  sleep 10
sleep 10
^Z
[2]  + 21558 suspended  sleep 10
jobs
[1]  - suspended  sleep 10
[2]  + suspended  sleep 10
fg %2
[2]    21558 continued  sleep 10
```

> Obs: When the job execution is back to the background, there's no indication of _Done_ at the end of it's execution.

### Managing Processes

Displaying processes:

```bash
ps
PID TTY TIME CMD
8205 pts/28 00:00:00 zsh
22315 pts/28 00:00:00 ps
```

for more details use the **list**, `-l`, option:

```bash
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
0 S  1000  8205  8196  0  80   0 - 11924 sigsus pts/28   00:00:00 zsh
4 R  1000 24129  8205  0  80   0 -  7230 -      pts/28   00:00:00 ps
```

or the `-f` option for full details like the user name:

```bash
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
0 S  1000  8205  8196  0  80   0 - 11924 sigsus pts/28   00:00:00 zsh
4 R  1000 24129  8205  0  80   0 -  7230 -      pts/28   00:00:00 ps
```

or combining both:

```bash
F S UID        PID  PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
0 S breno     8205  8196  0  80   0 - 11924 sigsus 08:02 pts/28   00:00:00 /usr/bin/zsh
4 R breno    24898  8205  0  80   0 -  9342 -      10:55 pts/28   00:00:00 ps -lf
```

To see al the process we use the option `-e`, normally combined with `-l` or `-f`. Another options is to generate the **process tree** using the command `ps -ejH`.

Like other shell commands, we can pipe the ps output, for example:

```bash
ps -ef | grep username
```

or the shortcut version, `pgrep`:

```bash
pgrep -a chrome
```

`pgrep` return the list of ids of processes that matched the grep filter. The `-a` option returns all the process data and `-l` returns the id and process name

send kill signal to a process

```bash
sleep 60
^Z
ps
  PID TTY          TIME CMD
 8205 pts/28   00:00:00 zsh
22468 pts/28   00:00:00 sleep
22496 pts/28   00:00:00 ps
kill 22468
ps
  PID TTY          TIME CMD
 8205 pts/28   00:00:00 zsh
23115 pts/28   00:00:00 ps
```

The default kill signal is `-15`. Same as `-term` or `-sigterm`.
To really quit the process the signal must be `-9`. Same as `-kill` or `-sigkill`

`pkill` allows to search and kill a process by name. It's like a `ps` piped to `grep` following by the `kill` command.

`killall` sends multiple kill signals to n processes:

```bash
sleep 60&
sleep 60&
sleep 60&
killall sleep
[1]    27370 terminated  sleep 60
[2]  - 27395 terminated  sleep 60
[3]  + 27421 terminated  sleep 60
```

`kill -l` list all the possible kill signals to send

> Obs.: Some processes may not respond to the terminate signal (-15, -term or -sigterm), like bash. To actually kill these processes we need to send the kill signal (-9, -kill or -sigkill).

### Top

from the docs:

> _the **top** provides a dynamic real-time view of a running system. It can display system summary information as well as a list of processes or threads currently being managed by the Linux kernel._

```bash
top

top - 11:20:49 up 19 days, 18:54,  6 users,  load average: 1.02, 0.96, 0.94
Tasks: 388 total,   1 running, 335 sleeping,   0 stopped,   0 zombie
%Cpu(s): 16.8 us,  3.3 sy,  0.0 ni, 79.0 id,  0.4 wa,  0.0 hi,  0.5 si,  0.0 st
KiB Mem : 16299568 total,  2601532 free, 11795960 used,  1902076 buff/cache
KiB Swap:  1998844 total,   395580 free,  1603264 used.  3346320 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
15662 breno     20   0 1380748 194896  61336 S  17.3  1.2  25:24.12 chrome
23331 breno     20   0 2159404 382260  58904 S  14.6  2.3 275:29.50 compiz
...
```

we can capture info from `top` for only a period of time:

```bash
bash -n 2 -d 3
```

the command above captures 2 snapshots in a 3'' interval.

Some shortcuts:

- **l**: toggle top bar
- **t**: toggle tasks bar modes
- **m**: toggle memory utilization modes
- **f**: fields management
- **s**: on fields management screen, change the field to order by
- **k**: inform a process id to _kill_
- **r**: inform a process id to _renice_ (process priority)

## 7. Search Text Files Using Regular Expressions

## 8. Using vi to Edit Files From the CLI

```

```
