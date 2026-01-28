# Managing output with pipes

[Back to Week 3](./index.md)

When dealing with processes, the output
of those processes is important. In this
section, we will discuss how to manage
the output of processes in different ways.

The main concepts that we will go over are:

* File descriptors and standard input/output
* Input/output redirection
* Special files
* Command pipelining
* Useful commands for processing output

Programs often write output to the console,
this is called *standard output*. The shell
defines `stdin`, `stdout`, and `stderr` for
every program.

### Standard file descriptors

| # | Common name | Description |
|---|---|---|
| `0` | `stdin` | Attached to programs for input data |
| `1` | `stdout` | Attached to programs for output data |
| `2` | `stderr` | Attached as a secondary output for errors |




You can also redirect these file descriptors
to attach to files other than the console.

### Redirection notation

| Notation | Meaning |
|---|---|
| `<   FILE` | Read `stdin` from `FILE` |
| `>   FILE` | Write `stdout` to `FILE` |
| `>>  FILE` | Append `stdout` to `FILE` |
| `2>  FILE` | Write `stderr` to `FILE` |
| `2>> FILE` | Append `stderr` to `FILE` |
| `|   PROGRAM` | Join `stdout` to `stdin` of `PROGRAM` |

The following example redirects the `stdout` from our program (the "Hello, world!" text it prints) from the console into the file `message.txt`.

```bash
$ hello > message.txt
```   
You can concatenate (print) the contents of
files with the `cat` program:

```bash
$ cat message.txt
Hello, world!
```
We can also use the output (`stdout`) of the `cat` program (again, the "Hello, World!" text) to the input (`stdin`) of another program with a pipe(`|`). We'll pass the output to the `wc` "word count" program which can count the words, lines, chars, etc of the provided input.

```
$ cat message.txt | wc --chars
14
```

??? question "How might we see how many files we have in our current directory?"
    We could get a list of our files with `ls`, and  pass the output to `wc` to count the number of lines.

    ```bash
    ls | wc --lines
    55
    ```





<!-- ### Useful programs

| Program | Meaning |
|---|---|
| `cat` | Concatenate (or slice) one or more files |
| `head/tail` | Limit output to first/last `N` lines (default 10) |
| `less/more` | Page output interactively (for ease of viewing) |
| `cut` | Slice columns from input by delimiter |
| `sort/uniq` | Reorder and filter input |
| `grep/sed` | Filter/modify input based on regular expression |
| `awk` | Streaming programming language (superpowers) |
| `wc` | Reduce output by counting (words, lines, etc) |
| `xargs` | Transpose output as positional arguments to next program | -->



Next section: [Processes](./processes.md)