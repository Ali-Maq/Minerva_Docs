Logging In
Connect to the Mount Sinai network either by connecting  to the LAN or MSSM Green while on campus or by using the VPN connection if not on campus. If using VPN, make sure you click on the “Tunnel” button on the VPN splash screen.

Use minerva.hpc.mssm.edu as the host name. This is a round-robin redirect to one of the 3 actual login nodes.

Start up your terminal emulation program and enter:

ssh yourUserID@minerva.hpc.mssm.edu
 

When prompted for your password, enter you Mount Sinai password followed immediately by the 6 digit VIP credential:

Password:  myH@rd2gu3$$p@$$word123456  (← this will not be echoed to the terminal.)

where 123456  would be the 6 digit credential from your VIP token.

 

If you are using X11 graphics, you will need to use the  -X  option to allow X11 forwarding to your workstation.

ssh -X yourUserID@minerva.hpc.mssm.edu

 

Two Factor Authentication
Minerva requires Two-Factor authentication at all times to log in. The first component is a memorized password. The second component is a one-time use, time sensitive, generated credential. Mount Sinai uses Symantec VIP to generate the one time use credential.

To set up two factor authentication for Symantec VIP, visit the ASCIT website. Symantec VIP produces a 6 digit code using a software token which can be installed via the ASCIT link on a variety of devices.

 

Virtual Private Network (VPN) Tunneling
Connection to Minerva from off campus requires the use of VPN and the F5 Big-IP software to be installed on your local workstation.

To setup VPN access, see https://itsecurity.mssm.edu/vpn-steps/

To setup the F5 Big-IP software to enable tunneling on Windows or MAC, see https://itsecurity.mssm.edu/vpn-access-selection/

 

Instructions on F5 Setup on Ubuntu
On your local workstation, go to https://mshmsvpn.mssm.edu and log in with your password and VIP credentials. The welcome page will have many boxes. Click on linux_deb and download the f5 software. Open a terminal on your workstation and enter one of the following commands:

 sudo apt install /absolute/path/to/deb/file
or
 sudo dpkg -i /absolute/path/to/deb/file
/opt/f5 will be created. Click on “tunnel” and on the f5 popup, “Choose” /opt/f5/vpn/f5vpn and you should be in.

Instructions for F5 Centos/Red Hat systems
On your local workstation, go to https://mshmsvpn.mssm.edu and log in with your password and VIP credentials. The welcome page will have many boxes. Click on linux_rpm and download the f5 software. Open a terminal on your workstation and enter one of the following commands:

For Centos:

	sudo yum localinstall /absolute/path/to/rpm/file
or
	sudo rpm -i absolute/path/to/rpm/file
For Red Hat/Fedora:

	sudo rpm -i /absolute/path/to/rpm/file
or
	sudo dnf localinstall /absolute/path/to/rpm/file
/opt/f5 will be created. Click on “tunnel” and on the f5 popup, “Choose” /opt/f5/vpn/f5vpn and you should be in.

 

Login Nodes:
Minerva currently has several login nodes which are used to access the compute cluster. Some are for general use and others are private to specific groups. Both the public and private login nodes are connected to the campus network allowing access only on campus or via tunneling over Mount Sinai’s VPN if off campus.

There are currently three login nodes available for general use, minerva12, minerva13 and minerva14.You may connect to one of them through one of two round-robin Domain Name Server (DNS) load balancing names or you may specify one of them explicitly, if you prefer one over the other.For example, if you have a disconnected screen session running on one of the nodes, you will want to log onto that particular node if you want to reconnect.

The addresses of the nodes are:

minerva.hpc.mssm.edu – round-robin redirect
chimera.hpc.mssm.edu – round-robin redirect
minerva12.hpc.mssm.edu – specific login node
minerva13.hpc.mssm.edu – specific login node
minerva14.hpc.mssm.edu – specific login node
Suggestion: Use the name minerva.hpc.mssm.edu for your connections, as it will continue to work in the future even if the login nodes are changed.

From Windows
To log in from a Windows machine, you will need a terminal emulator. There are two that we recommend.

