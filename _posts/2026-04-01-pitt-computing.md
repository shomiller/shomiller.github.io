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
to load the Anaconda package manager. Then, create an "environment" in your working directory on `/ix1`. For instance, to create an environment `ENV-NAME` with `ROOT`, `hepmc3`, and `lhapdf` (all requisites for proton-proton simulations through `Pythia` and `Delphes`), enter:
~~~
conda create --prefix=/ix1/shomiller/UserID/envs/ENV-NAME python=3.11
conda config --set channel_priority strict
conda install -c conda-forge root
conda install -c conda-forge hepmc3
conda install -c conda-forge lhapdf
~~~
This will take a few minutes to install, and you might get some prompts asking if you really want to install all these packages. 

Once this is done, you can activate the environment by running
~~~
source activate /ix1/shomiller/UserID/envs/ENV-NAME
~~~
You should see `(ENV-NAME)` at the beginning of your input lines in the shell now, indicating that the environment has been loaded successfully.

With that environment loaded, we can install other packages using those dependencies manually. For example, Pythia8 can be installed by copying the `.tar.gz` file from `/ix1/shomiller/sdh93/source/pythia8312.tar.gz`, unpacking it in your own directory, and then compiling it:
~~~
cp /ix1/shomiller/sdh93/source/pythia8312.tar.gz /ix1/shomiller/UserID/DESTINATION/
cd /ix1/shomiller/UserID/DESTINATION
tar -xzvf pythia8312.tar.gz
cd pythia8312.tar.gz
./configure --prefix=/ix1/shomiller/UserID/DESTINATION/pythia8-install --with-hepmc3 --with-lhapdf6 --with-python --with-gzip
make
make install
~~~
This will build pythia in the `DESTINATION` directory, with the configurations set to find the dependencies automatically. The last command installs pythia to your path so that it can be executed from anywhere, as you can check by running
~~~
pythia8-config
~~~

The main programs that serve as both templates and examples can all be found in `pythia8-install/share/Pythia8/examples/`, and can be compiled and run separately (but don't run them on a login node). 

Next we can install Delphes:
~~~
cp /ix1/shomiller/sdh93/source/Delphes-3.5.0.tar.gz /ix1/shomiller/UserID/DESTINATION/
cd /ix1/shomiller/UserID/DESTINATION
tar -xzvf Delphes-3.5.0.tar.gz
cd Delphes-3.5.0/
./configure
make
~~~

Later we can try to interface these with MadGraph, or just run them step-by-step individually. 

### Running Jobs with SLURM:

(To Do)