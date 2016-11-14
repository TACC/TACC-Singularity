Singularity @ TACC
==================

Singularity enables users of shared systems, such as HPC clusters, to take advantage of the portability afforded by containers. From the [project web site](http://singularity.lbl.gov/):

> Singularity enables users to have full control of their environment. This means that a non-privileged user can “swap out” the operating system on the host for one they control. So if the host system is running RHEL6 but your application runs in Ubuntu, you can create an Ubuntu image, install your applications into that image, copy the image to another host, and run your application on that host in it’s native Ubuntu environment!

TACC currently supports Singularity on its [Stampede](https://portal.tacc.utexas.edu/user-guides/stampede) cluster. Support for other TACC clusters is planned in the coming months. In addition, Docker and other container ecosystem applicaitons can be run natively on the joint IU-TACC [Jetstream cloud system](http://www.jetstream-cloud.org/). We will continue to enhance and refine our support for Singularity by packaging additional utilities with it on our systems and by providing tutorials via this Github repository.

Key Issues:
-----------

* The current installed version of Singularity on Stampede is *2.1*
* Environment variables set outside of the container will be active inside it unless the container specifically overrides them. This might be particularly confusing if you are using Python from within the container, but have `PYTHONPATH` set to incompatible libraries. One easy solution is to unset `PYTHONPATH` before running a container.
* Singularity containers are almost completely read-only. You can only write to: $HOME, /work and /scratch which correspond to filesystems on the Stampede host. One handy consequence of environment variables being inherited from the host is that `$WORK` and `$SCRATCH` resolve to your actual directories inside a Singularity container.
* Each container intended to work on Stampede needs to include `/home1` `/work` and `/scratch` folders which will serve as mount points.
* There is an easy way to convert (nearly) any Docker container to Singularity format using [docker2singularity](https://github.com/singularityware/docker2singularity) This procedure will create all of the necessary mountpoints for Stampede and other systems. The process is documented in a tutorial below.

Tutorials
---------

* [From Docker to Singularity](docs/from-docker-to-singularity.md)

References
----------
* [Singularity](http://singularity.lbl.gov/)
* [Stampede User Guide](https://portal.tacc.utexas.edu/user-guides/stampede)
* [TACC User Portal](https://portal.tacc.utexas.edu/)
* [Jetstream Cloud](http://www.jetstream-cloud.org/)
* [Open Container Project](https://runc.io/)
* [Docker](https://www.docker.com/)
* [Rkt](https://coreos.com/rkt/)
* [Docker Hub](https://hub.docker.com/)
* [Quay.io](https://quay.io/)

`Last Updated: Nov 13, 2016`
