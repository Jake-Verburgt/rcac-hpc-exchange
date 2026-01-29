# What is Unix?

[Back to Week 1](./index.md)

## The Unix Operating System


Unix is a broad family of multitasking, multi-user operating systems,
stemming from the original AT&T Unix, developed by Bell Labs in the
early 1970s. It is often associated with its rich tradition with
command-line environments, the UNIX "shell", and the C-programming
language.

Unix can also refer to a trademark, where other operating systems must
conform to a certain set of standards to be considered a "UNIX"
operating system.

Unix has many different variants:

* Genetic UNIX (such as FreeBSD)
* Branded UNIX (such as macOS)
* Functional UNIX (such as Linux)

Genetic UNIX means that the operating system can directly trace
its lineage back to the original Unix operating systems from the
70s. Branded UNIX is an operating system that has paid to have
the trademark of UNIX bestowed upon it, and has to fit a set of
standards to be considered such. Lastly, functional UNIX refers
to operating systems that follow the same standards as UNIX and
look/feel like UNIX, but do not pay to have the same trademark.

## Why Unix?

There are many reasons why one would want to use a UNIX operating
system for servers (both in HPC and Cloud scenarios).

* Performance
* Compatibility
* Configurability
* Multi-user
* Cost
* Programming environment

Firstly, UNIX systems are usually **lightweight**, meaning they
don't have all the features that many modern operating systems
have, which means that they can run much faster than other
operating systems out there.

Next, many different applications and libraries are **compatible**
with UNIX systems, meaning that a lot of different workflows
can be run on a UNIX system.

UNIX systems also often offer much more **flexibility** when it
comes to the system configuration, allowing the system to be
adapted to a plethora of different use cases.

One of the most important aspects of UNIX systems is that they
are **multi-user**, or multiple users can use the system at the
same time. Imagine if supercomputers were limited to one user
at a time\! Science would grind to a halt very quickly.

UNIX systems are also often **cheaper, or even free**, compared to  their counterparts, which is valuable in an enterprise situation,
where you may have thousands of users.

Lastly, UNIX systems often have primarily **text-based interfaces**,
as opposed to GUI-based ones such as Microsoft Windows. In
addition, there are many compilers and libraries that are needed
for scientific programming that are readily available for UNIX
systems, such as **MPI** and **BLAS** implementations.

## Unix components

UNIX systems are typically made up of three main components
that we will discuss in the following text. The three
components are:

* The File system hierarchy standard
* The Executable and library format
* The shell

### File system hierarchy

In UNIX systems, there is a conventional layout of the
file system directories. And there are repeated structures in
different places in the file system. There are standard
structures, names and purposes for different directories.
Some names exist only at the root of the file system and others
are repeated in layers for a similar purpose or function.
Some common directories you may see are:

* **/bin** - where binaries and executables go
* **/share** - where application specific data goes
* **/lib** - includes the libraries necessary for the binaries
* **/etc** - often contains system configuration files

Other top-level directories that you would commonly see are:
**/boot, /dev, /sys, /proc, /mnt, /tmp** and others.

### Everything is a file

A core UNIX design principle is that **everything is treated as a file**.

This means:

* System configuration is stored in plain text files  
* Devices (such as keyboards, disks, and GPUs) appear as files  
* Process and kernel information is exposed through virtual files  
* Inter-process communication often uses file-like interfaces  

---

### Metadata & Permissions

Every file has **metadata** that describes:

* **Ownership** — which user owns the file  
* **Group** — which group owns the file  
* **Permissions** — who can read, write, or execute it  
* **Timestamps** — when the file was created, modified, and accessed  

These properties form the basis of UNIX security and access control.

---

### Executables and libraries

Programs in UNIX are just files.

* **Executables** are files that can be run as programs.  
* **Libraries** are shared code files that executables depend on at runtime.

The operating system uses file metadata and standardized binary formats to determine **how programs are loaded, linked, and executed**.

---

### The Shell

The **shell** is the primary interface for interacting with a UNIX system.

It provides:

* A command-line environment  
* Program execution  
* File management  
* Scripting and automation  

The shell acts as the bridge between the user and the operating system, enabling both interactive work and powerful automation. We will explore the shell in much more detail in the next section.

Next section: [The Shell](./shell.md)