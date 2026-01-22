# Scripting

[Back to Week 3](./index.md)

In this section there are a couple of main
concepts that we are going to discuss:

* What are shell scripts?
* Where can scripts and other programs be stored?
* Environment variables
* Login profiles
* Program exit status

## What is a shell script?

A shell script is a text file of actions you want
the computer to take. The first line, called the
"shebang" defines what shell interpreter to be
used.

The lines after the shebang are effectively a series of commands



Following is a simple script we will use as an
example in the following sections:

```bash title="hello.sh" linenums="1"
#!/bin/bash

echo 'Hello, World!'
```

Notice the first line is the "shebang" that starts
with a `#!`, followed by the interpreter, which in
this case is the bash shell. We then have the `echo`
program, which just prints out the argument(s) to
the console.

!!! note "Quotes"
     Bash (and other shells) often use whitespace to delimit arguments to programs. Quotations keep arguments with spaces together.

     Double quotes and single quotes work differently in bash: double quotes allow expansion of variables and some other kinds of things, whereas single quotes are verbatim. Please use your command-line editor to copy this into a file named `hello.sh`.


A couple of helpful programs that you may want to
use:

### Helpful programs

| Program | Action | Example |
|---|---|----|
| `echo` | Print statements |`echo "Hello, World!"`|
| `hostname` | Name of the server you are logged into | `hostname -f` | 
| `date` | Current date and time (many format options) |`date`|


## Running Scripts
There are two ways to execute shell scripts:

1) Invoking it with the appropriate shell

```bash
$ bash hello.sh
Hello, World!
```

2) Refer to it as a program directly by its path

```bash
$ ./hello.sh
-bash: ./hello.sh: Permission denied
```

!!! failure "Execution Permissions"
    Why did this happen? What can we do to check the permissions? Use the command `ls -l hello.sh` to see the permissions:

    ```
    $ ls -l hello.sh
    -rw-r--r-- 1 username student 22 Oct 11 01:44 hello.sh
    ```

