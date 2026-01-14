# Cluster Job Submission (Slurm)


[Back to Week 2](./index.md)


In this section, we will talk about what
exactly is a scheduler and how to
interact with it to get your work
done.

First, what is a scheduler? We use a
scheduler called **Slurm** which is the
most common one used by major computing
centers. It is a resource manager and a
workload manager. Essentially, there are
a finite number of compute nodes, many
users of the system and even more jobs
to be run. the scheduler tracks resource
allocation requests and manages the
execution of those jobs. Users package
their work (directly or indirectly) into
self-contained **shell scripts** submitted
to Slurm.

![slurm_submission](../assets/images/slurm_submission.png)

So what is a `resource allocation request`
or a `job`? It's essentially a shell script
that encapsulates everything for the work
to be done. This shell script can be submitted
to the scheduler via the `sbatch` program.
The details of what resources are requested
are commonly inside the script itself, but
can also be passed to the `sbatch` program
manually. Let's take a look at what's in `myjob.sh`

```bash title="myjob.sh" linenums="1"
#!/bin/bash
#SBATCH  --account=hpcexc
#SBATCH  --partition=cpu
#SBATCH  --qos=normal
#SBATCH  --time=0-1:00:00
#SBATCH  --nodes=1
#SBATCH  --ntasks-per-node=1

cd $SLURM_SUBMIT_DIR

module load conda
conda activate example
python example.py
echo "Script is finished! Exiting..."
```
To submit this script to the scheduler, we can simply run:

```bash
sbatch myjob.sh
```
Let's take a closer look at the individual pieces of information we need to provide Slurm for it to schedule our job. 

## Account
```bash linenums="1" hl_lines="2"
#!/bin/bash
#SBATCH  --account=hpcexc
#SBATCH  --partition=cpu
#SBATCH  --qos=normal
#SBATCH  --time=0-1:00:00
#SBATCH  --nodes=1
#SBATCH  --ntasks-per-node=1
```
The first of which is what account to submit
that job to. Accounts are typically associated with a research group or department. Each account will have access to a limited amount of resources that they have purchased.

Use the `slist` program to show which Slurm accounts
are available for you to submit to, and what their current usage is:

```
$ slist
                  Current Negishi Accounts
==============================================================================
               |              CPU Partition              |    AI Partition
Accounts       |   Total     Queue      Run      Free    |  GPU Hours Balance
============== | ========= ========= ========= ========= | ===================
lab_queue      |       128        32        64        32 |                 0.0
```
!!! note "Queues"
    The `lab_queue` here is just a placeholder, in reality,this should be something similar to your PI's username. In the following examples, please substitute `lab_queue` with an account found in the output of `slist`.
    
    If your `slist` output is empty, that means that you aren't associated with any groups that have resources allocated to their accounts on that cluster.

## Partition

```bash linenums="1" hl_lines="3"
#!/bin/bash
#SBATCH  --account=hpcexc
#SBATCH  --partition=cpu
#SBATCH  --qos=normal
#SBATCH  --time=0-1:00:00
#SBATCH  --nodes=1
#SBATCH  --ntasks-per-node=1
```

Remember that not all backend/compute nodes are the same! Some nodes have special hardware like GPUs or increased RAM, or are set aside for a dedicated use like machine learning training. To manage this, we use partitions, which are just subsets of the compute nodes. We need to tell Slurm which partition we intend on using.

To show the different partitions
available on the cluster, run the `showpartitions`
program:

```
$ showpartitions
Partition statistics for cluster negishi at Thu Jul 17 16:12:58 EDT 2025
Partition       #Nodes     #CPU_cores  Cores_pending   Job_Nodes MaxJobTime Cores Mem/Node
Name  State   Total  Idle  Total   Idle Resorc  Other   Min   Max  Day-hr:mn /node     (GB)
cpu      up     446     0  57088   2078      0  15973     1 infin   infinite   128     257
highmem  up       6     0    768    236      0   2114     1 infin   infinite   128    1031
gpu      up       5     3    160    132      0      0     1 infin   infinite    32     515
```
To see what the different node types mean, use
the `sfeatures` program:

