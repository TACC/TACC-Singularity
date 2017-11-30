# From Docker to Singularity
============================

The following tutorial explains the steps to convert a Docker image into a Singularity image and run it on TACC's Stampede system.

### Requirements
* [Docker](http://www.docker.com/products/overview): version 1.10+ on Mac, Windows, or Linux.
* [Singularity](http://singularity.lbl.gov/): version 2.1+ (on Stampede).

### Sample files
* [blast.tar.gz](https://de.iplantcollaborative.org/anon-files/iplant/home/eriksf/share/blast.tar.gz): contains query FASTA file (proteins.fasta) and BLASTable database (pdbaa).

### Step-by-step guide
1. Grab a Docker image from the [Docker hub](https://hub.docker.com/) (or use one that you've developed). *The image used in this tutorial contains NCBI BLAST+ binaries, version 2.2.30.*
    ```
    $ docker pull simonalpha/ncbi-blast-docker
    Using default tag: latest
    latest: Pulling from simonalpha/ncbi-blast-docker

    a3ed95caeb02: Pull complete 
    ee48d0fb051e: Pull complete 
    b92ecff543cc: Pull complete 
    93701159904b: Pull complete 
    Digest: sha256:89e68eabc3840b640f1037b233b5c9c81f12965b45c04c11ff935f0b34fac364
    Status: Downloaded newer image for simonalpha/ncbi-blast-docker:latest
    ```

2. Run [docker2singularity](https://github.com/singularityware/docker2singularity) to convert the docker image. This tool will run inside a docker container.
    ```
    $ docker run \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v $PWD:/output \
    --privileged -t --rm \
    singularityware/docker2singularity \
    simonalpha/ncbi-blast-docker
    
    Size: 984 MB for the singularity container
    (1/9) Creating an empty image...
    Creating a sparse image with a maximum size of 984MiB...
    Using given image size of 984
    Formatting image (/sbin/mkfs.ext3)
    Done. Image can be found at: /tmp/simonalpha_ncbi-blast-docker-2015-01-03-a603a76886c2.img
    (2/9) Importing filesystem...
    (3/9) Bootstrapping...
    (4/9) Adding run script...
    (5/9) Setting ENV variables...
    Singularity: sexec (U=0,P=145)> Command=exec, Container=/tmp/simonalpha_ncbi-blast-docker-2015-01-03-a603a76886c2.img, CWD=/, Arg1=/bin/sh
    (6/9) Adding mount points...
    Singularity: sexec (U=0,P=151)> Command=exec, Container=/tmp/simonalpha_ncbi-blast-docker-2015-01-03-a603a76886c2.img, CWD=/, Arg1=/bin/sh
    (7/9) Fixing permissions...
    Singularity: sexec (U=0,P=157)> Command=exec, Container=/tmp/simonalpha_ncbi-blast-docker-2015-01-03-a603a76886c2.img, CWD=/, Arg1=/bin/sh
    Singularity: sexec (U=0,P=196)> Command=exec, Container=/tmp/simonalpha_ncbi-blast-docker-2015-01-03-a603a76886c2.img, CWD=/, Arg1=/bin/sh
    (8/9) Stopping and removing the container...
    a603a76886c2
    a603a76886c2
    (9/9) Moving the image to the output folder...
      1,031,798,816 100%  114.23MB/s    0:00:08 (xfr#1, to-chk=0/1)
    ```
    Replace `$PWD` with a path on the host filesystem if you don't won't the Singularity image in the current working directory.
    
    **Note:** if you're getting the following error, `docker: Error response from daemon: client is newer than server`, then make sure that your version of docker2singularity matches your version of docker. Use `docker info` to check the docker version and then grab the corresponding version of docker2singularity. For example, if the docker version is 1.10, then replace the call to docker2singularity in the above command with `singularityware/docker2singularity:1.10`.
    
3. Copy the Singularity image to Stampede.
    ```
    $ scp simonalpha_ncbi-blast-docker-2015-01-03-791cbc3b0415.img eriksf@stampede.tacc.utexas.edu:/work/03762/eriksf/singularity/
    To access the system:

    1) If not using ssh-keys, please enter your TACC password at the password prompt
    2) At the TACC Token prompt, enter your 6-digit code followed by <return>.  

    TACC Token Code:
    simonalpha_ncbi-blast-docker-2015-01-03-791cbc3b0415.img                             100%  984MB   5.2MB/s   03:10
    ```
    Replace `eriksf` with your username on Stampede and `/work/03762/eriksf/singularity` with the directory where you'd like to place the Singularity image.

4. Download and copy the above sample files to Stampede.
    ```
    $ scp blast.tar.gz eriksf@stampede.tacc.utexas.edu:/work/03762/eriksf/singularity/
    To access the system:

    1) If not using ssh-keys, please enter your TACC password at the password prompt
    2) At the TACC Token prompt, enter your 6-digit code followed by <return>.  

    TACC Token Code:
    blast.tar.gz                                                                         100%   42MB  13.8MB/s   00:03
    ```
    Replace `eriksf` with your username on Stampede and `/work/03762/eriksf/singularity` with the directory where you'd like to place the sample files.

5. Extract the sample files in the work directory on Stampede.
    ```
    login1.stampede(27)$ cd $WORK/singularity
    login1.stampede(28)$ pwd
    /work/03762/eriksf/singularity
    login1.stampede(29)$ tar zxvf blast.tar.gz
    blast/
    blast/db/
    blast/proteins.fasta
    blast/db/pdbaa
    blast/db/pdbaa.fasta
    blast/db/pdbaa.phr
    blast/db/pdbaa.pin
    blast/db/pdbaa.pnd
    blast/db/pdbaa.pni
    blast/db/pdbaa.pog
    blast/db/pdbaa.psd
    blast/db/pdbaa.psi
    blast/db/pdbaa.psq
    ```
    
6. (Optional) Examine the Singularity image on Stampede. As the image file is a physical representation of the container environment, you can obtain an interactive shell to examine it. *Singularity is NOT installed on the login nodes, so you must use an [interactive session](https://portal.tacc.utexas.edu/user-guides/stampede#interactive-sessionsrunning-envs-dev) in the development queue using `idev` or `srun`.*
    ```
    login2.stampede(17)$ idev -p development

    Defaults file    : ~/.idevrc
    Default  project : iPlant-Collabs
    Default  time    : 30 min.
    Default  queue   : development
    System           : Stampede
    Default tot tasks: -n 16 
    Default nodes    : -N 1
      end of defaults: ----------------------------------------------
    Using queue      : -p development
    -----------------------------------------------------------------
              Welcome to the Stampede Supercomputer              
    -----------------------------------------------------------------

    No reservation for this job
    --> Verifying valid submit host (login2)...OK
    --> Verifying valid jobname...OK
    --> Enforcing max jobs per user...OK
    --> Verifying availability of your home dir (/home1/03762/eriksf)...OK
    --> Verifying availability of your work dir (/work/03762/eriksf)...OK
    --> Verifying valid ssh keys...OK
    --> Verifying access to desired queue (development)...OK
    --> Verifying job request is within current queue limits...OK
    --> Checking available allocation (iPlant-Collabs)...OK
    Submitted batch job 7822232

     After your idev job begins to run, a command prompt will appear,
     and you can begin your interactive development session. 
     We will report the job status every 4 seconds: (PD=pending, R=running).

    job status:  PD
    job status:  PD
    job status:  PD
    job status:  R
    --> Job is now running on masternode= c557-802...OK
    --> Sleeping for 7 seconds...OK
    --> Checking to make sure your job has initialized an env for you....OK
    --> Creating interactive terminal session (login) on master node c557-802.
    TACC Stampede System
    LosF 0.40.0 (Top Notch)
    Provisioned on 23-Sep-2012 at 15:36
 
    c557-802.stampede(1)$ singularity shell simonalpha_ncbi-blast-docker-2015-01-03-791cbc3b0415.img 
    Singularity.simonalpha_ncbi-blast-docker-2015-01-03-791cbc3b0415.img> $ cd /
    Singularity.simonalpha_ncbi-blast-docker-2015-01-03-791cbc3b0415.img> $ ls -l
    total 1080
    drwxr-xr-x     2 root root   4096 Nov  3 17:22 beegfs
    drwxr-xr-x     2 root root   4096 Dec 31  2014 bin
    drwxr-xr-x     2 root root   4096 Nov  3 17:22 blast
    drwxr-xr-x     2 root root   4096 Sep 21  2014 boot
    drwxr-xr-x     2 root root   4096 Nov  3 17:22 corral-repl
    drwxr-xr-x    20 root root   3840 Nov  3 18:37 dev
    -rw-r--r--     1 root root     88 Nov  3 17:22 docker_environment
    -rw-r--r--     1 root root    293 Nov  3 17:22 environment
    drwxr-xr-x    34 root root   4096 Nov  3 17:22 etc
    drwxr-xr-x     2 root root   4096 Sep 21  2014 home
    drwxr-xr-x  4064 root root 196608 Nov  3 01:08 home1
    drwxr-xr-x     7 root root   4096 Jun 22  2012 lib
    drwxr-xr-x     2 root root   4096 Dec 31  2014 lib64
    drwxr-xr-x     2 root root   4096 Nov  3 17:22 local-scratch
    drwxr-xr-x     2 root root  16384 Nov  3 17:22 lost+found
    drwxr-xr-x     2 root root   4096 Dec 31  2014 media
    drwxr-xr-x     2 root root   4096 Nov  3 17:22 mkdir
    drwxr-xr-x     2 root root   4096 Sep 21  2014 mnt
    drwxr-xr-x     2 root root   4096 Nov  3 17:22 oasis
    drwxr-xr-x     3 root root   4096 Jan  3  2015 opt
    dr-xr-xr-x   629 root root      0 Oct 26 10:32 proc
    drwxr-xr-x     2 root root   4096 Nov  3 17:22 projects  
    drwxr-xr-x     2 root root   4096 Dec 31  2014 root
    drwxr-xr-x     5 root root   4096 Dec 31  2014 run
    drwxr-xr-x     2 root root   4096 Dec 31  2014 sbin
    drwxr-xr-x  4065 root root 208896 Nov  3 01:08 scratch
    drwxr-xr-x     2 root root   4096 Jun 10  2012 selinux
    drwxr-xr-x     3 root root   4096 Nov  3 17:22 share
    -rwxr-xr-x     1 root root     18 Nov  3 17:22 singularity
    -rw-r--r--     1 root root   5941 Nov  3 17:22 singularity.json
    drwxr-xr-x     2 root root   4096 Dec 31  2014 srv
    drwxr-xr-x    13 root root      0 Oct 26 10:32 sys
    drwxrwxrwt.   10 root root 348160 Nov  3 18:42 tmp
    drwxr-xr-x    10 root root   4096 Dec 31  2014 usr
    drwxr-xr-x    11 root root   4096 Dec 31  2014 var
    drwxr-xr-x  4097 root root 196608 Nov  3 01:08 work
    Singularity.simonalpha_ncbi-blast-docker-2015-01-03-791cbc3b0415.img> $ cat singularity 
    #!/bin/sh
    bash $@
    Singularity.simonalpha_ncbi-blast-docker-2015-01-03-791cbc3b0415.img> $ 
    ```
    
    A couple of things to note:
    * Singularity containers are almost completely read-only, only `$HOME`, `/work`, and `/scratch` are writable and map to the filesystems on the host
    * Environment variables set outside of the container will be active inside the container unless overridden by it
    * The file/script `/singularity` controls what runs when the image is called with the `singularity run` command or directly (`singularity (options) run [container image] (options)`). As you can see in the above file, this container runs `bash`.
    * To run arbitrary commands within the container, use the `singularity exec` command (`singularity (options) exec [container image] [command] (options)`)
    
7. Run a batch job with our BLAST singularity image using SLURM. The images serve as stand-alone programs and can be executed like any other program on the host. *The unique identifier in the filename of the image you uploaded will be different that what appears in the sample SLURM script here so make sure to use your filename.*  
    ```
    login2.stampede(22)$ cat run_singularity_ncbi-blast.slurm
    ```
    
    ```bash
    #!/bin/bash

    #SBATCH -p development
    #SBATCH -t 00:05:00
    #SBATCH -n 1
    #SBATCH -A iPlant-Collabs 
    #SBATCH -J test-singularity-ncbi-blast
    #SBATCH -o test-singularity-ncbi-blast.o%j

    # Set up inputs, parameters, and outputs
    image="simonalpha_ncbi-blast-docker-2015-01-03-791cbc3b0415.img"
    blast_command="blastp"
    input="blast/proteins.fasta"
    database="blast/db/pdbaa"
    output="proteins_blastp.txt"

    # move into work directory
    cd $WORK/singularity

    # Run the actual program
    singularity exec ${image} ${blast_command} -query ${input} -db ${database} -out ${output}
    ```
    
    ```
    login2.stampede(22)$ sbatch run_singularity_ncbi-blast.slurm 
    -----------------------------------------------------------------
                  Welcome to the Stampede Supercomputer              
    -----------------------------------------------------------------

    No reservation for this job
    --> Verifying valid submit host (login2)...OK
    --> Verifying valid jobname...OK
    --> Enforcing max jobs per user...OK
    --> Verifying availability of your home dir (/home1/03762/eriksf)...OK
    --> Verifying availability of your work dir (/work/03762/eriksf)...OK
    --> Verifying valid ssh keys...OK
    --> Verifying access to desired queue (osu)...OK
    --> Verifying job request is within current queue limits...OK
    --> Checking available allocation (iPlant-Collabs)...OK
    Submitted batch job 7822462
    login2.stampede(27)$ 
    ```
    
    In this case, the output file `proteins_blastp.txt` will end up in the `$WORK/singularity` directory.

