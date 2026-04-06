---
layout: post
title: Computing at Pitt 
#date: 2026-04-01 08:00:00-0400
description: Some notes on using Pitt's computing resources for members of my group
tags: 
categories: resources
---

This page is a set of notes on how to access and use the computing resources at Pitt for members of my group. Lots more information is available from the [Pitt Center for Research Computing and Data (CRC)](https://crc.pitt.edu). See for instance, the page on [Accessing the Cluster](https://crc.pitt.edu/requesting-new-account/accessing-cluster) and links from there. If I haven't already requested an account for you, let me know (I'll need your Pitt ID, of the form "sdh93") and I'll get you one with access to the "shomiller" group resources. 

### Logging In:

~~~
ssh pittID@h2p.crc.pitt.edu
~~~

Then enter your Pitt password (the same one you use for all SSO logins). 

This will sign you into a "login" node for the CRC resources. 

(To Add: storage space on `/ix1/shomiller`, checking usage and permissions, etc.)

### Setting up a Coding Environment: 

To run relevant software for High-Energy physics simulations, etc., we'll need to set up an environment with all the relevant packages install. Lots of software can be accessed immediately using the "Lmod Environment Modules tool" (see (https://crc-pages.pitt.edu/user-manual/getting-started/step3/getting-started-step3-software/)). We'll use Anaconda to create environments with other HEP-specific software loaded from conda-forge. You can run
~~~
module load python/ondemand-jupyter-python3.11
~~~~
to load the Anaconda package manager. Then, create an "environment" in your working directory on `/ix1`. For instance, to create an environment `ENV-NAME`, enter:
~~~
conda create --prefix=/ix1/shomiller/UserID/envs/ENV-NAME python=3.11
~~~
Once it's created, we can install various packages in this environment using conda force. First activate it by running 
~~~
source activate /ix1/shomiller/UserID/envs/ENV-NAME
~~~
You should see `(ENV-NAME)` at the beginning of your input lines in the shell now, indicating that the environment has been loaded successfully. Then install `ROOT`, `hepmc2`, `hepmc3`, and `lhapdf` (all requisites for proton-proton simulations through `Pythia` and `Delphes`):
~~~
conda config --set channel_priority strict
conda install -c conda-forge root
conda install -c conda-forge hepmc2
conda install -c conda-forge hepmc3
conda install -c conda-forge lhapdf
~~~
This will take a few minutes to install, and you might get some prompts asking if you really want to install all these packages. 

With that environment loaded, we can install other packages using those dependencies manually. For example, Pythia8 can be installed by copying the `.tar.gz` file from `/ix1/shomiller/sdh93/source/pythia8312.tar.gz`, unpacking it in your own directory, and then compiling it:
~~~
cp /ix1/shomiller/sdh93/source/pythia8312.tar.gz /ix1/shomiller/UserID/DESTINATION/
cd /ix1/shomiller/UserID/DESTINATION
tar -xzvf pythia8312.tar.gz
cd pythia8312.tar.gz
./configure --prefix=/ix1/shomiller/UserID/DESTINATION/pythia8-install \
--with-hepmc2 --with-hepmc3 --with-lhapdf6 --with-python-config=python3-config --with-gzip
make
make install
~~~
Note that we have to specify the python-config file manually, as Pythia's configuration script calls `python-config`, but this points to a system python version, rather than the conda environment, which is found by `python3-config`. 
This will build pythia in the `DESTINATION` directory, with the configurations set to find the dependencies automatically. The last command installs pythia to your path so that it can be executed from anywhere, as you can check by running
~~~
pythia8-config
~~~

The main programs that serve as both templates and examples can all be found in `pythia8-install/share/Pythia8/examples/`, and can be compiled and run separately (but don't run them on a login node). 

We can similarly install FastJet and Delphes:

~~~
cp /ix1/shomiller/sdh93/source/fastjet-3.5.1.tar /ix1/shomiller/UserID/DESTINATION/
cd /ix1/shomiller/UserID/DESTINATION
tar -xvf fastjet-3.5.1.tar
cd fastjet-3.5.1/
./configure --prefix=/ix1/shomiller/UserID/DESTINATION/fastjet-install --enable-pyext
make
make check
make install
~~~

~~~
cp /ix1/shomiller/sdh93/source/Delphes-3.5.0.tar.gz /ix1/shomiller/UserID/DESTINATION/
cd /ix1/shomiller/UserID/DESTINATION
tar -xzvf Delphes-3.5.0.tar.gz
cd Delphes-3.5.0/
./configure
make
~~~

To check the FastJet installation, compile and run the `short-example.cc` script on FastJet's website: (https://fastjet.fr/quickstart.html). 
Later we can try to interface Pythia8 and Delphes with MadGraph, or just run them step-by-step individually. 

### Running Jobs with SLURM:

Aside from installing and managing files, other scripts should not be run from the login nodes, to prevent disrupting access to the cluster for everyone at Pitt. We can instead use the SLURM workload manager to run resource-intensive commands either interactively (i.e., the same way you run things from a terminal on your own machine), or via the batch system. 

##### Interactive Jobs:

For interactive jobs, we follow the instructions here: (https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/). 

The main command is:
~~~
srun -M smp -n1 -t02:00:00 --pty bash
~~~
This will allocate 2 hours of wall-clock time (`-t02:00:00`), with the terminal mode (`--pty bash`), with one core (`-n1`) on an SMP machine (`-M smp`). Note that if you still have code running when the time allocated ends, it will be stopped, so make sure you allocate enough time for what you want to do, but try not to overuse it or we'll run out of our computing allocation from Pitt. 

Once you execute this command, you'll see that your shell will no longer say `[UserID@login0 ...]`, but that you're signed in on another machine. Here you can feel free to execute commands just like any other terminal window. Note that you will likely have to load any modules and conda environments again, as you did on the login node, to run your scripts.

(To Do: Add Batch Jobs)