```
$ sfeatures
NODELIST    CPUS  MEMORY   AVAIL_FEATURES   GRES
a[000-449]  128   257400   A,a              (null)
b[000-005]  128   1031600  B,b              (null)
g[000-004]  32    515500   G,g,MI210,mi210  gpu:3
```
Use `AVAIL_FEATURES` as tags as a constraint
on clusters with more distinct hardware
types.

Use the `-C` or `--constraint` option with `sbatch` to
target one of the feature tags.

## Quality of Service (QoS)

```bash linenums="1" hl_lines="4"
#!/bin/bash
#SBATCH  --account=hpcexc
#SBATCH  --partition=cpu
#SBATCH  --qos=normal
#SBATCH  --time=0-1:00:00
#SBATCH  --nodes=1
#SBATCH  --ntasks-per-node=1
```

Lastly, something you may want to specify is the
*Quality of Service* (QoS) for the job. The QoS determines the priority and some constraints of your job. The two primary QoS values will be `normal` and `standby`:

* The `normal` QoS gives your job increased priority, but subtracts from your accounts available resources. 
   * `normal` jobs can run for up to 2 weeks.

* The `standby` QoS doesn't subtract from your accounts resources, but are given very low priority to run.
   * `standby` jobs are only allowed to run up to 4 hours


## Time and Resources

```bash linenums="1" hl_lines="5-7"
#!/bin/bash
#SBATCH  --account=hpcexc
#SBATCH  --partition=cpu
#SBATCH  --qos=normal
#SBATCH  --time=0-1:00:00
#SBATCH  --nodes=1
#SBATCH  --ntasks-per-node=1
```

We may also need to specify what resources we want to request, and for how long

* `--time` is the maximum time your job will run. If your job has not yet finished in this amount of run time, it will be cancelled.

* `--nodes` is the number of nodes you want to request. 

* `--ntasks-per-node` is the number of CPUs

!!! note
    This is an incomplete list, and the required options may vary by cluster and partition. For example, some clusters will require you to request memory with `--mem`, or to list how many GPUs you want access to with `--gres=gpu:`. See the user guide for the cluster you are using for more details!

## Submission
Once that is written, submit it to the
scheduler with the `sbatch` program:

```bash
$ ls
example.py  myjob.sh  ...

$ sbatch myjob.sh
Submitted batch job 32209880
```
You have now submitted your first supercomputing
resource allocation request! This job ID number
is helpful to note down as it can be used elsewhere.

!!! note 
    The output of your job will, by default, be saved in
   files with this ID (e.g. `slurm-32209880.out`).

The job that we submitted requested 1 cores for 1
hour from your lab's account, to the CPU part of
the cluster, using the `normal` QoS.

Following is a list of common Slurm resource
parameters that you may want to specify in your
shell script:

### Common Slurm resource parameter reference
 | Shortcut | Long form option | Meaning |
|---|---|---|
| `-A` | `--account` | Account (default: `lab_queue`) |
| `-p` | `--partition` | Partition (default: `cpu`) |
| `-q` | `--qos` | Quality of Service (default: none) |
| `-J` | `--job-name` | Job name (default: `<script name>`) |
| `-t` | `--time` | Walltime limit |
| `-N` | `--nodes` | Number of nodes (default: 1) |
| `-n` | `--ntasks`, `--ntasks-per-node` | Nunber of Slurm tasks (default: 1) |
| `-c` | `--cpus-per-task` | Cores per task (default: 1) |
| `--mem` | `--mem-per-cpu` | Memory (default: ~2GB per core) |
| `-G` | `--gpus`, `--gpus-per-node` | Number of GPUs (default: 0) |
| `-o` | `--output` | File path for application output |

You can also use the command `man sbatch` to
learn more about different parameters

## Job Monitoring and Cancelling

