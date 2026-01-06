# Applications


[Back to Week 2](./index.md)


In this section, we'll talk a little bit about
how to use different applications on our clusters.

First, we have an example python script that we
will be using just as a test problem for our
sessions this week as well as in [week 4](../week4/index.md).

Please use your favorite command line text editor
(like `vim` or `nano`) to add this file to your cluster
account and save it as `example.py`.

```python title="example.py" linenums="1"
import numpy as np

A, B = np.random.rand(2, 10_000, 10_000)
C = np.matmul(A, B)

print(C.mean())
```
This script creates two random matrices, of size
ten thousand by ten thousand and multiplies them
together. It then prints out the mean of the
resultant matrix. It's not useful scientifically
but it does take some time for the computer to
do this problem, so it's helpful to use as a
toy problem.

Next, let's try running it:


```
$ python example.py
-bash: python: command not found
```
Wait, what? Why didn't that work? The system doesn't know about python yet, we haven't loaded it.

There are too many versions and conflicting software to
have every version of every application `pre-installed`
for all users all the time. To get around this problem,
we use a module system called `Lmod`. This module system
can load and unload programs and commands within your shell environment.

As an example, run the command `module list` to list all
currently loaded modules:

```
$ module list

Currently Loaded Modules:
1) gmp/6.2.1 3) mpc/1.1.0 5) gcc/12.2.0 7) openmpi/4.1.4 9) rcac -> modtree/cpu
2) mpfr/4.0.2 4) zlib/1.2.13 6) numactl/2.0.14 8) xalt/3.0.2 (S)

Where:
S: Module is Sticky, requires --force to unload or purge
```
There are many different `module` commands that we can use
to learn about what's available on the system and what our
current environment is:

| Command | Description |
|--------|-------------|
| `module list`   | List currently loaded modules |
| `module load`   | Load a module by name (and version) |
| `module unload` | Unload an already loaded module |
| `module avail`  | Search for currently available modules |
| `module spider` | Recursively search the entire module tree |
| `module purge`  | Unload all currently loaded modules |
| `module reset`  | Revert to the default module set |
| `module show`   | Show the module definition |
| `module help`   | Show help message for a module |


Let's check for available python modules with `module avail`
```
$ module avail python
No module(s) or extension(s) found!
```
Well, that's because we provide python via the `conda`
package manager...

```   
$ module avail conda

---- Core Applications ----
conda/2024.09
```

Now let's load conda to get our python loaded in!
```
$ module load conda

$ which python
/apps/external/apps/conda/2024.09/bin/python
```
!!! tip "`which` command"
     * `which` is a nice program that will tell us where the specified program is coming from. Remember that everythign is a file! `which` tells you what file starts the program when you run a command.

Now that we have python ready and our script is written,
let's run it (it may take a couple minutes to run):
```
   $ python example.py
   2499.9118
```


!!! bug  "Numpy error"
     If you get an error that says something like this:

    ```
    Traceback (most recent call last):
    File "/home/username/example.py", line 1, in <module>
    import numpy as np
    ModuleNotFoundError: No module named 'numpy'
    ```

    This has to do with our (RCAC's) conda installation.
    There's a long story about why this is happening, but
    there's a simple solution to it. We're going to make
    our own conda environment to install `numpy` for
    ourselves.

    Run these three lines of code to create the environment,
    activate it, and then run our example:
    ```
    $ conda create -y -n example numpy
    $ conda activate example
    $ python example.py
    2499.9118
    ```

Remember that when we log onto the clusters, we are typically placed on the login nodes, which aren't meant for computationally expensive tasks. This is a computationally intensive task that we should instead run on the compute nodes, which we'll cover in the next section!


Next Section: [Cluster Job Submission](./slurm-basics.md)