The file `hello.sh` doesn't have the **execute** bit set, so we can't run it as a program. Please feel free to refer to [Week 1 Filesystems](../week1/filesystem.md#permissions) for a refresher on permissions.

You can change file and directory permissions
using the `chmod` program:

```bash
$ chmod +x hello.sh
$ ls –l hello.sh
-rwxr-xr-x 1 username student 22 Oct 11 01:44 hello.sh
$ ./hello.sh
Hello, World!
```

This changes the execute permission from:

<span class="perm-type">-</span><span class="perm-user">rw-</span><span class="perm-group">r--</span><span class="perm-other">r--</span>username student 34 Jan 22 14:13 hello.sh

to 

<span class="perm-type">-</span><span class="perm-user">rwx</span><span class="perm-group">r-x</span><span class="perm-other">r-x</span>username student 34 Jan 22 14:13 hello.sh


For a quick refresher on what permissions are, take a look at [Navigating Filesystems](../week1/filesystem.md#permissions) from week 1.

The `chmod` program allows you to add and remove read(`r`)/write(`w`)/execute(`x`) permissions for the <span class="perm-user">user</span> (`u`), <span class="perm-group">group</span> (`g`), and <span class="perm-other">others</span> (`o`) with the following syntax:

```bash
chmod [ugo][-+][rwx] file
```


??? question "How would we remove read permissions on a
file for both the file *group* and *others*?"
     ```
     chmod go-r file.sh
     ```


## `$PATH` variable 

You may have noticed that we needed to execute our file relative to our current directory (i.e `./hello.sh` instead of just `hello.sh`). If we try to run just  `hello.sh`, we will get a "command not found" error:

```bash
$ hello.sh
hello.sh: command not found
```

In order for a shell to be able to find this little program and use it as a command, we need to add the directory it is located in to be part of our `PATH` variable. (We'll talk more on shell variables in a bit).

Essentially,the `PATH` variable controls where the shell looks for executable programs to run as commands. You can see all the directories that are searched for commands with the following command:

```bash
echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:
```
Every command that we run is located in one of these directories! For example, we can see that the program that is executed by the `ls` command is located in  `/usr/bin/`, which is listed in our `PATH` variable!

```bash
$ which ls
/usr/bin/ls
```

It's common to make a `bin` directory in your home directory, and store any executable files you want to run as commands there. Let's make a `bin` directory, move our program there, and add the `bin` directory to our `PATH` variable!

```bash 
$ mkdir ~/bin
$ mv hello.sh ~/bin/hello
$ export PATH=$PATH:~/bin
$ hello
Hello, World!
```

!!! tip
    Also, remember that file extensions are optional in UNIX (you can remove the `.sh` from the file name).

??? question "What happens if we close the shell and reopen it?"
     It doesn't matter if we change directories, but the change to our `PATH` variable is not kept if we close the shell.

     There is a file called the `.bashrc` file that you can add the `export PATH=$PATH:~/bin` command to, which will be ran whenever you open a terminal!

## Environment variables

Environment variables are similar to variables in
other programming languages. But, they also have
some important distinctions that set them apart.

Variables can have a *local* scope (in a script)
or a *global* scope (with child processes).

There are three types of variables:

* Simple
* Magic
* Special

You can also modify software (or shell) behavior
by environment variables.

### Simple variables

They are like other programming languages, and you can define them yourself! There are no types (mostly everything is text).

Simple assignment:
```bash
x=1
y=foo
```

You can access the variable with with the `$var` or `${var}`:

```bash
echo $x
1
echo ${y}
foo
```

Variables are *conventionally* uppercase, but
it's not necessary.

```bash
NAME="some data"
```
Variables are just dumb text, unlike other
programming languages. While there are some
exceptions, there are no complex data
structures.

```bash
MYDATA=1,2,3
```

There is also a weird thing where 0 is true
and 1 is false, which we will discuss later.

```bash
COND=1
```

Lastly, you can use **Command substitution** to set a variable to the output of a command:

```bash
HOST=$(hostname -f)
```

---

Variables also have a scope. If you define
a variable, it is only visible in local
scope (current script) by default.

```bash
X=true
```


To propagate the variable down to child
processes, you need to `export` it.

```bash
export X
```

You can also declare the variable and export
it on the same line.

```bash
export THING=0
```
You can also declare multiple variables on
one line.

```bash
export THING=0 DATASET=foo.in
```


### Magic variables

There are a couple of "magic" variables which
are not like other programming languages.

```bash
echo $RANDOM
```
Will always give you a random value, even if
you set it to be something else.


```bash
echo $SECONDS
```
Will print the number of seconds that have passed since the shell has been opened. 


### Special variables

There are many different special variables
you can use.


| Variable | Meaning |
|---|---|
| `$0` ... `$99` | x-th argument of script/function |
| `$_` | Last argument of script/function |
| `$@` | All arguments of script/function (whitespace) |
| `$#` | Count of arguments of script/function |
| `$?` | Exit status of last process (more on this later) |
| `$!` | Process ID of last process (more on this later) |


### System Variables
There are also special software/shell environment
variables that change the behavior of different
things.

| Variable | Meaning |
|---|---|
| `PATH` | Directories containing programs |
| `MANPATH` | Directories containing manual pages |
| `LD_LIBRARY_PATH` | Directories containing shared libraries |
| `PKG_CONFIG_PATH` | Directories containing package configuration |


## Login profile

As we discussed earlier, the `PATH` variable is reset
every time you log into the cluster, or open a new
terminal. What if we wanted to have it be modified
every time we started a new session? There's a
solution for that! It's called a **login profile**!
Typically, on Linux you would use the `~/.bashrc`
file, but on the UNIX system we have on the clusters
it's contained in the `~/.bash_profile` hidden file.

```bash  title="/home/username/.bashrc" linenums="1"

#aliases
alias rm="rm -i"
alias Negishi='ssh username@.negishi.rcac.purdue.edu'


#variable exports
export PATH=$PATH:$HOME/bin
```


Let's go ahead and make that now:

```bash
cd ~
vim .bashrc
#or
vim .bash_profile
# copy/paste the bashrc contents!
```

There are many things you can put in the login
profile to configure your personal session, but
the two that we are going to talk about are:

* Aliases
* Variables

### Aliases

An alias is a verbatim command substitution that
happens on the command line when invoked like a program.
Here's one example:
```bash
alias rm="rm -i"
```
Which would make sure that every time you run the
`rm` program, it's run in interactive mode. However,
other programs will not recognize aliases as
commands.

### Variables

You can also add variables (like `PATH`) to your
login profile.

```bash
export PATH=$PATH:$HOME/bin
```
This will add the newly created `bin` folder in your
home directory to the `PATH` variable every time
you create a new session. Note that we have `$PATH`
at the beginning of the assignment, that's so we
don't only have `$HOME/bin` as our entire `PATH`.
Because that would mean the only place that the
computer looks for programs would be in your
home directory. Which would be bad.


!!! warning 
    Something that you should **NOT** do is load modules (especially conda modules) in your login profile. This can mess up the rest of the start up process and can cause weird errors.

## Exit status

Successful programs should exit with a zero (`0`).
And any non-zero exit status should be considered
an error condition. Often, programs will document
the meaning of their different exit status values
in their manual page.

```bash hl_lines="4"
$ hello
Hello, world!

$ echo $?
0
```
Control flow (if-else conditional statements) in
shell scripts often hinge on the success or
failure of commands. Because of this, 0 means
true and 1 (non-zero) means false in the UNIX
shell. This is opposite of almost everywhere
else.

```bash hl_lines="2 6"
$ true
$ echo $?
0

$ false
$ echo $?
1
```

Next section: [Control Flow](./control-flow.md)