You can use the `squeue` program to list currently scheduled
(pending and running) jobs. By default it will show all jobs
from all users on the cluster, which leads to a lot of
output. You can limit this to just your jobs with the `--me` flag:

```bash
$ squeue --me
JOBID      USER     ACCOUNT      PART QOS     NAME       NODES TRES_PER_NODE   CPUS  TIME_LIMIT ST TIME
32541229   username rcac         cpu  normal  interactiv     1 N/A                8       30:00  R 0:09

```

<!-- Quiz: What option do we need to limit the output to a
specific account? Specific user? Only our own jobs?

.. admonition:: Answer
   :collapsible: closed

   Specific account: `squeue -A ACCOUNT_NAME`

   Specific user: `squeue -u USERNAME`

   Only our own jobs: `squeue \-\- me` -->

To learn more about the parameters of a single job, you can
use the `jobinfo` program. To use `jobinfo`, the command
would be `jobinfo JOB_ID`, where the `JOB_ID` is replaced
with the job ID mentioned above (which you can also check
with the `squeue` program).

```
$ jobinfo 32209880
Name : myjob.sh
User : username
Account : lab_queue
Partition : cpu
Nodes : a305
```
There are also `jobenv`, `jobcmd`, and `jobscript`
programs that tell you more information about the
job as it was submitted.

!!! Note
    These four commands: `jobinfo`, `jobenv`, `jobcmd`, and `jobscript` are all RCAC-specific. It is not guaranteed that other HPC centers will have these programs implemented.

To cancel a job, use the `scancel` program. It used by
running `scancel JOB_ID`, where `JOB_ID` is replaced
with the job ID mentioned before.

```
$ scancel 32209880
```
<!-- Quiz: Using the `man` program, what option do we need
to cancel all our own jobs?

.. admonition:: Answer
   :collapsible: closed

   To cancel all our own jobs: `scancel \-\-me` -->

!!! warning
    Cancelling an application this way isn't very "nice", in that it immediately stops everything and can cause problems if in the middle of file operations.

## Interactive Jobs
To get an interactive job (or essentially a shell
on a compute node), use the `sinteractive` program
(which is RCAC specific). You will need to specify
the same parameters as with `sbatch` (e.g. account,
partition, QoS, cores, nodes, time).


``` hl_lines="1 8"
username@login03.negishi:[~] $ sinteractive -A lab_queue -p cpu -q standby -c 4 -t 00:10:00
salloc: Pending job allocation 19809515
salloc: job 19809515 queued and waiting for resources
salloc: job 19809515 has been allocated resources
salloc: Granted job allocation 19809515
salloc: Waiting for resource configuration
salloc: Nodes a195 are ready for job
username@a195.negishi:[~] $
```


Notice that before the `sinteractive` program was run,
we were on `login03.negishi` and after it was run, we
are now on `a195.negishi`, this is a good way to tell
if you are running on a compute node, or on a login
node.

To get out of the interactive slurm job, simply
run the `exit` command and you'll be returned to
the login node you were on previously.

## Good citizenship

Last, but not least, there are four main points to touch
on about good citizenship on HPC resources:

* Do not request for excessive resources knowingly
   (don't ask for a large memory node if it's not needed)
* Do not abuse file systems (heavy I/O for `/depot` space, use `/scratch` instead)
* Do not submit lots of tiny jobs, instead use the pilot-job pattern with a workflow tool
* Do not submit jobs and camp (don't submit a GPU job from the Gateway for 24 hours so it's ready for you in the afternoon and then forget about it)


## Open OnDemand Interactive Apps
If you'd rather avoid running jobs on the command line entirely, RCAC offers Open OnDemand interactive apps that handle the submission to the compute backend for you. 


Most notably, we have an "Open OnDemand Desktop" application, which will give you a virtual desktop (running on a cluster backend node) available in your browser. This can be incredibly useful if you need to run graphical applications on RCAC, which don't run well


![Open OnDemand Desktop](../assets/images/ood_desktop.png)


Continue to [Week 3](../week3/index.md)