MobaXterm ( https://mobaxterm.mobatek.net ) an all-in-one product that provides a terminal emulator, builtin X11 server, multiple tabbed windows, scp, sftp, etc.

PuTTY ( https://www.putty.org/ ) a widely used SSH client and associated utilities (scp, sftp, etc). PuTTY does not come with a built in X11 server. Users will have to install Xming ( https://sourceforge.net/projects/xming/ ) if they wish to use X11 graphics.

From MAC
MAC machines come with a console window already installed. However, you may want to install a more versatile terminal emulator such as iTerm2 ( https://iterm2.com )




Storage and File Permission Management
Default Storage Allocations
Each user will automatically be allocated, free of charge:

30GB Home folder on SLOW Network File System (NFS) storage. This is generally used for configuration files and scripts. The amount of storage is fixed and cannot be increased. This folder is not purged and is backed up. The path to this folder is /hpc/users/<username>. For performance-critical purposes, please use the GPFS, the General Parallel File System, a massively parallel distributed file system.
100GB Work folder on our GPFS file system for their general use. The amount of storage is fixed and cannot be increased. The path to this folder is /sc/arion/work/<username>. This folder is not purged but is not backed up. It is the user’s responsibility to back up all important data to archival storage or other storage resources.
Scratch storage on our GPFS file system. Scratch storage is a 100TB shared pool for all users, intended for temporary production work and not long-term storage. A per-user quota of 15TB is implemented to prevent one user from monopolizing the scratch storage. It is the maximum scratch storage one user can use. This DOES NOT guarantee that you can get 15TB in scratch storage. Please note scratch usage frequently reaches its maximum. Files in scratch are not backed up and are automatically deleted after 14 days. The path to this folder is /sc/arion/scratch/<username>.
For HIPAA compliance, the default permission for these files is set to read and write only by the file’s owner (rwx——). If a user wants to share a file or files or the directory with others, then the chmod, setfacl and getfacl commands can be used. Please see documents or video on basic Linux file permissions for greater detail.

 

Purge and Quote Policy on Scratch Storage
Purge Policy: The scratch file system cannot be used for long term storage and files on scratch are not backed up or guaranteed by Minerva. In the event of a file system crash or purge, files in scratch directories cannot be recovered. It is the user’s responsibility to back up all important data to archival storage or other storage resources.Files are exempt from purge if they have been written to or read within the last 14 days. To see the list of files that will be purged you can use:

find  /sc/arion/scratch/$USER -atime +14
Special Note: Modifying file access times (using touch or any other method) for the purpose of circumventing purge policies may result in the loss of access to the scratch file systems. Also note that GPFS maintains a “Born On” date that is unaffected by touch or any standard unix command.

Quota policy: Scratch usage frequently reaches its maximum. This causes unnecessary errors for users who rely on it to pull large data temporarily. To ameliorate the issue, a per-user quota (15T) is implemented in the scratch folders. This policy is to avoid any one user from consuming all off the scratch space and reduce related job failures.

 

Project Storage
The bulk of our storage is allocated to the various projects underway on Minerva. If more storage than the default is required for laboratory projects the PI or a delegate can request that a project be created.

Only the PI or a delegate can authorize changes to a project, e.g., add/remove users, change ownership of files, grant access by researchers not in the project group, etc.

To request a project allocation please fill out the Minerva Project Allocation Form.

The cost for project folders is $119/TB/yr and we charge the PI every 2 months at $9.92/TB/mo. The storage charge includes the cost of all Minerva services including computation and is set yearly by the Mount Sinai Compliance and Finance Departments. Complete the Minerva Storage Information Collection Form, which will need to be filled out to specify the fund number/s that will be used to pay for the storage.

Project directories are not backed up. Minerva team recommends that critical information be backed up to archival storage independently. See TSM page. To request an increase in the size of your project directory, please submit a request to hpchelp@hpc.mssm.edu.

 

Storage and File Permission Management
When a project is created:

A unix group is created with the acronym chosen by the PI for the project.
Researchers designated by the PI or a delegate are made members of this group which will give them access to the project folder.
A folder is allocated with the approved allocation of storage on our GPFS file system.
The owner of the folder is the PI if they have a Minerva account. Otherwise another member of the project group.
The group owner is the project group.
Protections of the top level directory are set to rwxrws—. The group sticky bit is set to ensure that the group ownership of the files and folders below the top level is set to the project group
This folder is not purged but is not backed up. We recommend that you use the Archive feature of our Tivoli Storage Manager system to save your important data.
The allocated amount can be expanded by modifying the requested allocation on the Allocation Request Form. To do this, open the allocation website; click on the “Returning” button in upper right corner; enter your return code that was sent to you when the allocation submission was acknowledged.
The path to the project folder is /sc/arion/projects/<project_acronym>
By default, project directories are created with the project group as the group owner. Therefore members of your project have read and write permissions. Team members must be a member of that project group to access the project directory.

To find out which Unix group owns a project directory (assume your group’s name is projectA):

$ ls -ld  /sc/arion/projects/yourGroupDirectory
drwxrwx--- 2 48 projectA 4096 2011-03-05 12:42 yourGroupProjectDirectory/
 

File System Access Control Lists (FACL)
An ACL is a list of permissions that are associated with a directory or file. It defines which users and groups are allowed to access a particular directory or file. This is over and above the standard unix permissions. This can be used to authorize users or groups that are not normally associated with the files to have access to those specific files and no other.

Typically, one would like to give access to a sub-folder of a project to a specific user but no other folder. To do this, use the setfacl command (see the man page for setfacl and getfacl). As an example, to give a user, userj01, read and execute access to the files and folders in /sc/arion/projects/myproject/toBeShared:
cd /sc/arion/projects/myproject
setfacl -R -m u:userj01:rX toBeShared

The capital “X” means only give execute privileges if it make sense, e.g., to folders.

To complete the grant of access, you need to give the user at least execute privileges to all the directory files in the path. So:

setfacl -m u:userj01:x /sc/arion/projects/myproject

Since the user did not get read privileges, they won’t be able to read the myproject directory file and see what you have stored underneath. The only way to get to the toBeShared folder is to reference it with the full path name. But from there on down they will be able to list the file names and read the files.

getfacl is the companion command that will list the ACL’s on a file. For example:

code>getfacl /sc/arion/scratch/fludee01
yields:
getfacl: Removing leading ‘/’ from absolute path names

# file: sc/arion/scratch/fludee01
# owner: fludee01
# group: hpcstaff
user::rwx
user:pintod02:--x
user:linx19:r-x
group::r-x
mask::r-x
other::---
 

In this example, user pintod01 can only “pass through” the folder but not read it. User linx19 can both read and “pass through” the directory folder.

To remove access, use -x option:

setfacl  -x u:pintod02,u:linx19 /sc/arion/scratch/fludee01

getfacl /sc/arion/scratch/fludee01	
# file: sc/arion/scratch/fludee01
# owner: fludee01
# group: hpcstaff
user::rwx
group::r-x
mask::r-x
other::---
How to Check your GFPS Quota
The work, scratch and project storage all utilize GPFS, the General Parallel File System, a massively parallel distributed file system. To check your GPFS quotas, you can run the following script, either per user or per project is listed:

# showquota -h
usage: showquota [-h] [-u USER | -p PROJECT]

optional arguments:
  -h, --help            Show this help message and exit
  -u USER, --user USER  Show quota for user in groups
  -p PROJECT, --project PROJECT
                        Show quota for a project
Please also understand that the quota is based on GPFS’s quota report that is queried every 15 mins and might have a fudge factor (including some -ve numbers that you should ignore for small set of files) to account for files being created or deleted.

Summary table for the 4-ish folders you can have:

 

Home	
/hpc/users/<userid>

$ quota -s

30GB quota
Slow. Use for “config” files, executables, but NOT DATA
NOT purged and is backed up
Work	
/sc/arion/work/<userid>

$ df -h /sc/arion/work/<userid>

100GB quota
Fast, keep your personal data here
NOT purged but is NOT backed up
Scratch	
/sc/arion/scratch/<userid>

$ df -h /sc/arion/scratch

Free for all, shared by all; for temporary data
Current size is about 100TB
Purge every 14 days and limit per user is 10TB
Project	
/sc/arion/projects/<projectid>

$ df -h /sc/arion/projects/<projectid>

PI’s can request project storage by submitting an allocation request, and get approval from allocation committee; see fee and schedule policy.
Not backed up
Incurs charges of $119/TiB/year
Arion storage (/sc/arion) – IBM Spectrum Scale (formerly GPFS) is a high-performance, clustered file system designed for scalability and parallel access. However, maintaining strict POSIX semantics and supporting atomic operations (like file creation, deletion, renaming, and write visibility guarantees) comes with trade-offs — especially in interactive commands like ls, rm, mv, etc., on large or highly concurrent directories. GPFS is not designed for quick response on interactive commands and is more designed for sequential streaming IO.
It is expected that interactive commands like ls , rm , mv will be slow occasionally. You can always run “unalias ls” and then run “ls” in your directory to verify that this is the case , “ls” should return instantly.

Scientific Computing and Data / High Performance Computing / Documentation / Software Environment: Lmod

Software Environment: Lmod
We have implemented the Lmod modules software package to manage nearly all software installed in Minerva and the associated user environment. Modules provide an easy mechanism for updating a user’s environment, especially the PATH, MANPATH, NLSPATH, and LD_LIBRARY_PATH environment variables, to name a few. The distinct advantage of the modules approach is that the user is no longer required to explicitly specify paths for different software versions nor need to try to keep the related environment variables coordinated. With the modules approach, users simply “load” and “unload” modules to control their environment.

Module Command
The “module avail” or “ml avail” command lists all the modules available on Minerva.



Another way to search for modules is with the “module spider” or “ml spider” command:

$ ml spider

This will show a compact listing of all available modules on the system.

The “module avail” and “module spider” commands have search capabilities:

$ ml avail lib

will list for any modulefile where the name contains the string “lib”.

To have a module loaded to your current environment:

$ module load modulefile [modulefile2]...

Alternatively, you can specify:

$ ml modulefile [modulefile2]...

To load a specific version of a package to your environment, load the module using its full name with the version explicitly specified, e.g,

$ ml python/2.7.16
Otherwise the default version will be loaded.

To remove a module from your current environment:

$ module unload modulefile
or
$ ml -modulefile
To remove all the modules from your current environment:

$ module purge
or
$ ml purge
The following command demonstrated the true advantage of using modules. Different versions of entire software packages can be replaced with a single module command:

$ module switch [modulefile_old] [modulefile_new]
Alternatively, you can simply load the new module:

$ module load modulefile_new
or
$ ml modulefile_new
This will replace the old version and the “module list” or “ml” command will list all the modules which are currently loaded in your environment.



Frequently used collection of modules can be saved using the ”module save” command so that they can be restored in your environment later using the “module restore” command:



To get more information about a specific module use the module help command to display the “help” information contained within the given module file.



The module show command allows you to see exactly what a given “modulefile” will do to your environment, such as what will be added to the PATH, MANPATH, etc. environment variables:



To get a usage list of module options type the “ml help” command.

 

Setting Up Your Own Modules
You can point the module command to your own modulefiles using the “module use” command. This command updates the environment variable MODULEPATH, which defines where modulefiles are to be found. Documentation on preparing modulefiles can be found online (but feel free to poke around our modulefiles for some examples. They live in /hpc/packages/minerva-XYZ/modulefiles, where XYZ is either centos7 or common.




Load Sharing Facility (LSF) Job Scheduler
When you log into Minerva, you are placed on one of the login nodes. Minerva login nodes should only be used for basic tasks such as file editing, code compilation, data backup, and job submission. The login nodes should not be used to run production jobs. Production work should be performed on the system’s compute nodes. Note that Minerva compute nodes do not have internet access by design for security reasons. You need to be on the Minerva login node or one of the interactive nodes to talk to the outside world.

Contents
Running Jobs on Minerva Compute Nodes
Submitting Batch Jobs with bsub
Commonly Used bsub Options
Sample Batch Jobs
Array Jobs
Interactive Jobs
Useful LSF Commands
Pending Reasons
LSF Queues And Policies
Multiple Serial Jobs
Minerva Training Session
 

Running Jobs on Minerva Compute Nodes
Access to compute resources and job scheduling are managed by IBM Spectrum LSF (Load Sharing Facility) batch system. LSF is responsible for allocating resources to users, providing a framework for starting, executing and monitoring work on allocated resources, and scheduling work for future execution. Once access to compute resources has been allocated through the batch system, users have the ability to execute jobs on the allocated resources.

You should request compute resources (e.g., number of nodes, number of compute cores per node, amount of memory, max time per job, etc.) that are consistent with the type of application(s) you are running:

A serial (non-parallel) application can only make use of a single compute core on a single node, and will only see that node’s memory.
A threaded program (e.g. one that uses OpenMP) employs a shared memory programming (SMP) model and is also restricted to a single node, but can run on multiple CPU cores on that same node. If multiple nodes are requested for a threaded application it will only see the memory and compute cores on the first node assigned to the job while those on the rest of the nodes are wasted. It’s highly recommended the number of threads is set to that of compute cores.
An MPI (Message Passing Interface) parallel program can be distributed over multiple nodes: it launches multiple copies of its executable (MPI tasks, each assigned unique IDs called ranks) that can communicate with each other across the network.
An LSF job array enables large numbers of independent jobs to be distributed over more than a single node from a single batch job submission
 

Submitting Batch Jobs with bsub
To submit a job to the LSF queue you must have a project allocation account first and it has to be provided using the “-P” option flag. To see the list of project allocation accounts you have access to:

$ mybalance
If you need access to a specific project account, you will have to have the project authorizer (owner/delegate) send us a request at hpchelp@hpc.mssm.edu.

A batch job is the most common way users run production applications on Minerva. To submit a batch job to one of the Minerva queues use the bsub command:

bsub [options] command

$ bsub -P acc_hpcstaff -q premium -n 1 -W 00:10 -o hello.out echo "Hello World!":
In this example, a job is submitted to the premium queue (-q premium) to execute the command

echo "Hello World!"
with a resource request of a single core (-n 1) and the default amount of memory (3 GB) for a 10 min. of walltime (-W 00:10) under a specific project allocation account that Minerva staff members have access to (-P acc_hpcstaff). The result will be written to a file (-o hello.out) with a summary of resource usage information when the job is completed. The job will advance in the queue until it has reached the top. At this point, LSF will allocate the requested compute resources to the batch job.

Typically, the user submits a job script to the batch system.

bsub [options] < YourJobScript

Here “YourJobScript” is the name of a text file containing #BSUB directives and shell commands that describe the particulars of the job you are submitting.

$ bsub < HelloWorld.lsf
where HelloWorld.lsf is:

#!/bin/bash#BSUB -P acc_hpcstaff#BSUB -q premium#BSUB -n 1#BSUB -W 00:10#BSUB -o "hello. Out"      echo "Hello World!"
Note that If an option is given on both the bsub command line and in the job script, the command line option overrides the option in the script. The following job will be submitted to the express queue, not to the premium queue as specified in the job script.

$ bsub -q express < HelloWorld.lsf
 

Commonly Used bsub Options
-P acc_Project Name	Project allocation account name (required)
-q queue_name	Job submission queue (default: premium)
-W WallClockTime	Wall-clock limit in form of HH:MM (default 1;00:
-J job_name	Job name
-n Ncore	Total number of cpu cores requested (default: 1)
-R rusage[mem=#]	
Amount of memory per core in MB (default: rusage[mem=3000]).

Note that this is not per job.

Max memory per node: 160GiB (compute), 326GB (GPU), 1.4TiB (himem), 1.9TB (himem-GPU-A100-80GB)
-R span[ptile=#n’s per node]	Number of cpu cores per physical node
-R span[hosts=1]	All cores on same node
-R himem	Request high memory node
-o output_file	Direct job standard output to output_file (without -e option error goes to this file). LSF will append the job’s output to the specified output_file. If you want the output to overwrite the existing output_file, use to “-oo” option instead.
-e error_file 	Direct job error output to error_file. To overwrite existing error_file, use the “-eo” option instead
-L login_shell	Initializes the execution environment using the specified login shell
For more login details about the bsub options, click here.

Although there are default values for all batch parameters except for the -P, it is always a good idea to specify the name of the queue, the number of cores, and the walltime for all batch jobs. To minimize the time spent waiting in the queue, specify the smallest walltime that will safely allow the job to complete.

LSF inserts job report information into the job’s output. This information includes the submitting user and host, the execution host, the CPU time (user plus system time) used by the job, and the exit status. Note that the standard LSF configuration allows to email the job’s output to the user when the job completes, BUT this feature has been disabled on Minerva for the reason of stability.

 

Sample Batch Jobs
Serial Jobs
A serial job is one that only requires a single computational core. There is no queue specifically configured to run serial jobs. Serial jobs share nodes, rather than having exclusive access. Multiple jobs will be scheduled on an available node until either all cores are in use, or until there is not enough memory available for additional processes on that node. The following job script requests a single core and 8GB of memory for 2 hours in the “express” queue:

#!/bin/bash
#BSUB -J mySerialJob                           # Job name
#BSUB -P acc_YourAllocationAccount   # allocation account
#BSUB -q express                                  # queue
#BSUB -n 1                                            # number of compute cores = 1
#BSUB -R rusage[mem=8000]              # 8GB of memory
#BSUB -W 02:00                                   # walltime in HH:MM
#BSUB -o %J.stdout                              # output log (%J : JobID)
#BSUB -eo %J.stderr                             # error log
#BSUB -L /bin/bash                               # Initialize the execution environment

 

ml gcc
cd /sc/arion/work/MyID/my/job/dir/
../mybin/serial_executable < testdata.inp > results.log

Multithreaded Jobs
In general, a multithreaded application uses a single process which then spawns multiple threads of execution. The compute cores must be on the same node and it’s highly recommended the number of threads is set to the number of compute cores. The following example requests 8 cores on the same node for 12 hours in the “premium” queue. Note that the memory requirement ( -R rusage[mem=4000] ) is in MB and is PER CORE, not per job. A total of 32GB of memory will be allocated for this job.

#!/bin/bash
#BSUB -J mySTARjob                   # Job name
#BSUB -P acc_PLK2                     # allocation account
#BSUB -q premium                        # queue
#BSUB -n 8                                    # number of compute cores
#BSUB -W 12:00                            # walltime in HH:MM
#BSUB -R rusage[mem=4000]      # 32 GB of memory (4 GB per core)
#BSUB -R span[hosts=1]            # all cores from the same node
#BSUB -o %J.stdout                     # output log (%J : JobID)
#BSUB -eo %J.stderr                   # error log
#BSUB -L /bin/bash                      # Initialize the execution environment

 

module load star                           # load star module
WRKDIR=/sc/arioin/projects/hpcstaff/benchmark_star

 

STAR –genomeDir $WRKDIR/star-genome –readFilesIn Experiment1.fastq –runThreadN 8 -outFileNamePrefix Experiment1Star

MPI Parallel Jobs
An MPI application launches multiple copies of its executable (MPI tasks) over multiple nodes that are separated but can communicate with each other across the network. The following example requests 48 cores and 2 hours in the “premium” queue. Those 48 cores are distributed over 6 nodes (8 cores per node) and a total of 192GB of memory will be allocated for this job with 32GB on each node.

#!/bin/bash
#BSUB -J myMPIjob                     # Job name
#BSUB -P acc_hpcstaff                # allocation account
#BSUB -q premium                       # queue
#BSUB -n 48                                 # total number of compute cores
#BSUB -R span[ptile=8]              # 8 cores per node
#BSUB -R rusage[mem=4000]     # 192 GB of memory (4 GB per core)
#BSUB -W 02:00                          # walltime in HH:MM
#BSUB -o %J.stdout
#BSUB -eo %J.stderr
#BSUB -L /bin/bash

 

module load selfsched
mpirun -np 48 selfsched < test.inp

Please refer to LSF Queues and Policy for further information about available queues and computing resource allocation. Please refer to the GPU section for further information about options relevant to GPU job submissions.

Array Jobs
Sometimes it is necessary to run a group of jobs that share the same computational requirements but with different input files. Job arrays can be used to handle this type of embarrassingly parallel workload. They can be submitted, controlled, and monitored as a single unit or as individual jobs or groups of jobs. Each job submitted from a job array shares the same job ID as the job array and are uniquely referenced using an array index.
To create a job array add an index range to the jobname specification:

#BSUB -J MyArrayJob[1-100]

In this way you ask for 100 jobs, numbered from 1 to 100. Each job in the job array can be identified by its index, which is accessible through the environment variable “LSB_JOBINDEX“. LSF provides the runtime variables%Iand%J, that correspond to the job array index and the jobID respectively. They can be used in the #BSUB option specification to diversify the jobs. In your commands, however, you have to use the environment variablesLSB_JOBINDEXand LSB_JOBID.

#!/bin/bash
#BSUB -P acc_hpcstaff
#BSUB -n 1
#BSUB -W 02:00
#BSUB -q express
#BSUB -J “jobarraytest[1-10]”
#BSUB -o out.%J.%Im
#BSUB -e err.%J.%I

 

echo “Working on file.$LSB_JOBINDEX”

A total of 10 jobs, jobarraytest[1] to jobarraytest[10], will be created and submitted to the queue simultaneously by this script. The output and error files will be written to out.JobID.1 ~ out.JobID.10 anderr.JobID.1 ~ err.JobID.10, respectively.
Note: We also have an in-house tool “selfscheduler” that can submit large numbers of independent serial jobs, each of which is short (less than ca. 10 min.) and doesn’t have to be indexed by number, as a single batch.

Interactive Jobs
Interactive batch jobs give users interactive access to compute resources. A common use for interactive batch jobs is debugging and testing. Running a batch-interactive job is done by using the -I option with bsub.
Here is an example command creating an interactive shell on compute nodes with internet access:

bsub -P AllocationAccount -q interactive -n 8 -W 15 -R span[hosts=1] -XF -Is /bin/bash

This command allocates a total of 8 cores on one of the interactive compute nodes, reserved exclusively for jobs running in interactive mode. All cores are on the same node. The -XF option is for the X11 forwarding to enable the graphics applications to open on your screen. Once the interactive shell has been started, the user can execute jobs interactively there. In addition to those dedicated nodes, regular compute nodes that have no internet access can also be used to open an interactive session by submitting a batch interactive job to the non-interactive queues such as “premium”, “express” and “gpu” using the bsub -I, -Is, and -Ip options.

Useful LSF Commands
bjobs Show job status

Check the status of your own jobs in the queue. To display detailed information about a job in a multi-line format use the bjobs command with the “-l” option flag: bjobs -l JobID. If a job has already been completed the bjobs command won’t show the information about the job. At that point, one must invoke the bhist command to retrieve the job’s record from the LSF database.

bkill Cancel a batch job

A Job can be removed from the queue or be killed if it is running using the “bkill JobID" command. There are many ways to terminate a specific set of jobs of yours.

To terminate a job by its job name: bkill -J myjob_1
To terminate bunch of jobs using a wildcard: bkill -J myjob_*
To kill some selected array jobs: bkill JobID[1,7,10-25]
To kill all your jobs: bkill 0
bmod Modify the resource requirement of a pending job

Many of the batch job specifications can be modified after a batch job is submitted and before it runs. Typical fields that can be modified include the job size (number of nodes), queue, and wall clock limit. Job specifications cannot be modified by the user once the job enters the RUN state. The bmod command is used to modify a job’s specifications. For example: bmod -q express jobID changes the job’s queue to the express queue bmod -R rusage[mem=20000] jobID changes the job’s memory requirement to 20 GB per core. Note that -R replaces ALL R fields, not just the one you specify.

bpeek Displays the stdout and stderr output of an unfinished job.

bqueues Displays information about queues.

bhosts Displays hosts and their static and dynamic resources.

For the full list of LSF commands, see IBM Spectrum LSF Command Reference.

Pending Reasons
There could be many reasons for a job to stay in the queue for longer than usual; The whole cluster may be all busy; Your job requests a large amount of compute resources such as amount of memory and compute cores so that no compute node can satisfy the resource requirement at the moment; Your job would overlap with a scheduled PM. To see the pending reason use the bjobs command with the “-l” flag:

$ bjobs -l

LSF Queues And Policies
The command to check the queues is “bqueues“. To get more details about a specific queue, type “bqueues -l ” e.g., bqueues -l premium
Current Minerva Queues:
* The default memory for all queues are set to 3000 MB.

Queue	Description	Max Walltime
premium	Normal submission queue	144 hrs
express	Rapid turnaround jobs	12 hrs
interactive	Jobs running in interactive mode	12 hrs
long	Jobs requiring extended runtime	336 hrs
gpu	Jobs requiring gpu resources	144 hrs
gpuexpress	Short jobs requiring gpu resources	15 hrs
private	Jobs using dedicated resources	Unlimited
others	Any other queues are for testing by the Scientific Computing group	N/A
 

Policies
LSF is configured to do ” absolute priority scheduling(APS)” backfill. Backfilling allows smaller, shorter jobs to use otherwise idle resources.
To check the priority of the pending jobs, you can do $bjobs -u all -aps
In certain special cases, the priority of a job may be manually increased upon request. To request priority change you may contact ISMMS Scientific Computing Support at hpchelp@hpc.mssm.edu. We will need the job ID and reason to submit the request.

 

Multiple Serial Jobs
How to submit multiple serial jobs over more than a single node:
Sometimes users want to submit large numbers of independent serial jobs as a single batch. Rather than using a script to repeatedly call bsub, a self-scheduling utility (“selfsched”) can be used to have multiple serial jobs bundled and scheduled over more than a single node with one bsub command.

Usage:
In your batch script, load the self-scheduler module and execute it using mpi wrapper (“mpirun”) in a parallel mode:

module load selfschedmpirun selfsched < YourInputForSelfScheduler
where YourInputForSelfScheduler is a file containing serial job commands like,

/my/bin/path/Exec_1 < my_input_parameters_1 > output_1.log/my/bin/path/Exec_2 < my_input_parameters_2 > output_2.log/my/bin/path/Exec_3 < my_input_parameters_3 > output_3.log...
Each line has 2048 character limit and TAB is not allowed.
Please note that one of compute cores is used to monitor and schedule serial jobs over the rest of cores, so the actual number of cores used for the real computation is (the total number of cores assigned – 1).

A simple utility (“PrepINP”) is also provided to facilitate generation of YourInputForSelfScheduler file. The self-scheduler module has to be loaded first.

Usage:

module load selfschedPrepINP < templ.txt > YourInputForSelfScheduler
templ.txt contains input parameters with the number fields replaced by “#” to generate YourInputForSelfScheduler file.

Example 1:

1 10000 2  F                             ← start,  end,  stride,  fixed field length?/my/bin/path/Exec_# < my_input_parameters_# > output_#.log
The output will be

/my/bin/path/Exec_1 < my_input_parameters_1 > output_1.log/my/bin/path/Exec_3 < my_input_parameters_3 > output_3.log/my/bin/path/Exec_5 < my_input_parameters_5 > output_5.log.../my/bin/path/Exec_9999 < my_input_parameters_9999 > output_9999.log
Example 2:

1 10000 1  T                            ←  start,  end,  stride,  fixed field length?5                                       ←  field length/my/bin/path/Exec_# < my_input_parameters_# > output_#.log
The output will beserial j

/my/bin/path/Exec_00001 < my_input_parameters_00001 > output_00001.log/my/bin/path/Exec_00002 < my_input_parameters_00002 > output_00002.log/my/bin/path/Exec_00003 < my_input_parameters_00003 > output_00003.log.../my/bin/path/Exec_10000 < my_input_parameters_10000 > output_10000.log
Improper Resource Specification
Minerva is a shared resource. Improper specification of job requirements not only wastes them but also prevents other researchers from running their jobs.

1. If your program is not explicitly written to use more than one core, specifying more than one core is wasting cores that could be used by other users. E.g.

IF

bsub -n 6 < sincgle_core_program

THEN

6 cores allocated

program runs on 1 core

5 cores are idle

2. If your program can use more than one core but does not use things like mpirun, mpiexec, or torchrun, then all cores must be on the same node. This is a Shared-memory MultiProcessing program (SMP). You must specify -R span[hosts=1] to ensure all cores are on the same node.

E.g:

IF

bsub -n 6 < SMP_program

THEN

6 cores are allocated: 1 on node1,2 on node2, 3 on node3

1 core on node1 is used to run the program

The other 5 cores sit idle

or, perhaps,

IF

bsub -n 6 < SMP_program

THEN

6 cores are allocated: 3 on node1,1 on node2, 2 on node3

3 cores on node1 are used to run the program

The other 3 cores sit idle

But with -R span[hosts=1]

IF

bsub -n 6 -R span[hosts=1] < SMP_program

THEN

6 cores are allocated: 6 on node1

All 6 cores on node1 are used to run the program

3. Memory specified by -R rusage[mem=xxx] is reserved for your use and cannot be used by anyone else until released at end of job. If most/all of the memory on a node is reserved, no additional jobs can run even if there are still CPUs available.

Example:

IF

A 192GB node has 3 jobs dispatched to it

each job is requesting 1 core and 64GB of memory (-n 1 -R rusage[mem=64G] )

each is actually using only 1GB

THEN

All memory on the node is reserved so no other job can be dispatched to it.

Only 3GB of the memory is being used.

189 GB of memory and 43 cores are idle and cannot be used.

4. Check to see how much memory your program is actually using and request accordingly. If you are running a series of jobs using the same program, run one or two test jobs with a large amount of memory and then adjust for the production runs.

Test:

bsub -R rusage[mem=6G] other options < testJob.lsf

Look at output:

Successfully completed.

Resource usage summary:

CPU time : 1.82 sec.

Max Memory : 8 MB

Average Memory : 6.75 MB

Total Requested Memory : 6144.00 MB

Delta Memory : 6136.00 MB

Max Swap : –

Max Processes : 3

Max Threads : 3

Run time : 2 sec.

Turnaround time : 50 sec.

Test run shows this program only used 8MB maximum. Memory usage varies between run depending on what else is running on the node so change the amount of memory down but give yourself some “wiggle room”

bsub -R rusage[mem=15M] other options < productionJob.lsf

Successfully completed.

Resource usage summary:

CPU time : 2.93 sec.

Max Memory : 5 MB

Average Memory : 4.00 MB

Total Requested Memory : 15.00 MB

Delta Memory : 10.00 MB

Max Swap : –

Max Processes : 3

Max Threads : 3

Run time : 4 sec.

Turnaround time : 47 sec.



Minerva Training Session
On April 8, 2022, the Scientific Computing and Data team hosted a Training Class on LSF Job Scheduler. Click here for training session powerpoint slides.
Click here to see more about Minerva Training Classes.

General Purpose Graphics Processor Unit (GPGPU or just GPU) and GPU Etiquette
NVIDIA has made the computing engine of their graphics processing units (GPU’s) accessible to programmers. This engine is technically called the Compute Unified Device Architecture, CUDA for short, is a parallel computing architecture developed by NVIDIA used to render images that are to be displayed on a computer screen. These devices can be programmed using a variant of C or C++ and the NVIDIA compiler. While the CUDA engine is programmer accessible in virtually all of NVIDIA’s newer graphics cards, GPUs, they also make specially purposed devices that are used exclusively for computation called GPGPU or, more commonly, just GPU.
Minerva has a total of 75 GPU nodes. Please check below for more information.

12 Intel nodes each with 32 cores, 384GiB RAM, and 4 NVIDIA V100 GPU cards with 16GB memory on each card (see more)
8 Intel nodes each with 48 cores, 384GiB RAM, and 4 NVIDIA A100 GPU cards with 40GB memory on each card (see more)
2 Intel nodes each with 64 cores, 2TiB RAM, and 4 NVIDIA A100 GPU cards with 80GB memory on each card each with NVLINK connectivity
49 nodes in total, including 47 nodes with Intel Xeon Platinum 8568Y+ processors (96 cores, 1.5 TB RAM per node) and 4 NVIDIA H100 GPUs (80 GB each) connected via SXM5 with NVLink, as well as 2 nodes with Intel Xeon Platinum 8358 processors (32 cores, 512 GB RAM per node) and 4 NVIDIA H100 GPUs (80 GB each) connected via PCIe. The 2 additional nodes also feature 3.84 TB NVMe SSD storage (3.5 TB usable) (see more)
4 nodes, each with AMD Genoa 9334 processors (64 cores, 1.5 TB RAM per node) and equipped with 8 NVIDIA L40S GPUs per node, totaling 32 L40S GPUs across 4 nodes. Each processor operates at 2.7 GHz (see more)
Requesting GPU Resources
The GPU nodes must be accessed by way of the LSF job queue. There are nodes on the interactive, gpu and gpuexpress queues.

To request GPU resources, use the -gpu option of the bsub command. At a minimum, the number of GPU cards requested per node must be specified using the num= sub-option. The requested GPU model should be specified by using an LSF resource request via the -R option.

bsub -gpu num=2 -R a100
Full details of the -gpu option can be obtained here.

By default, GPU resource requests are exclusive_process.

The available GPU models and corresponding resource specifications are:

-R 	GPU MODEL 
v100 	TeslaV100_PCIE_16GB 
a100 	NVIDIAA100_PCIE_40GB 
a10080g 	NVIDIAA100_SXM4_80GB 
h10080g 	NVIDIAH100_PCIE_80GB 
h100nvl 	NVIDIAH100_SXM5_80GB 
l40s 	NVIDIAL40S_PCIE_48GB 
Specifying the gpu model number in the -gpu gmodel= option does not always work. The recommended way to specify the GPU model is via the -R option.

Also, CUDA 11.8 or higher is needed to utilize the h100 GPU devices.

Note that GPU resource allocation is per node. If your LSF options call for multiple CPUs ( e.g., -n 4 ) and LSF allocates these CPUs across more than one node, the number of GPU cards you specified in the -gpu num= parameter will be allocated to your job on each of those nodes. If your job cannot distribute its workload over multiple nodes, be sure to specify the option: -R span[hosts=1] otherwise you will be wasting resources.

If your program needs to know which GPU cards have been allocated to your job (not common), LSF sets the CUDA_VISIBLE_DEVICES environment variable to specify which cards have been assigned to your job.

Supplemental Software
One almost certainly will need auxiliary software to utilize the GPUs. Most likely the CUDA libraries from NVIDA and perhaps the CUDNN libraries. There are several versions of each on Minerva. Use:

ml avail cuda
and/or

ml avail cudnn
to determine which versions are available for loading.

For developers, there are a number of CUDA accelerated libraries available for download from NVIDIA.

Interactive Submission
Minerva sets aside a number of GPU enabled nodes to be accessed via the interactive queue. This number is changed periodically based on demand but is always a small number, e.g, 1 or 2. The number and type of GPU will be posted in the announcement section of the home page.

To open an interactive session on one of these nodes:

bsub -P acc_xxx -q interactive -n 1 -R v100 -gpu num=1  -W 01:00 -Is /bin/bash
Alternatively, one can open an interactive session on one of the batch GPU nodes. This is particularly useful if the interactive nodes do not have the model GPU you would like to use:

bsub -P acc_xxx -q gpu -n 1 -R a100 -gpu num=2  -W 01:00 -Is /bin/bash
Batch Submission
Batch submission is a straightforward specification of the GPU related bsub options in your LSF script.

bsub < test.lsf 
Where test.lsf is something like:

#BSUB -q gpu
#BSUB -R a100
#BSUB -gpu num=4
#BSUB -n 1
#BSUB -W 4
#BSUB -P acc_xxx
#BSUB -oo test.out

ml cuda 
ml cudnn 

echo “salve mundi” 
Accessing the Local SSD on the a100 GPU Nodes
To take advantage of the local 1.8 TB SSD, request the resource using the rusage specification, for example:

-R “rusage[ssd_gb=1000]”
This example will allocate 1000GB of dedicated SSD space to your job.

We would advise you to make your ssd_rg request to be <= 1,500 (1.5T).

The soft link, /ssd, points to the ssd storage. You can specify /ssd in your job script and direct your temporary files there. At the end of your job script, please remember to clean up your temporary files.

A foolproof way of doing this is to use LSF’s options for executing pre_execution commands and post_execution commands. To do this, add the following to your bsub options:

#BSUB -E “mkdir /ssd/$LSB_JOBNUM” #Create a folder before beginning execution
#BSUB -Ep “rm -rf /ssd/$LSB_JOBNUM” # remove folder after job completes
Inside your LSF script, use /ssd/$JOB_NUM as the folder in which to create and use temp files.

Monitoring GPU Usage
The LSF queuing system on Minerva is configured to gather GPU resource usage using <a “href=”https://developer.nvidia.com/dcgm””>NVIDIA Data Center GPU Manager (DCGM). This allows users to view the gpu usage of their finished jobs using bjobs -l -gpu if the job finished within the last 30 minutes or bhist -l -gpu otherwise.

The following is a sample of the report: (SM=streaming multiprocessors. i.e., the part of the GPU card that runs CUDA)

bjobs -l -gpu 69703953

HOST: lg07c03; CPU_TIME: 8 seconds
GPU ID: 2
Total Execution Time: 9 seconds
Energy Consumed: 405 Joules
SM Utilization (%): Avg 16, Max 100, Min 0
Memory Utilization (%): Avg 0, Max 3, Min 0
Max GPU Memory Used: 38183895040 bytes

GPU ID: 1
Total Execution Time: 9 seconds
Energy Consumed: 415 Joules
SM Utilization (%): Avg 20, Max 100, Min 0
Memory Utilization (%): Avg 0, Max 3, Min 0
Max GPU Memory Used: 38183895040 bytes
GPU Energy Consumed: 820.000000 Joules

Further Information:
CUDA Programming
Available Applications

GPU Etiquette
The GPU nodes in Minerva are in great demand. Because of the manner in which LSF allocates GPU resources to a job, it is important to specify the job requirements carefully. Incorrect LSF resource requests can cause resources to be unnecessarily reserved and, hence, unavailable to other researchers.

The bsub options that need particular attention are:
-n The number of slots assigned to your job.
-R rusage[mem=] The amount of memory assigned to each slot.
-gpu num= The number of GPU cards per node assigned to your job.
-R span[] The arrangement of the resources assigned to your job across Minerva nodes

-n

This option specifies how many job slots you want for your job. View this value as how many packets of resources you want allocated to your job. By default, each slot you ask for will have 1 cpu and 3GB of memory. For most cases, job slots and cpus are synonymous. Note that if your program is not explicitly coded to be parallelized, adding more cores will not affect the performance.

-R rusage[mem=]
This option specifies how much memory is to be allocated per slot. Default is 3GB. Note that request is per slot, not per job. The total amount of memory requested will be this value times the number of slots you have requested with the -n option.

-gpu num=

This option specifies how many GPU cards you want allocated per compute node, not slot, that is allocated to your job. The GPU cards/devices are numbered 0 through 3. However, for L40S GPUs, the devices are numbered 0 through 7.

Because your program needs to connect to a specific GPU device, LSF will set an environment variable, CUDA_VISIBLE_DEVICES, to the list of GPU devices assigned to your job. E.g, CUDA_VISIBLE_DEVICES=0,3. Do not change these values manually and, if writing your own code, you must honor the assignment. The installed software on Minerva that use GPUs honor these assignments.

-R span[]
This option tells LSF how to lay out your slots/cpus on the compute nodes of Minerva. By default, slots/cpus can be, and usually are, spread out over several nodes.

There are two ways that this option is generally specified: using ptile=x, to specify the tiling across nodes or hosts=1 to specify all slots are to be allocated on one node (only option).

Example A:

-R span[ptile=] where value is the number of slots/cores to be placed on a node.

e.g.

#BSUB -n 6	# Allocate 6 cores/slots total
#BSUB -R rusage[mem=5G]  #Allocate 5GB per core/slot
#BSUB -R span[ptile=4]   # Allocate 4 core/slots per node

#BSUB -gpu=2

#BSUB -R a100

Will result in 2 nodes being allocated: one with 4 cpus; 20GB (4*5GB) of memory; 2 A100 GPU cards and a second with 2 cpus; 10GB (2*5GB) of memory;2 A100 GPU cards.

Example B:
-R span[hosts=1] allocate all cores/slots to one host.

e.g.

#BSUB -n 6 
#BSUB -R rusage[mem=5G] 
#BSUB -R span[hosts=1] 

#BSUB -gpu=2

#BSUB -R a100

Will result in 1 node being allocated with 6 cpus; 30GB (6*5GB) of memory; 2 A100 GPU cards.

If you do not specify a span requirement, LSF will do what it thinks is best and may allocate only one slot per node so that, in the example above, you job would be tying up 12 GPU cards.

If your program is not a distributed program or is a Shared Memory Parallel (SMP) program, and most parallel programs in our environment are, only the resources on the first node of the nodes assigned to your job will be used. The remainder will be unavailable to other users and may cause significant backlogs in job processing.

Some hints that your program cannot run in a distributed manner are

Documentation does not say that it can.
The mpirun command is not used to start the program.
Has program options such as
-ncpu
-threads
In these cases all resources must be on a single node and you should specify

-R span[hosts=1]

to ensure all resources are placed on a single node.

This is crucial when GPUs are involved. GPU devices are valuable and there are not a large number of them. LSF will allocate the number of GPUs you request with the  -gpu num= option on each node that is allocated to your job whether you are using that node or not.

If your program is single thread or an SMP program and your job specification spans several nodes, only the first node will be used and the GPUs on all other nodes will be allocated but unused and probably preventing another job from being dispatched.

R and Rstudio
 

Contents
R
Rstudio
Rstudio Connect Server
R
R is a widely used open-source software for statistical computing and graphics supported by the R Core Team and the R Foundation for Statistical Computing. Many versions of R are available to use on Minerva. To see a list of installed versions simply type:

$ module -r spider “^R$”
where “^R$” is a regular expression meaning that the module name starts with R and ends with R as well.

Note that new versions of R are periodically added and the default version of R changes accordingly. You may want to explicitly specify the version of R in your module load command (i.e. module load R/3.5.3) to avoid picking up a new version of R when you don’t want it. We would recommend users to use the most recent version installed. Our current R installation comes with a large number of popular scientific and high-performance computing packages preinstalled, including Bioconductor packages.

Usage
R may be run either interactively via the R terminal, or as a batch process reading commands from a script file. To launch the R terminal, open an interactive session by submitting a job to the interactive LSF queue. Then simply execute the R command. After the terminal is launched in the interactive mode, users can run R commands at the prompt:

[choh07@li03c04 ~]$ bsub -q interactive -P acc_hpcstaff -n 1 -W 1:00 -R rusage[mem=8000] -XF -Is /bin/bash
Job <65508400> is submitted to queue .
<>
<>
<>

[choh07@lc02a29 ~]$ ml R/4.2.0
[choh07@lc02a29 ~]$ R

R version 4.2.0 (2022-04-22) -- "Vigorous Calisthenics"
Copyright (C) 2022 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

  Natural language support but running in an English locale

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

> print("Hello World!", quote = FALSE)
[1] Hello World!
>
Rscript, on the other hand, can be used to run a R script in which a sequence of R commands has been saved. The syntax is:

$ Rscript <options> <your R script>
For example,

$ cat hello.R
print("Hello World!", quote = FALSE)

$ Rscript hello.R
[1] Hello World!
Installing New Packages Locally
If you find that a particular package you need is missing from the R version you use, you may install it yourself into your local space. In some cases you will need to build/maintain your own set of R packages in your space locally due to various reasons.

To list currently installed packages, typing library() from the R command prompt, as shown.

$ R -e 'library()'
There are multiple ways to install R packages. Here we present one of them below as an example. To begin, you need to create a directory in your local space to install R packages into. We will use “~/.Rlib” in this example, which is the default location and its path is predefined in the environment variable “R_LIBS_USER” but you may choose as you wish.

$ mkdir ~/.Rlib
When using a different R version, it is recommended to reinstall your existing packages to ensure compatibility and optimal performance with the updated R environment. This process helps prevent potential conflicts and ensures that all packages are compiled specifically for the new R version’s architecture and dependencies.

To make R packages installed into this local directory, create the “.Renviron” file in your home directory to redefine the environment variable “R_LIBS” for the R packages search path accordingly:

$ cat ~/.Renviron
R_LIBS=${R_LIBS_USER}:${R_LIBS}
Note that the path to your local R packages directory is prepended to the system one in order to point R to the directory where your local packages are installed first. Load R and start up an R session from the terminal. You should be able to use the standard R command to install the package interactively now.

$ R

R version 4.2.0 (2022-04-22) -- "Vigorous Calisthenics"
Copyright (C) 2022 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)
.
.
.

> .libPaths()
[1] "/hpc/users/choh07/.Rlib"
[2] "/hpc/packages/minerva-centos7/rpackages/4.2.0/site-library"
[3] "/hpc/packages/minerva-centos7/rpackages/bioconductor/3.15"
[4] "/hpc/packages/minerva-centos7/R/4.2.0/lib64/R/library"

> install.packages("zoo")
Installing package into ‘/hpc/users/choh07/.Rlib’
(as ‘lib’ is unspecified)
.
.
.
** R
** demo
** inst
** byte-compile and prepare package for lazy loading
** help
*** installing help indices
** building package indices
** installing vignettes
** testing if installed package can be loaded from temporary location
** checking absolute paths in shared objects and dynamic libraries
** testing if installed package can be loaded from final location
** testing if installed package keeps a record of temporary installation path
* DONE (zoo)
>
 

Rstudio
RStudio is an integrated development environment (IDE) for R. It includes a console, syntax-highlighting editor that supports direct code execution, as well as tools for plotting, history, debugging and workspace management. It is available in two formats: RStudio Desktop is a regular desktop application while RStudio Server runs on a remote server and allows accessing RStudio using a web browser. RStudio Desktop and RStudio Server are both available in free and fee-based (commercial) editions.

On Minerva, there are two ways that you can access the rstudio interface, running Rstudio application over GUI (graphical user interface) or one simple command to get On-the-fly Rstudio over Web in a Minerva job.

 

Using Rstudio application over GUI
We have the rstudio desktop compiled as a module on Minerva. To use it:

Enable X11 forwarding when you login to Minerva (see login page for details)
ml rstudio/2022.02.1-461
rstudio
This is the Rstudio Desktop application, and it may be slow to access over GUI depending on your network.

 

Using Rstudio application over GUI
We have developed simple tool scripts for users to launch RStudio server with interactive web interface inside the LSF job with dedicated resources. With one simple command,you can run the free Rstudio Server remotely on Minerva and access the RStudio interface using your local web browser.

The scripts are located in the login nodes under

/usr/local/bin/{minerva-rstudio-web.sh, minerva-rstudio-web-r4.sh}
With one command on the terminal, you will launch RStudio server and access interactive web interface inside the LSF job with dedicated resources on Minerva.

The

 minerva-rstudio-web.sh
will start RStudio web with R3, and

 minerva-rstudio-web-r4.sh
will start RStudio with R4 kernel.

Usage:
For example, to start an RStudio web session with R, on the login nodes, run commands minerva-rstudio-web-r4.sh (using R 4.0.3) or minerva-rstudio-web.sh (R 3.6.2) with default resource configuration and URL to access it. Please see the -h option for help messages containing resource requests and installing packages.

Example:

$ sh minerva-rstudio-web-r4.sh 
[INFO] Image not specified, check if previously used
[INFO] Found previously used image 
/hpc/users/guow03/minerva_jobs/rstudio_jobs/singularity-rstudio.simg. 
Using it.
[INFO] Project is not specified, or is acc_null, 
using 1st avail project.
[INFO] Project to use is acc_hpcstaff
[INFO] Parameters used are: 
[INFO] -n    4
[INFO] -M    3000
[INFO] -W    6:00
[INFO] -P    acc_hpcstaff
[INFO] -J    rstudio
[INFO] -q    premium
[INFO] -R    null
[INFO] -i    /hpc/users/guow03/minerva_jobs/rstudio_jobs/
singularity-rstudio.simg
[INFO] Submitting rstudio job...
Job <25962439> is submitted to queue .
[INFO] See below for web access when job starts.
Job <25962439> : Not yet started.
[INFO] Job is pending
Job <25962439> : Not yet started.
[INFO] Job is pending
Job <25962439> : Not yet started.
[INFO] Job is pending
Job <25962439> : Not yet started.
[INFO] Job is pending
[INFO] Job is running, wait for link
[INFO] Job is running, wait for link
[INFO] Job is running, wait for link
<< output from stdout >>
Using local available port 8787
Using password in /hpc/users/guow03/minerva_jobs/rstudio_jobs/
.rstudio_onthefly_password
RStudio started in the singularity container with PID 306586.
Making sure it is alive
Checking 3, next check in 5 seconds.
   PID TTY          TIME CMD
306586 ?        00:00:00 singularity
Checking 2, next check in 5 seconds.
   PID TTY          TIME CMD
306586 ?        00:00:00 starter-suid
Checking 1, next check in 5 seconds.
   PID TTY          TIME CMD
306586 ?        00:00:00 starter-suid

SSH port forwarding to 10.95.46.103 with PID 306725.

Rstudio is started on compute node lc03e29, port 8787

Access the RStudio Web using your web browser:  
http://10.95.46.103:52439 

<< output from stderr >>
The RStudio web interface will be available at URL http://10.95.46.103:52439. Use the browser on your laptop to access it. Here 10.95.46.103 is the login node IP on the campus network and 52439 is the port. 2439 is the last 4 digit of the job 25962439 running the instance.

What happens behind the scene? This tool wraps the following tasks in one command.

downloads a custom built Singularity container image of RStudio in your home directory
prompts and creates a password for the RStudio interface,
writes and submits an LSF job script to launch the RStudio within the image,
provides the URL link to access the instance
Currently, the minerva-rstudio-web-r4.sh script is the preferred version as it is built with common libraries including"DBI", "odbc", "shiny", "devtools", "ggplot2", "tidyverse", "tidymodels", "car", "dplyr", "tidyr". If you want to switch the scripts to use, move or rename the downloaded image singularity-rstudio.simg in $HOME/minerva_jobs/rstudio_jobs.
See the Package Installations section in the help message for packages not installed.

Common problems:

Plotting in rstudio errors due to X11 display not working when using the v4 version. This problem has been fixed with an updated image. Please remove your old image and run the script.
This is a containerized application for your workflow reproducibility, and R packages are installed in$HOME/x86_64-pc-linux-gnu-library/R_VERSIONby default. Since this is a container environment, you need to install and maintain your own R related package. No module system setup. To install the R packages needed, please read carefully on the help message (minerva-rstudio-web-r4.sh -h). You will need to do the installation in the Rstudio web “Shell terminal tab” not the R Console tab shown as below.
[INFO] === Package Installations ===
[INFO] To install R packages, do the following in the RStudio web *Shell terminal console*
[INFO] $ export http_proxy=http://172.28.7.1:3128
[INFO] $ export https_proxy=http://172.28.7.1:3128
[INFO] $ export all_proxy=http://172.28.7.1:3128
[INFO] $ export no_proxy=localhost,*.hpc.mssm.edu,*.chimera.hpc.mssm.edu,172.28.0.0/16
[INFO] $ R
[INFO] >>> install.packages(name_of_package)
[INFO] The packages will be installed in your /hpc/users/gail01/R/x86_64-pc-linux-gnu-library/R_VERSION
[INFO] If the package is not available in your RStudio Web interface by R library('name_of_package')
[INFO] You can restart the RStudio job
 

Installing R Modules That Require External Libraries From Minerva Packages for on-the-fly RStudio Web
Occasionally, you may need to install an R module that requires one or more dynamic libraries from Minerva packages. In these cases, you should install the R module into the private library that is used by rstudio outside of rstudio using hard-coded paths to those libraries. The library that is automatically set up is $HOME/R/x86_64-pc-linux-gnu-library/[3.0 or 4.0].

A convenient way to signal the linker that it should encode the full path to dynamic libraries is to set LD_RUN_PATH to LD_LIBRARY_PATH after you load all the Minerva packages needed to build the R module but before you actually build the module. When you build the module, you will need to specify the destination library as the default private library for rstudio.
Example:

ml R/4.0.2
ml geos
export LD_RUN_PATH=$LD_LIBRARY_PATH
R
> install.packages(“Seurat”, lib=”/hpc/users/fludee01/R/x86_64-pc-linux-gnu-library/4.0”)

Now that the Seurat R module has been built and installed in rstudio’s user library, you can call it from within on-the-fly rstudio.

 

RStudio Connect Server
What is Rstudio Connect?
Rstudio Connect server is available on Minerva since Aug 2020, where you can publish Shiny, R Markdown and Jupyter for collaborators or others. See more details about Rstudio Connect here.

 

How many users allowed to log into Rstudio Connect?
Currently, the subscription is upgraded to allow 45 named users on the server.

 

How to publish?
Rstudio-connect server is at https://rstudio-connect.hpc.mssm.edu

To publish your R product with Rstudio-connect:

1. Login to Minerva by ssh -X userid@minerva.hpc.mssm.edu ( X11 forwarding needed for GUI)

2. After login,

$ ml rstudio

$ rstudio

Connect Rstudio IDE to Rstudio connect, (see Rstudio Connect here)

The address of the RStudio Connect server is https://rstudio-connect.hpc.mssm.edu. This will request login. Use your Sinai ID and password followed by the 6-digit VIP token.

3.To publish, open your app file such as app.R in Rstudio IDE, and click the right corner publish button (see Rstudio Publishing here)

NOTE: please uncheck “Launch browser” when publishing, since launching the browser over GUI is slow.



4. After your application is successfully deployed as shown in the deploy log, you can copy the URL and open it in any browser you like



5. You can manage access and other metrics in the dashboard. Tip: you can modify your Content URL as shown in the screenshot (bottom right corner), and copy the URL for sharing. For more on how to manage your application in the dashboard, please follow Rstudio connect user guide located here.


Python and Jupyter Notebook
 

Contents
Python
Jupyter Notebook
 

Python
Python is an interpreted programming language that has become increasingly popular in high-performance computing environments because it’s available with an assortment of numerical and scientific computing libraries (numpy, scipy, pandas, etc.), relatively easy to learn, open source, and free.

Many versions of Python are available to use on Minerva. To see a list of installed versions of Python on the cluster, use Lmod’s spider command:

$ ml spider python 
 
 
---------------------------------------------------------------------------- 
  python: 
---------------------------------------------------------------------------- 
 	Versions: 
    	python/2.7.9-UCS4 
    	python/2.7.16 
    	python/2.7.17-UCS4 
    	python/2.7.17 
    	python/3.4.0 
    	python/3.5.0 
    	python/3.6.2 
    	python/3.7.3 
    	python/3.8.2 
    	python/3.10.4 
 	Other possible modules matches: 
    	bx_python  lsf_python_api  wxPython 
 
 
---------------------------------------------------------------------------- 
  To find other possible module matches execute: 
 
 
  	$ module -r spider '.*python.*' 
 
 
---------------------------------------------------------------------------- 
  For detailed information about a specific "python" module (including how to load the modules) use the module's full name. 
  For example: 
 
 
 	$ module spider python/3.6.2 
---------------------------------------------------------------------------- 
 

Note that new versions of Python are periodically added and the default version of Python changes accordingly. You may want to explicitly specify the version of Python in your module load command (i.e., ml python/3.8.2) to avoid picking up a new version of Python when you don’t want it. We would recommend users to use the most recent version installed. Our current Python installation comes with many popular scientific and high-performance computing packages preinstalled.

 

Usage
Python may be run either interactively, or as a batch process reading commands from a script file. To run Python interactively, open an interactive session by submitting a job to the interactive LSF queue. Then simply execute the Python command. After the terminal is launched in the interactive mode, users can run Python commands at the prompt:

$ bsub -q interactive -P acc_hpcstaff -n 1 -W 1:00 -R rusage[mem=8000] -XF -Is /bin/bash 
Job <66690152> is submitted to queue . 
<> 
<> 
<> 
$ ml python 
$ python  
Python 3.7.3 (default, Oct 13 2020, 20:41:27)  
[GCC 8.3.0] on linux 
Type "help", "copyright", "credits" or "license" for more information. 
>>>  
>>> print("Hello World!") 
Hello World! 
>>>
The syntax of running a Python script consists of a sequence of Python commands:

$ python
 
For example,

$ cat hello.py 
print("Hello World!") 
$ python hello.py 
Hello World!
 

Installing New Packages Locally
If you find that a particular package you need is missing from the Python version you are using, you may open a ticket at hpchelp@hpc.mssm.edu to have it installed in the system area or install it yourself in your local space. In some cases, you will need to build/maintain your own set of Python packages in your space locally due to various reasons. There are multiple ways to install Python packages. Here we present two of them in detail below as examples:

Using pip
pip is the package installer for Python. You can use it to install packages from the Python Package Index
and other indexes.
Python will need to be loaded in module before using pip:
$ ml python
The syntax of installing a single Python package is:

$ pip install --user package_name==version
For example,

$ pip install --user numpy==1.21.6
Packages will be installed in:

~/.local/lib/python_version/site-packages/
For example, for Python 3.7.3, the path is:

~/.local/lib/python3.7/site-packages/
Then, prepend the package path and bin path to PYTHONPATH and PATH environment variables:

$ export PYTHONPATH=~/.local/lib/python_version/site-packages/:$PYTHONPATH 
$ export PATH=~/.local/bin/:$PATH
You should be able to use the new package now.

$ python 
>>> import numpy 
>>> numpy.__version__ 
'1.21.6'
You can also install packages to a specific location by adding the –prefix:

$ pip install --prefix=/path/to/folder package_name==version
You will also need to prepend the paths as shown above:

$ export PATH=/path/to/folder/bin/:$PATH 
$ export PYTHONPATH=/path/to/folder/lib/python_version/site-packages/:$PYTHONPATH
 

Using venv
venv allows you to manage separate package installations for different projects. They essentially allow you to create a “virtual” isolated Python installation and install packages into that virtual installation. When you switch projects, you can simply create a new virtual environment and not have to worry about breaking the packages installed in the other environments. It is always recommended to use a virtual environment while developing Python applications.
Python will need to be loaded in module before using venv:
$ ml python
Creation of virtual environments is done by executing the command venv:

$ python -m venv /path/to/new/virtual/env
venv will create a virtual Python installation in the “/path/to/new/virtual/env” folder.
Before you can start installing or using packages in your virtual environment, you’ll need to activate it:

$ unset PYTHONPATH 
$ source /path/to/new/virtual/env/bin/activate
Activating a virtual environment will put the virtual environment-specific python and pip executables into your shell’s PATH.
You can confirm you’re in the virtual environment by checking the location of your Python interpreter:

$ which python
It should be in the env directory:

/path/to/new/virtual/env/bin/python
The name of the virtual environment will also show in front of your username in the prompt:

(env) [user_name@li03c03 ~]$
As long as your virtual environment is activated, you can use pip for package installation and pip will install packages into that specific environment by default. You’ll also be able to import and use packages in your Python application.
If you want to switch projects or otherwise leave your virtual environment, simply run:

$ deactivate
If you want to re-enter the virtual environment just follow the same instructions above about activating a virtual environment. There’s no need to re-create the virtual environment.

 

Using Anaconda
You can also create a virtual environment using Anaconda. Anaconda is available on Minerva:
$ ml spider anaconda3 
 
 
---------------------------------------------------------------------------- 
  anaconda3: 
---------------------------------------------------------------------------- 
     Versions: 
        anaconda3/latest 
        anaconda3/4.5.11 
        anaconda3/4.6.4 
        anaconda3/2018.12 
        anaconda3/2019.10 
        anaconda3/2020.11 
        anaconda3/2021.5 
 
 
---------------------------------------------------------------------------- 
  For detailed information about a specific "anaconda3" module (including how to load the modules) use the module's full name. 
  For example: 
 
 
     $ module spider anaconda3/4.6.4 
----------------------------------------------------------------------------
Please click here for more information about using anaconda.

 

Jupyter Notebook
Jupyter notebooks (formerly iPython notebooks) is an interactive computational environment, in which you can code interactively in Python from a web browser with support for equation editing, code execution, rich text, mathematics, inline plotting, rich media etc.

On the Minerva cluster, you can access the Jupyter notebook running on compute nodes via port forwarding (details refer to here). You can run step-by-step commands to start a Jupyter notebook running from Minerva compute nodes and access it at your local web browser. We also provided in-house wrappers/tools to access the Jupyter notebook via one simple command line such as “minerva-jupyter-module-web.sh” or “minerva-jupyter-web.sh”.

With those tools, Jupyter notebook servers run on the Minerva compute nodes as LSF jobs with dedicated resources. You can request the needed resources for your Jupyter interactive work as you do in other LSF batch jobs. It is recommended that the Jupyter notebook is used only for code development and testing on smaller samples. For computationally intensive or long running tasks, the bulk computation should be performed in Python scripts submitted as non-interactive batch jobs, if possible.

Table 1 summary comparison of the two wrappers

 	minerva-jupyter-module-web.sh	minerva-jupyter-web.sh
Access modules on Minerva	Yes	No
Using singularity image	No	Yes
Support GPU node	Yes	Yes
Python versions	By default, python/3.7.3;
You can load other python version and other modules needed for your Jupter Notebook by -mm option	This script uses the python within this Singularity image (shub://ISU-HPC/jupyter)
Others	For users who want to access Minerva modules.	For users who want an isolated/clean env working with a container image. You need to install/maintain your own python related package. No module system setup
 

Option1:minerva-jupyter-module-web.sh
One simple command to get interactive web sessions in a Minerva LSF job (Available on login nodes only). You can check the script at /usr/local/bin/minerva-jupyter-module-web.sh.

Usage:
For example, to start jupyter notebook web session with python/3.7.3, on the login nodes, run commands

minerva-jupyter-module-web.sh
(using python/3.7.3) with default resource configuration and URL to access it. Please see the

--help
option for help messages containing resource requests and installing packages.

$ minerva-jupyter-module-web.sh
[INFO] Project is not specified, or is acc_null, using 1st avail project.
[INFO] Project to use is acc_psychgen
[INFO] Parameters used are: 
[INFO] -n	4
[INFO] -M	3000
[INFO] -W	6:00
[INFO] -P	acc_psychgen
[INFO] -J	jupyter
[INFO] -q	premium
[INFO] -R	null
[INFO] -mm	null
[INFO] -env  null
[INFO] Submitting jupyter job...
Job <61550541> is submitted to queue .
[INFO] Wait and see below for web access when job starts.
<< output from stdout >>
Using local port 8888
Jupyter Notebook is started on compute node lc02g01, port 8888
<< output from stderr >>
Currently Loaded Modules:
  1) gcc/8.3.0   2) unixODBC/2.3.9   3) python/3.7.3
[I 16:22:11.473 NotebookApp] Serving notebooks from local directory: /hpc/users/gail01
[I 16:22:11.473 NotebookApp] Jupyter Notebook 6.1.4 is running at:
[I 16:22:11.473 NotebookApp] http://lc02g01:8888/?token=f9e6d8dea461b8d6837ff11bc1e3ed32f80e1b282c22b305
[I 16:22:11.473 NotebookApp]  or http://127.0.0.1:8888/?token=f9e6d8dea461b8d6837ff11bc1e3ed32f80e1b282c22b305
[I 16:22:11.473 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 16:22:11.477 NotebookApp]    
    To access the notebook, open this file in a browser:
        file:///hpc/users/gail01/.local/share/jupyter/runtime/nbserver-219276-open.html
    Or copy and paste one of these URLs:
        http://lc02g01:8888/?token=f9e6d8dea461b8d6837ff11bc1e3ed32f80e1b282c22b305
     or http://127.0.0.1:8888/?token=f9e6d8dea461b8d6837ff11bc1e3ed32f80e1b282c22b305
[INFO] Token not ready, trying ...


[INFO] Copy the following link in your browser for the jupyter notebook web access.
[INFO] http://10.95.46.103:40541/?token=f9e6d8dea461b8d6837ff11bc1e3ed32f80e1b282c22b305



For Jupyter sessions, after see links blinking with the url with token (no password), like the above
 “
[INFO] Copy the following link in your browser for the jupyter notebook web access.
[INFO] http://10.95.46.103:40541/?token=f9e6d8dea461b8d6837ff11bc1e3ed32f80e1b282c22b305
”

Copy the url and paste it in your browser to access the Jupyter Notebook web session. Note: You can load Minerva modules needed for your Jupyter Notebook with –mm option.

Please see the

--help
option for help messages containing resource requests and installing packages.

$ minerva-jupyter-module-web.sh --help
[INFO] This script is to submit a Python Jupyter Notebook web instance inside an
[INFO] LSF job on *one single host* for users.
[INFO] By default, this script uses Jupyter from python/3.7.3
[INFO] You can load other python version and other modules needed for your Jupter Notebook by -mm option
[INFO] 
[INFO] 09/01/2021
[INFO] Contact: hpchelp@hpc.mssm.edu
[INFO] 
[INFO] minerva-jupyter-module-web.sh  -n  -M  -W 
[INFO]                         -p  -J  -q  
[INFO]                         --mm 
[INFO] 
[INFO] -n | --ncpus		Number of CPU slots, will be allocated in one host using -R 'span[nhost=1]' default 4 if not specified.
[INFO] -M | --mem		Memory in Megabytes per CPU slot, used for resource request, default 3000 MB. -R 'rusage[mem=3000]'
[INFO] -W | --timelimit		Wall time for the job, format HH:MM. Default is 6:00/6 hours
[INFO] -P | --project		Minerva account for this job, default the first acc_ from your mybalance if not specified
[INFO] -J | --jobname		Specify the job name, default jupyter
[INFO] -q | --queue		Specify the queue name, default premium
[INFO] -R | --resource	Optional resource, eg himem, a100, v100
[INFO] -mm |--module		module your want to loaded, seperated by , for multiple modules 
[INFO] -env | --myenv	anaconda env you want to activate if anaconda is loaded 
[INFO] -h | --help		Help message
[INFO] 
[INFO] Files and directories:
[INFO] /hpc/users/gail01/minerva_jobs/jupyter_jobs			The directory where this script generates the job submission scripts. 
[INFO] 
[INFO] Job output and error files will be saved in the current working directory when you run this script. 
[INFO] If job is still running, use bpeek  to check the output
 

Behind the scene, the wapper tool just runs a LSF command and the port forwarding as shown below. You can issue those commands step by step with more control by yourself and work with the port forwarding. Here are the details:

# start an interactive session for example

$bsub -P acc_xxx -q interactive -n 2 -R "span[hosts=1]" -R 
rusage[mem=4000] -W 3:00 -Is /bin/bash
#Then on the allocated nodes lc01c30, start Jupyter Notebook

lc01c30 $ml python
lc01c30$jupyter notebook --no-browser --port=8889
#On your local workstation, forward port XXXX(8889) to YYYY(8888) and listen to it

$ssh -t -t -L localhost:8888:localhost:8889 
gail01@minerva.hpc.mssm.edu ssh -X lc01c30 -L 
localhost:8889:localhost:8889
#Open firefox on local: http://localhost:8888
Note: you can change the portal number 8888/8889 to others

Option 2: minerva-jupyter-web.sh
minerva-jupyter-web.sh is on-the-fly Jupyter Notebook in a Minerva LSF job using the python jupyter notebook within a singularity image. It is a containerized application for workflow reproducibility (for users who want an isolated/clean env working with container image), and related packages are set to be installed in $HOME/.local.

By default, it uses the python within this Singularity image (shub://ISU-HPC/jupyter). You can use your own image with the option -i, but this may need a bit modification of the path for python in line 335 and following at /usr/local/bin/minerva-jupyter-web.sh. You can consult hpchelp@hpc.mssm.edu if you have issues with this.
There is no module system setup, so you cannot access any central modules maintained on Minerva. You will need to install/maintain your own python related package as below:
Open the terminal in web the jupyter web, type pip install packages –user
This will be in your home directory $HOME/.local. Please restart the jupyter notebook to pick up the changes
Usage
For example, to start a Jupyter notebook web session with a container image, on the login nodes, run commands minerva-jupyter-web.sh with default resource configuration and URL to access it. Please see the --help option for help messages containing resource requests and installing packages.

 $ minerva-jupyter-web.sh 
[INFO] Image not specified, check if previously used
[INFO] No previously used image in /hpc/users/gail01/minerva_jobs/jupyter_jobs, pulling the default image shub://nickjer/singularity-jupyter
[INFO] Pulling image to /hpc/users/gail01/minerva_jobs/jupyter_jobs/jupyter_latest.sif
INFO:    Downloading shub image
328.0MiB / 328.0MiB [==============================================================================] 100 % 201.5 MiB/s 0s
[INFO] Project is not specified, or is acc_null, using 1st avail project.
[INFO] Project to use is acc_psychgen
[INFO] Parameters used are: 
[INFO] -n	4
[INFO] -M	3000
[INFO] -W	6:00
[INFO] -P	acc_psychgen
[INFO] -J	jupyter
[INFO] -q	premium
[INFO] -R	null
[INFO] -g	0
[INFO] -i	/hpc/users/gail01/minerva_jobs/jupyter_jobs/jupyter_latest.sif
[INFO] Submitting jupyter job...
Job <61550613> is submitted to queue .
[INFO] Wait and see below for web access when job starts.
<< output from stdout >>
Using local port 8888
Jupyter Notebook is started on compute node lc01a11, port 8888
 
 
<< output from stderr >>
[I 16:44:57.521 NotebookApp] JupyterLab beta preview extension loaded from /usr/local/lib/python3.6/site-packages/jupyterlab
[I 16:44:57.521 NotebookApp] JupyterLab application directory is /usr/local/share/jupyter/lab
[I 16:44:57.526 NotebookApp] Serving notebooks from local directory: /hpc/users/gail01
[I 16:44:57.526 NotebookApp] 0 active kernels
[I 16:44:57.527 NotebookApp] The Jupyter Notebook is running at:
[I 16:44:57.527 NotebookApp] http://lc01a11:8888/?token=d863e7b88b5ab7344846e182d29bf0ba97a5edc7b9b93de1
[I 16:44:57.527 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 16:44:57.527 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://lc01a11:8888/?token=d863e7b88b5ab7344846e182d29bf0ba97a5edc7b9b93de1&token=d863e7b88b5ab7344846e182d29bf0ba97a5edc7b9b93de1
[INFO] Token not ready, trying ...
 
 
[INFO] Copy the following link in your browser for the jupyter notebook web access.
[INFO] http://10.95.46.103:40613/?token=d863e7b88b5ab7344846e182d29bf0ba97a5edc7b9b93de1
Please see the –help option for help messages containing resource requests and installing packages.

 $minerva-jupyter-web.sh --help
[INFO] This script is to submit a Singularity containerized Jupyter Notebook web instance inside an
[INFO] LSF job on *one single host* for users.
[INFO] By default, this script uses this Singularity image (shub://ISU-HPC/jupyter)
[INFO] see https://singularity-hub.org/collections/1069/usage for the this image info.
[INFO] 
[INFO] Wei Guo
[INFO] 12/14/2020
[INFO] Contact: hpchelp@hpc.mssm.edu
[INFO] 
[INFO] minerva-jupyter-web.sh  -n  -M  -W 
[INFO]                         -p  -J  -q  
[INFO]                         --image 
[INFO] 
[INFO] -n | --ncpus		Number of CPU slots, will be allocated in one host using -R 'span[nhost=1]' default 4 if not specified.
[INFO] -M | --mem		Memory in Megabytes per CPU slot, used for resource request, default 3000 MB. -R 'rusage[mem=3000]'
[INFO] -W | --timelimit		Wall time for the job, format HH:MM. Default is 6:00/6 hours
[INFO] -P | --project		Minerva account for this job, default the first acc_ from your mybalance if not specified
[INFO] -J | --jobname		Specify the job name, default jupyter
[INFO] -q | --queue		Specify the queue name, default premium
[INFO] -R | --resource	Optional resource, eg himem, a100, v100
[INFO] -g | --ngpus		Optional. Specify the number of GPUs to use on a single node, default is 0
[INFO] -i | --image		Full path of the image file your specified other than the default. If not specified, this script will pull the default image in your /hpc/users/gail01/minerva_jobs/jupyter_jobs directory. 
[INFO] -h | --help		Help message
[INFO] 
[INFO] Files and directories:
[INFO] /hpc/users/gail01/minerva_jobs/jupyter_jobs			The directory where this script generates the job submission scripts. 
[INFO] /hpc/users/gail01/minerva_jobs/jupyter_jobs/jupyter_latest.sif			The default file of the image.
[INFO] 
[INFO] Job output and error files will be saved in the current working directory when you run this script. 
[INFO] If job is still running, use bpeek  to check the output
 

There is also a tool called minerva-jupyter-r-web.sh, which supports both Jupyter notebook Python 3 and R3 kernels. Run the –help option for help messages containing resource requests and installing packages.

What happens behind the scene? This tool wraps the following tasks in one command.

downloads a custom built Singularity container image of Jupyter Notebook in your home directory
writes and submits an LSF job script to launch the Jupyter Notebook within the image,
provides the URL link to access the instance
 

Submit Jupyter notebook as a batch job
The Jupyter command, which is available from the python installation (ml python), comes with a very versatile command jupyter-nbconvert. With this command you can convert your notebook to python, html, pdf and execute our notebook in batch or on the command line. For all the options:

jupyter-nbconvert –help.

 


To run a notebook from the command line:

jupyter-nbconvert --to notebook --ExecutePreprocessor.timeout=-1 --execute myfile.ipynb
To run this in batch, just wrap it in a shell script and submit it using LSF. If you want the results to be part of the notebook, use the –inplace option.
You may also just want to convert the notebook to straight python:

jupyter nbconvert myfile.ipynb --to python

Conda
Contents
Usage
Using Conda with Python
Using Conda with R
Conda is an open-source package management system and environment management system that runs on Windows, macOS, Linux and z/OS. Conda quickly installs, runs and updates packages and their dependencies. Conda easily creates, saves, loads and switches between environments on your local computer. It was created for Python programs, but it can package and distribute software for any language.

The conda package and environment manager is included in all versions of Anaconda and Miniconda. Many versions of Anaconda are available to use on Minerva. To see a list of installed versions of Anaconda on the cluster, use Lmod’s spider command:

$ ml spider anaconda
---------------------------------------------------------------------------- 
  anaconda2: 
---------------------------------------------------------------------------- 
     Versions: 
        anaconda2/latest 
        anaconda2/2019.03 
        anaconda2/2019.10dev 
        anaconda2/2019.10 
 
 
---------------------------------------------------------------------------- 
  For detailed information about a specific "anaconda2" module (including how to load the modules) use the module's full name. 
  For example: 
 
 
     $ module spider anaconda2/latest 
---------------------------------------------------------------------------- 
 
 
---------------------------------------------------------------------------- 
  anaconda3: 
---------------------------------------------------------------------------- 
     Versions: 
        anaconda3/latest 
        anaconda3/4.5.11 
        anaconda3/4.6.4 
        anaconda3/2018.12 
        anaconda3/2019.10 
        anaconda3/2020.11 
        anaconda3/2024.06 
 
 
---------------------------------------------------------------------------- 
  For detailed information about a specific "anaconda3" module (including how to load the modules) use the module's full name. 
  For example: 
 
 
     $ module spider anaconda3/4.6.4 
----------------------------------------------------------------------------
 

 

Usage
When creating an environment, conda will download large amounts of installation tarballs to the package cache directory. The default is “~/.conda/pkgs” in /home directory, however on Minerva, user’s home directory has 20 GB quota. Therefore, instead of using this default setting, it is recommended to redirect the downloaded package cache directory to a different location, which can be specified in the “~/.condarc” file. Below steps listed steps for redirecting the package cache location:

Edit conda user config file: “~/.condarc” to redirect the package cache and env directory. For example, after opening “~/.condarc”, add the following lines:

envs_dirs: 
- /sc/arion/work/your_username/test-env/envs 
pkgs_dirs: 
- /sc/arion/work/your_username/test-env/pkgs
If you do not have a “~/.condarc” file, use the command below to create one:

$ touch ~/.condarc
Before trying to create a conda virtual environment, do a module purge to clean up the current environment:

$ module purge
Then load an Anaconda module. For example:

$ module load anaconda3/2024.06
Check conda information. If conda is available, you will see:

$ conda info 
… 
       conda version : 24.11.3 
 conda-build version : 24.5.1 
      python version : 3.12.2.final.0
              solver : libmamba (default) 
    virtual packages : __archspec=1=sapphirerapids 
... 
       package cache : /sc/arion/scratch/your_username/pkgs 
    envs directories : /sc/arion/scratch/your_username/envs 
                       /hpc/users/your_username/.conda/envs 
                       /hpc/packages/Minerva-rocky9/anaconda3/2024.06/envs 
            platform : linux-64 
...
 

Also make sure the envs and package cache directories have been redirected to the correct directory.
Next is to create a conda virtual environment. For example, creating a conda environment called “test” by:

$ conda create -n test
The conda environment should then be activated before installing packages:

$ source activate test
The name of the virtual environment will also show in front of your username in the prompt:

(test) [user_name@li04e03 ~]$
Then you can install packages using the command:

$ conda install -c channel_name package1=version package2=version
Channel name and package version are optional.
If you want to leave your virtual environment, simply run:

$ conda deactivate
Other useful conda commands:
Checking current and available conda environment:

$ conda info –-envs
Check installed packages:

$ conda list
Upgrade and uninstall packages inside a conda environment:

$ conda upgrade/update package1 package2  
$ conda uninstall/remove package1 package2
Remove a conda environment:

$ conda env remove -n env_name
 

Using Conda with Python
Make sure to unset PYTHONPATH and PYTHONHOME in case they are specified previously:

$ unset PYTHONPATH 
$ unset PYTHONHOME
The version of Python in the conda environment can be specified when creating the conda environment, for example, specify python 3.6 to be used in the environment “test”:

$ conda create –n test python=3.6
Or install python after creating the conda environment:

$ conda install python=3.6 
 

Using Conda with R
Make sure to unset R_LIBS and R_LIBS_USER in case they are specified previously:

$ unset R_LIBS 
$ unset R_LIBS_USER 
Also, you might need to comment lines related to these variables in .Renviron and .Rprofile file in your home directory.
The version of R in the conda environment can be specified when creating the conda environment, for example, specify R 4.2.0 to be used in the environment “test”:

$ conda create –n test r-base=4.2.0 
Or install R after creating the conda environment:

$ conda install r-base=4.2.0 
When using conda to install R packages, you will need to add r- before the regular package name. For instance, if you want to install rbokeh, you will need to use:

$ conda install r-rbokeh  
or for rJava, type:

$ conda install r-rjava 
For more information about using conda, please click here.

Software Build and Compile on Minerva
Building Third-Party Applications
You should be able to find many applications already installed on Minerva as system modules using module spider or module avail on the command line. These system modules are there just for the sake of convenience for the users and in no event imply an absolute requirement of using them on Minerva. You may use your own software installed in your local area and can even set up your own modules if you prefer.

In most cases you’ll want to download the source code and build the software so that it’s compatible with the Minerva software environment. You cannot use yum or any other installation process that requires root privileges, but this is almost never necessary. The key is to specify a target installation directory for which you have write permissions. Details vary; you should consult the package’s documentation and be prepared to experiment. When using the popular autotools build process, the standard approach is to use the --prefix option to specify a non-default, user-owned installation directory at the time you execute configure or make:

$ export INSTALLDIR=/sc/arion/work/MyUserID/my_apps
$ ./configure --prefix=$INSTALLDIR
$ make
$ make install
Other languages, frameworks, and build systems generally have equivalent mechanisms for installing software in user space. In most cases a web search like “Software_Name Linux install local” will get you the information you need.
 

Compiling
The programming environment is centered on the compiler. Minerva supports C/C++ and Fortran compilers from INTEL, and GNU Compiler Collection (GCC). Only one compiler module should be loaded at a time to avoid ambiguity. Compiling code for parallel execution on Minerva must use the proper implementation of MPI and link against the proper libraries. The MPI implementation currently supported by Minerva is OpenMPI and INTEL MPI. In particular, note that OpenMPI is not part of the MPICH family of MPI implementations.

For each supported compiler suite, a version of OpenMPI that is compatible with that compiler is loaded by using the module command. For example, to use the GCC compilers with OpenMPI:

module load openmpi
To use the INTEL MPI:

module load intel
When compiling non-MPI programs, modules for compilers without MPI support can be loaded instead:

module load gcc
 

Compiler Names
Compiler “wrappers” provided by OpenMPI and INTEL MPI supply the correct compiler and linker flags for MPI applications. For non-MPI codes the “native” compilers may be used directly.

Language	MPI	Native GCC	Native Intel
Fortran	mpiff77, mpif90	gfortran	ifort
C/C++	mpicc	gcc	icc
 

Basic Examples
Serial Code

gfortran -O2 -o test test.f
MPI

mpicc -O2 -o MPItest MPItest.c
OpenMP

icc -O2 -qopenmp -o OpenMPtest OpenMPtest.c
gcc -O2 -fopenmp -o OpenMPtest OpenMPtest.c
Available compiler options depend on the underlying native compiler; complete descriptions are available via the “man” command.

Singularity
On Minerva HPC cluster, singularity tool is supported for the container platform, instead of docker due to security concerns. However, Docker container images can easily be run as Singularity container images, which are safe to run on the cluster.

Singularity containers allows you to create and run containers that package up pieces of software in a way that is portable and reproducible. Your container is a single file and can be run on different systems. I can use singularity on Minerva when

The software I want to use is so complicated that I can’t get it to work on my computer anyhow
The software can’t be installed on Minerva because of new kernel or system level library requirements
I want to rerun my analysis sometime ago; I want to reproduce my collaborator’s pipelines or results
 

Singularity training slides on Minerva HPC
See a detailed demo at Minerva Singularity slides.

 

Some Helpful External Web Sites
Singularity User guide
Singularity Hub
Docker Hub
Remote Builder
 

How to use Singularity on Minerva HPC
Load the singularity module:

$ module load singularity/3.6.4
You can use “singularity pull” to download a container image from a give URI, such as pull the image from Sylabs Cloud library://, Singularity Hub shub:// and Docker Hub docker://

$ singularity pull --name hello-world_latest.sif shub://vsoch/hello-world
To pull a docker image:

$singularity pull ubuntu_latest.sif docker://ubuntu:latest
To create a container within a writable directory (called a sandbox):

$singularity build --sandbox lolcow/ shub://GodloveD/lolcow
 

Running a Singularity Container
When running within a Singularity container, a user has the same permissions and privileges that he or she would have outside the container.

Once you have the images, you can shell into it (The shell subcommand is useful for interactive work)

# From the cluster
singularity shell ubuntu_latest.sif
To Run a container with the default runscript command:

$ singularity run hello-world_latest.sif
Or run a custom command with exec, or pipes for batch processing (e.g. within a LSF job), For example:

singularity exec ubuntu_latest.sif /path/to/my/script.sh
Where script.sh script contains the processing steps you want to run within the LSF batch job. You can also pass a generic Linux command to the exec subcommand. Pipes and redirected output are supported with the exec subcommand. Below is a quick demonstration showing the change in Linux distribution:

gail01@li03c02: $ cat /etc/*release
CentOS Linux release 7.6.1810 (Core) 
NAME="CentOS Linux"
VERSION="7 (Core)"
……….
CentOS Linux release 7.6.1810 (Core) 
CentOS Linux release 7.6.1810 (Core) 
 
gail01@li03c02: $ singularity shell ubuntu_latest.sif 
 
Singularity> cat /etc/*release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.4 LTS"
NAME="Ubuntu"
VERSION="20.04.4 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.4 LTS"
VERSION_ID="20.04"
…………
Singularity>
 

Building External Directories
Binding a directory to your Singularity container allows you to access files in a host system directory from within your container. By default,  /tmp, user home directory $HOME, and /sc/arion/

is automatically mounted into the singularity image, which should be enough for most of the cases. You can also bind other directories into your Singularity container yourself. To get a shell with a specified dir mounted in the image.

$ singularity run -B /user/specified/dir ubuntu_latest.sif
Warning: Sometimes libraries or packages in$HOME got picked up in container, especially for python packages at ~/.local and R library $_LIBS_USER at ~/.Rlib)

 

Running GPU-Enabled Containers
Singularity supports running on GPUs from within containers. The--nv option must be passed to the exec or shell subcommand. For example:

$ module load singularity/3.6.4 
$ singularity pull docker://tensorflow/tensorflow:latest-gpu 
$ singularity shell --nv -B /run tensorflow_latest-gpu-jupyter.sif
IMPORTANT:
Singularity gives you the ability to install and run applications in your own Linux environment with your own customized software stack. While the HPC staff can provide guidance on how to create and use singularity containers, we do not have the resources to manage containers for individual users. If you decide to use Singularity, it is your responsibility to build and manage your own containers, and software within your own Linux environment.

 

Building Your Own Containers
Although there are a lot of container images contributed by various users on Docker Hub and Singularity Hub, there is time that you want to create/build your own containers. You can build your own container images either use Singularity Remote Builder or your local Linux workstation with which you have the root access. If you don’t have a Linux system you could easily install one in a virtual machine using software like VirtualBox, Vagrant, VMware, or Parallels.

Singularity build is not fully supported on Minerva due to the sudo privileges for users
Using the Remote Builder, you can easily and securely create containers for your applications without special privileges or set up in your local environment from a Linux machine where you have administrative access (i.e. a personal machine)
rite your recipe file/definition file following the guide. You can easily find some examples for recipe files online, for example this GitHub repo.
Convert docker recipe files to singularity recipe files:
$ml python 
$spython recipe Dockerfile Singularity
 

Example Singularity Applications on Minerva
minerva-rstudio-web-r4.sh please check R webpage at https://labs.icahn.mssm.edu/minervalab/documentation/r/




As with the Windows environment, macOS does not come with an X11 server. So, if you want to use X11 graphics you will need to install the XQuartz X11 server ( https://www.xquartz.org/ ) .

File Transfer: Globus and Others
An advanced secure version of Globus File Manager with High Assurance HIPAA compliance BAA subscription is deployed on Minerva.

How to login to Globus with mssm.edu:
To manage or share your Minerva files with collaborators, visit https://app.globus.org/ to login. When prompted for authentication, please use your Mount Sinai school email (eg, first.last@mssm.edu) for access. If you are sure you have the correct username and password (no VIP), please contact hpchelp@mssm.edu for further assistance.





Minerva Collections Available
After login, in the File Manager tab, search Minerva in the collections. Please use the following two collections to access your files under /sc/arion and your files under home /hpc/users. Watch the owner id be89659c-xxx.



How to Share files with Globus?
Please see step by step instruction for file sharing here.

For more information on “Log In and Transfer Files with Globus”, click here.

About your Globus Connect Personal
Users handling HIPAA/sensitive data on machines running Globus Connect Personal, please check High Assurance in the preference. Please ask hpchelp@hpc.mssm.edu to add you to the Globus BAA group subscription.



 

Globus Plus
For some kinds of data transfer or sharing, you may need Globus Plus. If you need to upgrade your Globus account to Globus Plus, please contact us at hpchelp@hpc.mssm.edu to request a Globus Plus invite. See more about “What is Globus Plus? Do I need it?”

The following collection which has no HIPAA subscription setup will retire by June 30 2021.



(WG 030521)

 

Data Transfer
Icahn School of Medicine at Mount Sinai maintains high speed network connections with the commercial Internet and Internet2 through NYSERnet. ISMMS currently maintains the following options for file transfer:

 

Globus Online:
When available, the preferred method for file transfer is Globus Online. The Globus Online software is required, which is free. It allows you to make parallel high speed transfers that can easily will a network connection.

 

SCP, SFTP, rsync:
The standard transfer utilities, SCP, SFTP and rsync can be used to transfer files to and from MSSM systems. These utilities are usually already installed on Linux/Unix machines, and Mac’s. There are also many command and graphical clients available. Due to familiarity and ease, these may be the best choice for transferring scripts and small files, however, these options can be slow in comparison, and may be ill suited for transferring large amounts of data, such as hundreds of TB’s. More information on these utilities can be found on the transfer utilities page.

For windows, the best application for SCP is “PSCP”. It is a command-line tool which replicates the Linux / Unix tools. It is very fast and efficient. You can acquire PSCP here.

A graphical alternative for Windows and Mac is CyberDuck. It is also quick, efficient, and full-featured.

Note: Ensure the Reuse password feature of CyberDuck is disabled. CyberDuck will try to reuse your one-use six-digit VIP token code repeatedly until you get locked out!

 

Physical Transport:
We do support the copying of physical hard drives on the behalf of users. If you need files transferred this way, please provide us with drive to copy to / from and make a ticket with your request. Copies take several days. This service is done as a courtesy and is a best-effort service for bulk data transfer.

 

Unsupported Methods:
Minerva currently does not support FTP or BBCP. Please utilize one of the above methods.

Web Services
Web Services – General
Usage, Security/SSL, Availability and Accessibility
Getting Started with User-based Web Services
Supported Scripting Languages
Authentication
Group-based Web Services
Domain Names
GPG
 

Web Services – General
In General, Minerva web services is a standard Linux-Based web hosting services. Services are provided through Apache 2.4 with the MPM-ITK addon. It is designed to support basic web pages for the intent and purpose of providing web-based science portals and other applications to interact with the jobs, queues, and large science datasets on Minerva. The web services should be on-par with common unix-based web hosting services such as Dreamhost etc. Most common web frameworks and applications should easily fit into this service such as: Django, WordPress, Wikimedia, Custom Perl apps, and static content.

Server-side content is not run as the common “apache” user, but instead as the owner of that application directory. That is, each user’s content/scripts run as that user.

As of October 1st 2022, there are two different Domain Name System for user website’s landing point with different network access:

https://userid.u.hpc.mssm.edu for internal websites
https://userid.dmz.hpc.mssm.edu for public websites
By default, each user’s default web services landing point is https://userid.u.hpc.mssm.edu, with only internal access (campus network or VPN are needed for access).  

 

If you need public websites for your research, please fill out the form or at https://redcap.link/g08ytzki. Once the request is received, the IT security team will scan the web application. If no critical/high vulnerabilities reported, we will move the webpage to userid.dmz.hpc.mssm.edu for public access. The time to complete this request will be depending on the vulnerability status of the website. A rough estimate is 1 week. 

 

Account Creation Time Delay: It may take between 30min – 1hour after user account creation for the system to automatically subscribe a user to web services at https://userid.u.hpc.mssm.edu. If you are a new user, please endure the delay and check web services again before submitting a trouble ticket.

 

Usage, Security/SSL, Availability and Accessibility
The Minerva web services are designed to support basic web pages for the intent and purpose of providing web-based science portals and other application interactions and to interact with the jobs, queues, and large science datasets on Minerva. The web services should be on-par with common unix-based web hosting services such as Dreamhost etc. That said, we don’t recommend using web services as a primary website for a department or lab, websites with heavy traffic, or for websites that require a guaranteed amount of uptime. There are no uptime guarantees. Web services may go down during Preventative Maintenance periods. We may block IPs with suspicious activity for periods of time, possibility indefinitely, possibly automatically.
One possible usage style may be to create a website for your department or lab elsewhere and pull specific real-time generated content from the Minerva web services via javascript includes, redirects, and API’s.

Usage is unlimited for the purposes of interaction with Minerva components and viewing science.

WARNING WARNING WARNING: Be careful! Content, executables, scripts, symlinks, applications, etc within the www/ folder may be ( or are ) world accessible. Scripts and applications launched via Apache in that folder run as your user! They can access any data (including your groups’ /project data), delete data, archive data, submit jobs, cancel jobs, email people, etc as your user. You are responsible for any actions taken on your behalf!

 

Getting Started with User-based Web Services
Each user, by default, has a web services site. A specific user’s site is located at users.hpc.mssm.edu/~userid The Document Root for a user’s site is within their home folder in a folder called www.
Thus, https://userid.u.hpc.mssm.edu/~username/www

Step 1:
If this folder does not exist in your home directory, you should create it.

$ mkdir ~/www

Step 2:
Place content in the www folder.

$ cat > ~/www/index.html <<EOF

Hello World from my website.

EOF

Step 3:
Test your web services. Point your browser to https://userid.u.hpc.mssm.edu

 

Supported Scripting Languages
The following languages are supported: PHP, Python, Perl, and CGI (all others).
The Default extensions for each language:

Language	Extension	Index
PHP 7.3.14	.php	index.php
Python 3.8.2 (wsgi)	See Below	See Below
Perl 5.16.3	.pl	index.pl
CGI	.cgi	index.cgi
PHP:

Web Services supports PHP. Files must end in a .php extension. The service is based on PHP 7.3.14. The following modules / patches are installed: gd ldap json mysql pear xml pear-DB pgsql mbstring pecl-imagick imap snmp suhosin zlib.
Clean URL’s may be supported through a .htaccess modification. Please read the PHP and your application’s documentation for how it should be done for your application.

An example of “Hello World” in PHP:

<?php

print “Hello World”;

?>

Python:

wsgi method:

The WSGI method of hosting python programs is new and more elegant compared to the mod_python method. It is now the standard for many frameworks, such as Django, Pyramids, etc.
( Please note this is WSGI-Script, not the daemonized / threaded style. Most apps should be able to handle this correctly. )
The Key to hosting a WSGI app is creating a directory in which you put the WSGI file, which will serve as the root of the app.
For example, if you want to host an app at https://userid.u.hpc.mssm.edu/myapp such that the URLs https://userid.u.hpc.mssm.edu/myapp/subpage and https://userid.u.hpc.mssm.edu/myapp/subpage/anotherthing are all handled by your WSGI app, then, do the following:
Create the folder by your main app name inside the root of your www folder:

mkdir ~/www/myapp

Then add a .htaccess file to declare that all files in that folder will be WSGI files. Put the following lines in a .htaccess file, like so:
Example of how to edit:

vi ~/www/myapp/.htaccess

Line to put in the file:

SetHandler wsgi-script

At this point, if you create a test WSGI app in your folder it will work, for example:

$ cat wsgione.wsgi
def application(environ, start_response):
status = '200 OK'
output = b’Hello World!’
response_headers = [(‘Content-type’, ‘text/plain’),(‘Content-Length’,
str(len(output)))]
start_response(status, response_headers)
return [output]
$
The app above would be available at https://userid.u.hpc.mssm.edu/myapp/wsgione.wsgi URLs beyond that would also be handled by it, for example: https://userid.u.hpc.mssm.edu/myapp/wsgione.wsgi/one/two/three
(In this case, of course, nothing is done with those URLs, but something could be in your app, like with Django).
Of course, this URL is not very clean, so we can move the file from wsgione.wsgi to wsgione ( just removing the .wsgi ) and it will still work. Thus, you could host many apps in one folder, for example, /apps/app1, /apps/app2 .. etc etc.

That too, still may not be ideal for some use cases, so finally, it is possible to make a main top-level folder redirect into an app, however, ideally you should make a folder that only contains the one WSGI file and the .htaccess file.
You will need to call your WSGI file index.wsgi.
Then set your .htaccess file like so:

AddHandler wsgi-script .wsgi
DirectoryIndex index.wsgi
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*)$ index.wsgi/$1 [QSA,PT,L]
This will rewrite https://userid.u.hpc.mssm.edu/myapp/one/two/three into the index.wsgi file and pass-in the URL parameters for the app to handle.

Note: mod_python is no longer supported in our new webserver.

Some demos on setting up your first python flask and dash app (campus network or VPN needed to access)
https://gail01.u.hpc.mssm.edu/flask_demo/
https://gail01.u.hpc.mssm.edu/dash_demo/
Code is at https://gail01.u.hpc.mssm.edu/code/

Perl and CGI:

Perl is supported through mod_perl and CGI. Simply end your file with a .pl or .cgi extension and ensure it is executable on the filesystem. (for example do a chmod +x test.pl to make it executable). As the Perl is being executed as CGI, you must put a “shebang” at the beginning of your file to point to the perl executable. The service is based on Perl 5.10.1
The following Perl modules are installed for sure, in addition to possibly others: perl-DBI perl-URI perl-IO-Socket-SSL perl-Net-DNS perl-HTML-Tagset perl-Archive-Tar perl-Net-IP perl-DBD-MySQL perl-Socket6 perl-Digest-SHA1 perl-IO-Socket-INET6 perl-Compress-Zlib perl-DBD-Pg perl-HTML-Parser perl-IO-Zlib perl-BSD-Resource perl-SGMLSpm perl-libwww-perl perl-Digest-HMAC perl-Net-SSLeay perl-Pod-POM perl-Pod-Simple perl-Date-Calc

An example Perl Hello World:

#!/usr/bin/perl
print “Content-type: text/html\\n\\n”;
print “Hello, world!\\n”;
As Perl here is executed as if it was CGI, you may instead create any executable file, including shell script (bash, csh, ksh) or even an executable binary with an appropriate name to execute it.

 

Authentication
It may be desirable to force visitors to authenticate before viewing a portion (or all) of your website.
Authentication is supported in two ways: System Auth or Password File

System Auth

System Auth will authenticate users using their Mt Sinai / Minerva Credentials, the same as you ssh login to Minerva. For the campus-facing web servers, password auth is supported.

To enable System Auth, you should create a .htaccess file in the root of the directory structure you wish to protect.

The .htaccess file to use for System Auth is:

AuthType Basic
AuthName Your-Site-Name
AuthBasicProvider external
AuthExternal pwauth
require valid-user
Password File

Password File will allow you to create a file with usernames and passwords combinations that will allow successful logins. Password File is not secure. It allows you to create usernames and passwords such as user “test” with password “test”. It’s not for serious authentication, however, it can be useful for light protection of folders that you just don’t want Google to accidentally run in to, for example. Please do not create bogus passwords such as test or password
To enable Password File auth, you will need to create a password file and create a .htaccess file in the root of the directory structure you wish to protect.

The password file should sit somewhere that is outside of the www/ folder. A good place may be in a folder in your home directory. If you desire, you can create a folder in your home directory for just storing these types of file, or possible a hidden folder in your home directory (one beginning with a dot)

To create a folder and password file and user named test:

$ mkdir ~/.htpasswords
$ htpasswd -c ~/.htpasswords/passdb1 test
New password:
Re-type new password:
Adding password for user test
The .htaccess file to use for Password File is:

AuthType basic
AuthName “private area”
AuthUserFile    “/hpc/users/user1/.htpasswords/passdb1”
Require            valid-user
Order allow,deny
Allow from all
Note: You should place the full and proper path to the password file in the AuthUserFile line.
Note:

Apache 2.2: Used Allow, Deny and Order directives for access control.
Apache 2.4: Uses the Require directive for a modern, unified system. For example, use Require ip 192.168.1.0/24 or Require all denied to grant access to clients within a specific IP address range (from 192.168.1.0 to 192.168.1.255) or to denies access to all clients.
mod_access_compat: Temporarily supports old directives in 2.4 but may be removed in any update.
Action Needed: Update .htaccess and configs to use the Require directive to avoid future disruptions.
Risk: Failure to update could break access controls, impacting security or availability.
General:
You may prefer some variations. For example:
Varing the line AuthName will show the user a different prompt at the login box.
Varing the require valid-user to require user user1 user2 will restrict access to user1 and user2 only. Similarly, require group group2 will restrict access to group2
Other variations are possible. Anything that may be supported in a .htaccess file is allowed (some restrictions). You can block based on remote IP etc.
Some Sites show many possible variations.

Please read the Apache documentation on Basic Auth and The Require Directive

 

Group-based Web Services
As described above, web services are currently only created, by default, for each user. For a group-based web service, or a custom-named web service, please submit a ticket by emailing hpchelp@hpc.mssm.edu with your request. Do note that the web service will need to run as some specific user, so please plan ahead as to which user should own the web service.
Similarly, custom web services (see below) on a per-user or per-group basis may use non-default domain names, however, it is a special request.

 

Domain Names
By default, the Scientific Computing group places all services below the hpc.mssm.edu domain. For automated purposes, we place the generated web service accounts below the u.hpc.mssm.edu domain (u for “users”). Although this provides a quick way for users to start working with web services, it may not be desirable a desirable URL for certain cases. Shorter domains typically look nicer. Long URL’s with usernames can be confusing.

The Scientific Computing group does not have the ability to acquire or control URL’s outside of our own domain. However, your user / group may go through either Mt Sinai IT to acquire some other *.mssm.edu (or similar) domain and/or purchase a domain from a public entity, such as Network Solutions, ENOM, or Godaddy. You will also need some sort of Domain Name Hosting services. You can then create an A or CNAME record to point to our web hosting IP address(es). We can configure your account to accept requests for these additional domains.

The Scientific Computing group and the web services do not provide Domain Name Hosting or Email Services.

SSL

SSL with domain names outside the hpc.mssm.edu domain presents some challenges. Each unique domain will require an additional IP Address. This will need to be evaluated on a case-by-case basis to see if there are remaining addresses to address the need. Please plan ahead before making any purchases to ensure the service will function correctly.
Other hybrid variations may provide useful. For example, the non-secure content can be on the custom domain and the secure content can be on the default u.hpc.mssm.edu domain. The two sites can pass the user back and forth as needed.

 

GPG
What Is GPG
GPG stands for GNU Privacy Guard and is a set of encryption tools to protect data transmitted from one person to another over an untrustworthy medium such as the internet. Used properly it can ensure that any third-party who unintentionally obtains a file will be unable to decrypt the contents.

Usage of GPG on Minerva
On Minerva GPG is best suited for use when publishing files on user/DMZ pages for access by people outside of Minerva. Within Minerva it is more secure to use file permissions or Access Control Lists (ACL’s) to allow other users to access your files.

Limitations of GPG on Minerva
GPG cannot be used to distribute PHI/PII or any other form of controlled data. Files containing PHI/PII are strictly not permitted on user web pages even if they are encrypted.

How does GPG Work

Using GPG on Minerva

Step 1: Generate a Key Pair

On Minerva run `gpg –gen-key`. This will begin a series of prompts which are explained below.
For the first prompt select option “(1) RSA and RSA (default)”. This option determines which type of keys to generate.
For the second prompt press enter to select the default size of the key (2048 bit). For added protection from brute force you can enter 4096.
Do not use any value less than 2048.
The third option determines how long the key will be good for. Files may be encrypted using temporary keys which will expire after a defined amount of time. Most users will want a key that will never expire.
Enter 0 to generate a key with an infinite lifetime, or use the formatting specified in the prompt to enter the duration for which you want the key to be valid.
The next three prompts request your real name, email address, and an optional comment.
After entering these, review the entries and when ready select “(O)kay” by entering O.
A prompt will appear asking for a passphrase. This is a password that is required to unlock the key.
*DO NOT* use your Minerva password or any password you use on any other service for this purpose. This should be a unique password. A second prompt will ask you to verify the password.
*DO NOT* generate keys with no passphrase as these offer no security.
There will be a message that begins with “We need to generate a lot of random bytes.” The key is now being generated which can take several minutes to complete.
Your keys have now been created and can be verified by running `gpg –list-keys`
Step 2: Encrypt a File

Run the command `gpg -e` followed by the name of the file you want to encrypt.
A prompt will ask you to “Enter the user ID”. This should be an email address. You may use your own email address.
When all recipients have been entered press RETURN to continue.
You will now have a file with the same name but ending in .gpg in the directory. For example, if you encrypt “private-file.txt” you will have a new “private-file.txt.gpg” which has been encrypted.

Step 3: Decrypting a File

Run the command `gpg` followed by the name of the encrypted file.
To decrypt the file “private-file.txt.gpg” you would enter `gpg private-file.txt.gpg`.
You will be prompted to enter the passphrase you configured in Step 1. If you enter the wrong passphrase the file will not be able to be decrypted.
Distributing Keys
Any person who downloads a file you have encrypted will need a copy of the public key that you generated in Step 1. This can be sent via any medium.

A best practice is to distribute the key directly to intended recipients via email.

Obtaining the Public Key to Distribute

Run `gpg –export -a –output KEY_NAME.asc EMAIL` where KEY_NAME will be the filename and EMAIL is the email address you entered as part of Step 1.
This will create a file with the specified name that contains the key in plain text. You may attach this file to email or copy/paste the file contents into the email body. The recipient will simply need to save a copy of this data to a file which can be imported into GPG on their computer.

Importing Keys
There are many options for use in GPG that relate to what type of encryption is done, and ways to automate the entire process by populating prompt data from the command line. Please view the man page using `man gpg` or accessing

More Information
There are many options for use in GPG that relate to what type of encryption is done, and ways to automate the entire process by populating prompt data from the command line. Please view the man page using `man gpg` or accessing
https://www.gnupg.org/documentation/manpage.html

The GPG webpage has extensive information about usage of GPG, best practices, and advanced topics. Please visit https://www.gnupg.org/index.html
Database Services
The Scientific Computing group is providing basic database services. These can be used in conjunction with the web services or separately to hold science results of limited size, 50 GiB.

MySQL database:
Currently, MySQL relational database service is provided using MariaDB 10.1. At this moment, there is no backup for the databases.

Other databases:
The Scientific Computing group has very limited capacity to host custom database servers for applications which require deep levels of specific versions of packages or other custom software, such as Oracle SQL Server. DB server availability is very limited and evaluated on a case-by-case basis. Please submit a ticket with your request by emailing hpchelp@hpc.mssm.edu.

 

MariaDB/MySQL Database
MariaDB is a fork of the MySQL relational database management system. It intends to maintain high compatibility with MySQL, ensuring a drop-in replacement capability with library binary equivalency and exact matching with MySQL APIs and commands.

Compatibility & Differences Between MariaDB and MySQL

There are two networks can connect to the MariaDB database:

Internal Network: Inside Minerva HPC cluster (usually, on the login nodes li03c01-04, web server web01, and compute nodes, you can connect to the database using the internal network, data1.chimera.hpc.mssm.edu (172.28.4.160).

External Network: On your laptop or other campus computers, connect use data1.hpc.mssm.edu (10.95.46.73)

To access within minerva, without chimera.hpc.mssm.edu is OK:

 $ mysql -h data1 -u userid -p your_database
To access within campus network, eg on your laptop, use data1.hpc.mssm.edu, .hpc.mssm.edu is needed:

 $ mysql -h data1.hpc.mssm.edu -u userid -p your_database
To backup your database, do

$ mysqldump -u userid –p your_database [tablename] -h data1 > [dumpfilename.sql]
Database users are not synchronized with Minerva users at this time. To create users and databases, please submit a ticket by emailing hpchelp@hpc.mssm.edu.

 

External Links
MariaDB Tutorial
MariaDB Documentation

Ollama
Ollama is a platform that enables users to interact with Large Language Models (LLMs) via an Application Programming Interface (API). It is a powerful tool for generating text, answering questions, and performing complex natural language processing tasks. Ollama provides access to various fine-tuned LLMs, allowing developers and researchers to integrate sophisticated language understanding and generation capabilities into their applications, such as chatbots, content creation tools, and research projects. With its easy-to-use API, Ollama streamlines the process of leveraging advanced Artificial Intelligence (AI) models, making it accessible for a wide range of users in different fields. For detailed information, please check here.

We provide an Ollama wrapper script that allows you to start an Ollama server on Minerva’s compute node and access it from your local machine through an API endpoint. This setup enables computationally expensive LLM tasks to be performed on Minerva, while you can easily access the results from your local machine. Below are step-by-step usage instructions.

 

Usage
The Ollama script is available on the login node at the following location:

/usr/local/bin/ 
The script name is: minerva-ollama-web.sh.

To start the script, run minerva-ollama-web.sh on a login node.

Example:

$ sh minerva-ollama-web.sh 
[INFO] Image not specified, check if previously used 
[INFO] Found previously used image /hpc/users/hasans10/minerva_jobs/ollama_jobs/ollama_v0.3.10.sif. Using it. 
[INFO] Project is not specified, or is acc_null, using 1st avail project. 
[INFO] Project to use is acc_hpcstaff 
[INFO] Parameters used are: 
[INFO] -n       4
[INFO] -M       300 
[INFO] -W       6:00
[INFO] -P       acc_hpcstaff 
[INFO] -J       ollama 
[INFO] -q       gpu 
[INFO] -R       v100 
[INFO] -g       1 
[INFO] -o       /hpc/users/hasans10/minerva_jobs/ollama_jobs
[INFO] -i       
/hpc/users/hasans10/minerva_jobs/ollama_jobs/ollama_v0.3.10.sif 
[INFO] Submitting Ollama job... 
[INFO] Job ID: 189591101 
Ollama is started on compute node lg03a03, port 9999 
[Authentication Info] 
User ID: hasans10 
Token: d6972ea4292e66e93d4ece5f15831f14 
Access URL: http://10.95.46.94:51101 

*** You can access Ollama from Python as shown below ***** 
from ollama import Client
ollama_client = Client(host='http://10.95.46.94:51101', headers={"Authorization": "Bearer hasans10:d6972ea4292e66e93d4ece5f15831f14"})
By default, this script will start an Ollama server on Minerva’s GPU node. If the script runs successfully, it will provide a URL such as http://10.95.46.94:51101, your user ID, and a randomly generated token. If you run a curl command with your URL, user ID, and token, you will see a message indicating that ‘Ollama is running’ (an example is provided below). PLEASE DO NOT SHARE YOUR URL AND TOKEN WITH OTHERS.

$ curl http://10.95.46.94:51101 -H "Authorization: Bearer hasans10:d6972ea4292e66e93d4ece5f15831f14"
Ollama is running
This indicates that the service is up and running, and you can interact with it. The generated URL will also serve as your API endpoint.

Ollama is configured to run on a GPU node by default, as CPU execution is slow. The script is flexible, allowing you to change various parameters. To see the usage instructions, please run the following command:

minerva-ollama-web.sh --help
 
Install Ollama Python Library
You can access Ollama through the Python library. If you don’t have the Ollama Python library installed, use the following commands to install it on Minerva:

module load python/3.10.14
pip install --user ollama==0.3.1
Alternatively, after starting the Ollama server on Minerva, you can also access it from your local machine. To install the Ollama Python library on your local machine, use the following command:

pip install ollama
For more details, visit the Ollama Python library GitHub page.

 

Download Model and Chat
You can download a model using the URL received after job submission. For example:

from ollama import Client
ollama_client = Client(host='http://10.95.46.94:51101', headers={"Authorization": "Bearer hasans10:d6972ea4292e66e93d4ece5f15831f14"})
ollama_client.pull('tinyllama')
You can then chat with a model as follows:

stream = ollama_client.chat(
    model='tinyllama',
    messages=[{'role': 'user', 'content': 'What are the main causes of cardiovascular disease?'}],
    stream=True,
)
for chunk in stream:
    print(chunk['message']['content'], end='', flush=True)
Sample Output:

There are several causes of cardiovascular disease, which includes:
1. Hypercholesterolemia or high cholesterol: This is a major cause of cardiovascular disease (CVD). High levels of bad (Low-density lipoprotein) ("LDL") cholesterol can lead to thickening and plaque buildup in the blood vessels, which can eventually block or rupture them.
2. Hypertriglyceridemia: This is another major cause of CVD, especially when triglycerides (fatty substances) are present in large amounts.
3. Hypertension (high blood pressure): High blood pressure can damage the arteries and reduce blood flow to the heart and other organs.
4. Diabetes: This condition results in high levels of glucose (sugar)in the bloodstream, which can cause plaque buildup in the arteries.
5. Smoking and alcohol use: Both smoking and excessive alcohol consumption can damage the lining of blood vessels, leading to inflammation and increased risk of CVD.
6. Physical inactivity: A sedentary lifestyle is associated with an increased risk of CVD due to reduced blood flow and oxygen supply to organs and muscles.
7. Obesity: Excess body fat can cause a buildup of plaque in the arteries, leading to CVD.
8. Atherosclerosis: This is the thickening and hardening of artery walls due to inflammation and plaque accumulation.
 

Change Ollama Work Directory
 By default, Ollama stores models in your HOME directory. Some Ollama models are quite large and may exceed the 20GB size limit of your HOME directory. To avoid this issue, you can use your project directory (or another directory with sufficient space) as the Ollama work directory. For example, you can change the work directory as shown below.

sh minerva-ollama-web.sh -o /sc/arion/projects/<projectid>
or

sh minerva-ollama-web.sh --ollamaworkdir /sc/arion/projects/<projectid>


