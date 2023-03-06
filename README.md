# Fishyscapes SIMG Example

This is an example of creating an [Apptainer / Singularity](https://apptainer.org/) image for [Fishyscapes](https://fishyscapes.com/) [submission](https://fishyscapes.com/submission). We suggest building a Docker image first and then converting the Docker image to an Apptainer image.

This example is based on the [synboost](https://github.com/giandbt/synboost) method. You do not need to look into `demo.py` and `requirements.txt` as they are specific to synboost.

## Install Docker and Apptainer

You can follow this [link](https://docs.docker.com/engine/install/) for Docker and this [link](https://apptainer.org/docs/admin/main/installation.html) for Apptainer.

If you need GPU to build your Docker or Apptainer image, please also install [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html), and edit or create the `/etc/docker/daemon.json` following [these instructions](https://stackoverflow.com/a/61737404).

## Build your docker image using Dockerfile

E.g., `docker build -t happygod/synboost:4.0 .`.

Make sure to follow the input and output requirements of the Fishyscapes benchmark.

The Docker image will be converted to an Apptainer image. Please note the following differences:

* Apptainer containers are read-only. Do not create temporary or cache files outside the mounted output folder during runtime. If you have to use these files (e.g., torchvision's pretrained weight), please create them during the building process. 

* We will run Apptainer under a non-privileged user in the container. Docker runs under the root user by default in the container, which may cause permission issues in Apptainer. Please make sure non-privileged users have read access to all your files.

* There will be no Internet connection in our evaluation environment.

(Optional) You may test your Docker image by executing `docker run --rm --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -v /absolute/path/to/input/folder:/input -v /absolute/path/to/output/folder:/output happygod/synboost:4.0`.

## Build your Apptainer image using Apptainer Definition file

E.g., `singularity build synboost_4.0.simg singularity_synboost.def`.

Note that your working directory specified using `WORKDIR` command in `Dockerfile` is not preserved in the Apptainer image. You need to specify the path in the [Apptainer Definition file](https://apptainer.org/docs/user/latest/definition_files.html).

(Optional) You may test your Apptainer image by executing `singularity run --nv --bind /absolute/path/to/input/folder:/input,/absolute/path/to/output/folder:/output synboost_4.0.simg`.
