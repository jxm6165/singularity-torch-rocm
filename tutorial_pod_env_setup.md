# Tutorial for POD AMD Cluster Setup

*Last updated: **Oct 18, 2022** by Jianyu Mao*

>   This tutorial serves as a guideline for Ubuntu environment setup and Python-based Deep Learning frameworks installation with full utilization of [AMD ROCm<sup>TM</sup>](https://docs.amd.com/bundle/ROCm-Deep-Learning-Guide-v5.3/page/Introduction_to_Deep_Learning_Guide.html) acceleration on [POD<sup>TM</sup>](https://pod.penguincomputing.com/) MT3 Clusters. Please note the last updated date. This tutorial may be obsolete and needs adjustment.


------------------------------------------------------------------------


## Introduction to AMD ROCm and POD

### 1. AMD ROCm and Deep Learning

AMD ROCm™ is the first open source software development platform for HPC/Hyperscale-class GPU-accelerated high performance computing and machine learning. Widely-used DL frameworks (PyTorch, Tensorflow, etc.) are supporting ROCm including full capability for mixed-precision and large-scale training using AMD’s MIOpen & RCCL libraries.

### 2. Penguin Computing® On Demand™ and MT Clusters

Penguin Computing® On Demand™ (POD™) is a HPC computing environment in the cloud provided by Penguin Computing. Refer to [this documentation](https://pod.penguincomputing.com/documentation/) for more details about POD if necessary.

POD resources are divided into several MT clusters. We will have access to a MT2 instance (CPUs enabled) as the login node and MT3 instances (CPUs and AMD GPUs enabled) as compute nodes. **Note that login nodes (MT2) are used for administrative tasks, not for research computing.** **We are required to setup our own workplace on compute nodes in MT3 clusters.** In this tutorial, *compute nodes*, *MT3 nodes* and *MT3 instances* are used interchangeably.


------------------------------------------------------------------

## Guideline for Access POD Clusters

This section provides a step-by-step guideline for access POD instances including how to create POD account, sign in to POD and access compute nodes (MT3 instance) through ssh client. Please check POD ducumentation:  [Account Creation](https://pod.penguincomputing.com/documentation/account-creation.html#) & [Account Setup](https://pod.penguincomputing.com/documentation/account-setup.html) for more information.

### 1. Account Creation

*   Check if a invitation email sent by POD is received. If not, please contact your supervisor or Dr.Xu for help.
*   Once invitation is received, click on the <u>Sign-up on the POD Portal</u>.
*   Type in your email address and create a password on that page.
*   Follow the steps by POD to verify your email address and create your POD ***username***.
*   Note that the POD ***username*** will be your `$USER` name on the MT instances.



### 2. Sign in to POD Portal

*   Sign in to your personal POD portal by entering your email address and password created in the last step on [this page](https://pod.penguincomputing.com/).
*   Now, you are able to overview your account and monitor your resource usage on MT clusters.
*   Click on <u>POD MT3</u> on the left panel under the <u>RESOURCES BY LOCATION</u> Tab
*   Make sure you have access to one node in MT3 cluster. If you encounter a message saying “Cannot access the instances at this time”. Please ask Dr.Xu for assistance.
*   Remember the IP **104.224.20.197** under **Hostname / IP**. This is the node IP we will try to connect to. (IP may differ, but make sure you have access to at least one node in MT3.)
*   Click on <u>SSH Keys</u> on the left panel for the next step.



### 3. Create SSH Key Pair

The following steps are suitable for **Linux and MacOS** users. If you are using a **Windows** local machine, please visit [**git bash for ssh key connection**](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) for painless instructions on Git bash. 

*   POD documentation (last modified in 2020) says,

    >   Once completed you will have two sets of access credentials for POD:
    >
    >   >   1.  E-mail address and password used for logging in with a Web Browser
    >   >   2.  Username and SSH key-pair used for logging in with an SSH client

    which is out-of-date in 2022. We are required to create SSH key Pair because this is the **only** way to access MT nodes from now on.

*   Open **Terminal** on your local machine.

*   Run the following commands.

    *   ```bash
        localname@local:~$ eval `ssh-agent`
        Agent pid xxxxx # This ouput indicate ssh-agent is running.
        localname@local:~$ ssh-keygen -t rsa -C "penguin@mylaptop"
        Generating public/private rsa key pair.
        Enter file in which to save the key (/Users/localname/.ssh/id_rsa): # Press Enter
        Enter passphrase (empty for no passphrase): # Press Enter
        Enter same passphrase again: # Press Enter
        Your identification has been saved in id_rsa.
        Your public key has been saved in id_rsa.pub.
        The key fingerprint is:
        SHA256:sc+GdfQhQqICl5WjRKsyIjxliJuwy3u23iXpnsCRuQM penguin@mylaptop
        The key's randomart image is:
        +---[RSA 2048]----+
        |  ..oo... .      |
        |. .oo.o. o       |
        |o. +o.... . o .  |
        |o+o.+.   o o o . |
        |OE.+    S . . .  |
        |+++ o .  = .     |
        |.. = o .. +      |
        |  .o= +  .       |
        | .+oo=           |
        +----[SHA256]-----+
        localname@local:~$ cat $HOME/.ssh/id_rsa.pub # Copy the whole output
        ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCsvctNQODHWNCAPdONfibjIW6YcMA1xvk/rmevRiTX
        s1/QD68DVQWHyO5agOMwH4ve09QezcwMSanIuhn/rGVOd/XYoDd9AnMe2t0PFRl7ubPL2wswBUeVeryU
        1HtF+nLpcGSBrzvrwP/UjcpctACUjajFBtsxS/R4n/tg6rLG3u2epCi9igUeUnfsBgR+r88wVTab3eNk
        zNAXyzLJJZgulWXeShwFQYddecOTCdw9XXKwS9UQzuS8XCW0KgN02eNC/YlTvJITzm+eqktgVZKoW2zJ
        v9iwcUIhXyWzJEUI7rPz7Pb1gXBp3Z/gJWQYc+81TDu9BMx0oIp7KWOB7rtf penguin@mylaptop
        ```

*   Copy the output of `cat $HOME/.ssh/id_rsa.pub`.

*   From the last section, your browser should stay in the **My SSH Keys** page. In the **Add an SSH Key** section, type in `penguin@mylaptop` in the text box under **Name**. 

*   Paste the output of `cat $HOME/.ssh/id_rsa.pub` in the text box under **key**. 

*   Check :white_check_mark: boxes for both **POD MT2** and **POD MT3** so that we can use this SSH key to access both MT2 and MT3 nodes.

*   Click on the blue button **Upload New Key** to add your key.

*   After a few seconds, your ssh key will show under **My SSH Keys**.

*   Troubleshooting: If you cannot upload your key, make sure the key is starting with `ssh-rsa` and ending with `penguin@mylaptop` and your whole key string should be **one** line. **Do Not** start a new line betwen the key content and `penguin@mylaptop` .

*   Add the generated private key to you local machine by running the following commands.

    *   ```bash
        localname@local:~$ ssh-add -l
        The agent has no identities.
        localname@local:~$ ssh-add ~/.ssh/id_rsa
        Identity added: /Users/localname/.ssh/id_rsa (/Users/localname/.ssh/id_rsa)
        localname@local:~$ ssh-add -l
        2048 SHA256:sc+GdfQhQqICl5WjRKsyIjxliJuwy3u23iXpnsCRuQM penguin@mylaptop (RSA)
        ```

    *   Make sure the `SHA256` fingerprint is identical to what is shown under **My SSH Keys** on POD portal.                                                                                                                                                                                                               
    
*   We have set up the ssh keys for accessing MT3 compute node. Try the following command:

    *   ```bash
        localname@local:~$ ssh username@104.224.20.197
        ```

        *   Replace the `username` above with your POD ***username*** created in Section 1 and also listed on your POD portal.
        *   Replace the node IP address with your MT3 node IP if necessary.
        *   If it is the first time to connect, you may need to type **yes** and press ENTER for the popped-up question to allow your local machine to remember the POD host.

*   If no error message occurs, congratulations! We have successfully connected to the node we will work on. Next, we will set up Ubuntu environment with singularity.


------------------------------------------------------------------------



## Steps on Set Up Ubuntu Container with Singularity

This section introduces how to use [singularity container](https://docs.sylabs.io/guides/3.5/user-guide/introduction.html) for secure Ubuntu environment setup. The well-configured container will be later used in compute node for research work. Several options to set up environment containing DL framework with ROCm compatibility will be introduced.

### 1. What is and Why Singularity?

Singularity is a *container* platform. It allows you to create and run containers that package up pieces of software in a way that is portable and reproducible. You can build a container using Singularity on your laptop, and then run it on many of the largest HPC clusters in the world.

The POD cluster is shared across multiple users and thus cannot grant each user the root privilege. But the root privilege i.e. `sudo` is required for package installation and other administrative tasks. So we need to create a singularity (ubuntu/linux) container in the server that provides sudo access permissions. Additionally, since many people are working on the same cluster, we may want to manage and maintain our own environment that will not interfere with others’.

**Note**: The latest version of AMD ROCm (v5.3) **no longer supports Ubuntu 18.04**. Please refrain from setting up Ubuntu 18.04 container with singularity. For <u>List of Supported and Unsupported Operating Systems</u>, please check [this link](https://docs.amd.com/bundle/ROCm-Release-Notes-v5.3/page/About_This_Document.html#d2e25) from ROCm official documentation.



### 2. Ubuntu + DL Framework + ROCm Container Setup with Singularity

This section will offer two options for using singularity to build a container with Ubuntu 20.04.4, PyTorch 1.13.0 with ROCm support. If you are seeking for building containers with other DL framework like Tensorflow, please check [ROCm-DL-Installation](https://docs.amd.com/bundle/ROCm-Deep-Learning-Guide-v5.3/page/Frameworks_Installation.html). That page will give you an idea how to adjust the following steps for Tensorflow installation, and the adjustments should be simple and straightforward.

#### Option 1 (Short Solution): Use Docker Image with PyTorch Pre-installed

This option will provide a painless setup procedure to enable almost all packages that we need, including Ubuntu 20.04.4 LTS, Python 3.7.13, conda 22.9.0, PyTorch 1.13.0, ROCm 5.3.0, etc. But please try the Option 2 as well because that will give us flexibility in case we need to configure our environment with a customized list of desired packages.

*   Connect to POD MT3 node.

    *   ```bash
        localname@local:~$ ssh username@104.224.20.197
        ```

        *   Replace the `username` above with your POD ***username*** created in Section 1 and also listed on your POD portal.
        *   Replace the node IP address with your MT3 node IP if necessary

*   POD MT3 node has multiple pre-built modules, we can check the available modules by the following command.

    *   ```bash
        username@pod[node]:~$ module avai
        ```

*   We see that `singularity` is available in the node. We will use the following command to load `singularuty` for our usage.

    *   ```bash
        username@pod[node]:~$ module load singularity
        ```

*   ROCm releases the docker image with PyTorch pre-intalled and other packages that we need. Directly pulling the latest docker image into our MT3 node by `singularity` will tremendously ease our setup procedure. To retrieve a remote container, use the `pull` command. We name the pulled docker containers (converted to SIF format by `singularity`) as `rocm-torch.sif`.

    *   ```bash
        username@pod[node]:~$ mkdir containers
        username@pod[node]:~$ cd containers
        username@pod[node]:~/containers$ singularity pull rocm-torch.sif docker://rocm/pytorch:latest
        # If working with Tensorflow, singularity pull rocm-tensorflow.sif docker://rocm/tensorflow:latest
        ```

*   That’s it! Because the docker image released by ROCm contains all we need, so we can skip the steps to build our own container from scratch. Now, let’s test our `rocm-torch.sif` container by `exec` and `shell` command. You may also come across `run` command, an alternative to `exec`. They do the same job.

    *   ```bash
        username@pod[node]:~/containers$ singularity exec rocm-torch.sif echo “Hello, world”
        “Hello, world”
        username@pod[node]:~/containers$ singularity shell rocm-torch.sif
        singularity> python3 -V
        Python 3.7.13
        singularity> echo “Hello, world”
        “Hello, world”
        ```

        

*   However, this option does not offer much flexibility to install custom packages in the container.  The next option gives a way to customize the container when needed. Option 2 is not recommended compared to Option 1 due to its complexity, but it provides a detailed instruction on how to build nearly everything in an image from scratch with singularity and how POD cluster can utilize the customized image.



#### Option 2 (not recommend but good to study): Using ROCm Base Docker Image and Build PyTorch From Source

This option is considered to be more flexible than Option 1. However, the setup procedure will be much more complex than Option 1. The workflow will be installing singularity on local machine, define our own container recipe, build container image based on the recipe by singularity and transfer the built container image from local to POD cluster for later use. **Note**: (Oct 2022) PyTorch + ROCm package from `pip` wheel has embedded miopen kernel compilation issues, so we have only to build PyTorch from source, which is a big inconvienience. FYI, check [that  issue](https://github.com/ROCmSoftwarePlatform/MIOpen/issues/1709#issuecomment-1220033318).

*   `singularity`  can be installed on Linux machines (Ubuntu or CentOS). If your local machine is running a different OS, please try to set up linux environment or get a Linux instance from any cloud computing service, like AWS EC2. For general purpose, the steps below are running on an AWS EC2 instance.

*   AWS EC2 Setup (skip this step if it is unnecessary)

    *   Select EC2 –> Go to EC2 Dashboard –> Click on `Launch instance` button -> Launch instance

    *   Type in `ubuntu` as **Name** –> Select Ubuntu under **Quick Start** –> Select `t2.small` as **Instance type**

    *   `Create new key pair` if you don’t have one, a key file called *ubuntu.pem* will be downloaded to your local machine.

    *   Change 10 GiB to `50 GiB` for **Configure storage**. (Leave suffcient free space. The required storage may vary based on the packages to be installed)

    *   Keep everything else as default. Click on `Launch instance` button on the right.

    *   Wait for several minutes until the instance is `running`.

    *   On your local machine, go to the directory that *ubuntu.pem* is stored.

    *   ```bash
        localname@local:~/Downloads$ chmod 400 ubuntu.pem
        localname@local:~/Downloads$ ssh -i "ubuntu.pem" ubuntu@ec2-{Public IP}.compute-1.amazonaws.com
        ubuntu@ip-{Private IP}:~$
        ```

*   Please make sure your instance has at least 2GB RAM and at least 20GB free disk space (depend on the size of your desired packages). Limited RAM may cause out-of-memory error and you will see the instance freezes.

*   When creating the instance, please make sure you hold the ssh key (.pem from AWS) on the local machine to access the instance.

*   Open **Terminal** on your Linux machine / Connect to your instance. Copy the specific git repo to your machine. This repo contains files for singularity installation and a recipe for our desired container.

*   Run the following commands to install singularity on your Linux instance. For CentOS users, please chmod and execute `singularity_install_centos.sh`. During Installation, if a window pops up and ask you to update some stuff, press ENTER. This actually will not affect the installation.

*   ```bash
    ubuntu@ip-{Private IP}:~$ git clone https://github.com/jxm6165/singularity-torch-rocm.git
    ubuntu@ip-{Private IP}:~$ cd singularity-torch-rocm
    ubuntu@ip-{Private IP}:~/singularity-torch-rocm$ ls
    pod-torch-rocm.def  singularity_install_centos.sh  singularity_install_ubuntu.sh
    # Run singularity_install_centos.sh if CentOS
    ubuntu@ip-{Private IP}:~/singularity-torch-rocm$ chmod 777 singularity_install_ubuntu.sh
    ubuntu@ip-{Private IP}:~/singularity-torch-rocm$ ./singularity_install_ubuntu.sh
    ......
     DONE
    3.6.3
    ubuntu@ip-{Private IP}:~/singularity-torch-rocm$ cd ~ && which singularity 
    /usr/local/bin/singularity
    ```

*   We have successfully install `singularity`. Next we will build our docker by the recipe `pod-torch-rocm.def`.

*   ```bash
    ubuntu@ip-{Instance IP}:~/singularity-torch-rocm$ cat pod-torch-rocm.def
    Bootstrap: docker
    From: rocm/pytorch:latest-base
    
    %environment
    export PATH=/usr/local/sbin:/usr/local/bin:$PATH
    export LD_LIBRARY_PATH=/usr/local/lib64:/usr/local/lib:$LD_LIBRARY_PATH
    export LC_ALL=C
    export CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"}
    
    %labels
    AUTHOR Jianyu Mao
    
    %post
        BUILD_DIR=/tmp/build    # temp build folder inside the container
        ulimit -n 10240
        
        # apt install packages you need
        apt-get -y update
        apt-get -y install wget git bzip2 libjpeg-dev python3-dev
    
        # build environment
        mkdir -p $BUILD_DIR && cd $BUILD_DIR
        
        # miniconda install if necessary. The base docker has anaconda pre-installed already.
        #wget -c https://repo.anaconda.com/miniconda/Miniconda3-py37_4.12.0-Linux-x86_64.sh
        #bash Miniconda3-py37_4.12.0-Linux-x86_64.sh -b -f -p /usr/local
    
        # conda
        conda config --file /.condarc --add channels defaults
        conda config --file /.condarc --add channels conda-forge
        conda update --yes conda
    
        # conda install packages you need, tqdm here as an example
        conda install --yes -c conda-forge tqdm
        
        # pip install packages you need
        pip3 install --upgrade pip
    
        # build PyTorch from source
        conda install astunparse numpy ninja pyyaml setuptools cmake cffi typing_extensions future six requests dataclasses mkl mkl-include
        cd ~
        git clone --recursive https://github.com/pytorch/pytorch
        cd pytorch
        git submodule sync
        git submodule update --init --recursive
    
        python3 tools/amd_build/build_amd.py
        USE_ROCM=1 python3 setup.py install
    
        # cleanup
        cd /
        conda clean --index-cache --tarballs --packages --yes
        apt-get -y clean
        pip3 cache purge
        rm -rf $BUILD_DIR
    
    # vim: syntax=sh:ts=4:sw=4:expandtab
    ```

*   In this recipe, we will build our base environment supporting ROCm by pulling PyTorch base docker image from rocm. We could freely install more packages by `apt install`, `conda` and `pip3`. 

*   Run the following command to build container image defined in the recipe. It may take a **very long** period to complete.

*   ```bash
    # build the recipe (.def) into a sandbox folder
    ubuntu@ip-{Private IP}:~$ sudo singularity build --sandbox pod-torch-rocm /singularity-torch-rocm/pod-torch-rocm.def 
    INFO:    Starting build...
    ......
    INFO:    Build complete: pod-torch-rocm
    # convert the sandbox into a read-only, squashfs image
    ubuntu@ip-{Private IP}:~$ sudo singularity build pod-torch-rocm.img pod-torch-rocm
    INFO:    Starting build...
    ```

*   We have successfully build our container image *pod-torch-rocm.img*. This image is supposed to be stored on MT3 as well. The only stuff left is to transfer the container image *pod-torch-rocm.img* to our POD cluster.

*   ```bash
    # If the image is built on Cloud, first download it to local machine, open Terminal on LOCAL MACHINE and go to the directory where .pem file locates
    # If the image is built on local machine, the following command line is unnecessary
    localname@local:~/Downloads$ scp -i "ubuntu.pem" ubuntu@ec2-{Public IP}.compute-1.amazonaws.com:~/singularity-torch-rocm/pod-torch-rocm.img ~/Downloads/
    
    # Transfer the image to POD MT3 instance
    localname@local:~/Downloads$ scp -C pod-torch-rocm.img username@104.224.20.197:~/containers/
    ```

*   Connect to POD MT3 node. We can find *pod-torch-rocm.img* under *~/containers/* folder. Feel free to do some basic tests with `singularity` commands `exec` and `shell` as in Option 1.

    

#### More Options and More Deep Leanring Frameworks

For more options to build PyTorch and/or other Deep Learning Frameworks supporting ROCm, please check  [ROCm-DL-Installation](https://docs.amd.com/bundle/ROCm-Deep-Learning-Guide-v5.3/page/Frameworks_Installation.html). 

**Note**: (Oct 2022) Please do **not** intall PyTorch using wheels package (Option 2 in [ROCm-DL-Installation](https://docs.amd.com/bundle/ROCm-Deep-Learning-Guide-v5.3/page/Frameworks_Installation.html).), i.e. `pip3 install --pre torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/nightly/rocm5.2/`. Currently, wheels package provided by PyTorch has a building issue with ROCM architecture (*MIOpen*(*HIP): Warning* [*SQLiteBase*] Unable to read *system database*). This issue may be resolved in the future versions of PyTorch wheels packages.



----------------------------------------------------------


## SLURM and Train with AMD GPU

This section will introduce how we will run PyTorch tasks with utilization of AMD GPU ROCm on POD clusters. We will use [SLURM](https://slurm.schedmd.com/documentation.html) to manage our jobs (in our case, run Python scripts) on HPC computing platforms. For our jobs, we will run or execute our tasks under the container environment obtained by `singularity` from the last section.



### 1. SLURM

On POD clusters, users run programs by submitting commands or job script to the [Slurm](https://www.schedmd.com/) job scheduler. 

#### Some useful SLURM commands.

*   #### To Submit a Job

    | Command | Description                                            |
    | ------- | ------------------------------------------------------ |
    | sbatch  | submits your job to the scheduler for batch jobs       |
    | srun    | submits your job to the scheduler for interactive jobs |

*   ####  After a Job is submitted

    | Command                     | Description                                      |
    | --------------------------- | ------------------------------------------------ |
    | squeue -u ${username}       | see your jobs that are running or waiting to run |
    | scontrol show jobid <jobid> | display detailed info about a job                |
    | scancel <jobid>             | cancel a job                                     |

*   #### After a Job is Completed

    | Command      | Description                  |
    | ------------ | ---------------------------- |
    | seff <jobid> | see results of completed job |
    | shistory     | display your job history     |



#### One Line Example

Suppose we have a bash script to be executed (e.g., hello_world.sh), we can submit this job via `sbatch`. Now, the task has been submitted and will be arranged to execute by job scheduler.

```bash
$ sbatch -t 00:01:00 --cpus-per-task=1 --gres=gpu:1 hello_world.sh
```

The commonly used arguments in sbatch and srun are listed below.

| Argument                              | Description                                     |
| ------------------------------------- | ----------------------------------------------- |
| -p, --partition=partition             | partition requested                             |
| -J, --job-name=jobname                | name of job                                     |
| -t, --time=time                       | time limit                                      |
| --gres=rsrc_name[:rsrc_type]:rsrc_num | required generic resources                      |
| -c, --cpus-per-task=ncpus             | number of cpus required per task                |
| -d, --dependency=type:jobid[:time]    | defer job until condition on jobid is satisfied |
| -e, --error=err                       | file for batch script's standard error          |
| -o, --output=out                      | file for batch script's standard output         |
| --mem-per-cpu=MB                      | maximum amount of real memory per allocated     |
| --ntasks-per-node=n                   | number of tasks to invoke on each node          |

In the case we have a lot of arguments to be specified and / or we want to execute several scripts together. It will be good to submit a job with a job script.



#### Job Script Example

Below is a job script named as `job.slurm` for running a Python code.

```bash
#!/bin/bash
#SBATCH --job-name=myjob         # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=1               # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem-per-cpu=2G         # memory per cpu-core (4G is default)
#SBATCH --time=00:01:00          # total run time limit (HH:MM:SS)

module purge
module load python
python my_script.py

```

Then, we submit the job by `sbatch`.

```bash
$ sbatch job.slurm
```



### 2. Train with AMD GPU

`singularity` supports program executaion with GPUs. Specifically, for AMD ROCm, issuing command like `singularity exec --rocm my_container.img python my_script.py`. The following example will combine everything together to test your container image as well as train a simple MNIST model with ROCm acceleration. First, connect to the POD MT3 node.

```bash
localname@local:~$ ssh username@104.224.20.197
```

 Then, run the following commands to download the example from git repo as well as the dataset.

```bash
username@pod[node]:~$ git clone https://github.com/jxm6165/pod-torch-test.git
username@pod[node]:~$ ls pod-torch-test/
download_mnist.py  mnist_classify.py  torch_test.slurm
username@pod[node]:~$ cd pod-torch-test/
username@pod[node]:~/pod-torch-test$ module load singularity
username@pod[node]:~/pod-torch-test$ singularity exec ~/containers/rocm-torch.sif python download_mnist.py
```

Then, view the given job script and submit the job to run. This example will train an imagenet for MNIST classification for 3 epochs. It request 4 CPUs and 1 GPU.

```bash
username@pod[node]:~/pod-torch-test$ cat torch_test.slurm 
#!/bin/bash
#SBATCH --job-name=torch-test    # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=1               # total number of tasks across all nodes
#SBATCH --cpus-per-task=4        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem=4G                 # total memory per node (4 GB per cpu-core is default)
#SBATCH --gres=gpu:1             # number of gpus per node, more or equal number of cpus to fully utilize gpus 
#SBATCH --time=00:05:00          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=begin        # send email when job begins
#SBATCH --mail-type=end          # send email when job ends
#SBATCH --mail-user=<your-id>@andrew.cmu.edu

module purge
module load singularity
singularity exec --rocm $HOME/containers/rocm-torch.sif python3 mnist_classify.py --epochs=3
username@pod[node]:~/pod-torch-test$ sbatch torch_test.slurm
Submitted batch job 559311
username@pod[node]:~/pod-torch-test$ squeue -u $USER
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
            559311       C19 torch-te  jianyum  R       0:39      1 c19n054
```

We can view that the task is running by `squeue` command. The task is assumed to be completed within 1 minute. If it runs more than 1 minute, there is something wrong with your container image, especially ROCm is not working or GPUs are not available.

After the job is completed, an output file `slurm-<job-id>.out` will be stored in the current directory. This is a log file contains outputs from both PyTorch and SLURM. You could how the model is trained in 3 epochs.

-----------------------------------------------------

## The End

That’s all. Now, we can move to start our work on POD with AMD GPUs.
