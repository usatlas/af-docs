# Ways to Implement ML Containers

The ML containers were developed to be used both at the command line and with
each of the AF's jupyter interfaces.

## Command Line ML Container Usage

The images are deployed onto the Docker hub and CVMFS and there are 4 ways to
run the ML images at the command line.

=== "Apptainer/Singularity"

    You can directly use **Apptainer** or **Singularity** to develop with the containers using the `apptainer run` or `singularity run` command.

    ``` output
    lxplus901% apptainer run /cvmfs/unpacked.cern.ch/registry.hub.docker.com/yesw2000/ml-base:alma9-python39
    For the content in this container,
      please read the file /list-of-pkgs-inside.txt

    To create your own new env, run "source /create-newEnv-on-base.sh -h" for help
    Singularity> python --version
    Python 3.9.19
    Singularity>
    ```

=== "Virtual Environment"

    We have included a bash script with every container that will create a virtual environment based on that container.

    ``` output
    lxplus901% source /cvmfs/unpacked.cern.ch/registry.hub.docker.com/yesw2000/ml-base:alma9-python39/setupMe-on-host.sh
    To create your own new env, run "source $EnvTopDir/create-newEnv-on-base.sh -h" for help
    (base) lxplus901% python --version
    Python 3.9.19
    (base) lxplus901%
    ```

=== "Docker or podman"

    Since the containers are also deployed to Docker hub, you can use `docker` or `podman` to develop with them.

    ``` output
    lxplus901% podman run -it docker.io/yesw2000/ml-base:alma9-python39
    [...]
    For the content in this container,
    please read the file /list-of-pkgs-inside.txt

    To install new pkg(s), run "micromamba install pkg1 [pkg2 ...]"
    (base) bash-5.1# python --version
    Python 3.9.19
    (base) bash-5.1#
    ```

=== "Script for laptops"

    [A script](https://raw.githubusercontent.com/usatlas/ML-Containers/main/run-ml_container.sh) is available to run the ML container on laptops.

    ``` output
    lxplus901% wget https://raw.githubusercontent.com/usatlas/ML-Containers/main/run-ml_container.sh
    lxplus901% source run-ml_container.sh ml-base:alma9-python39
    Found the image name= ml-base:alma9-python39  with the dockerPath= docker.io/yesw2000/ml-base:alma9-python39
    Trying to pull docker.io/yesw2000/ml-base:alma9-python39...
    [...]
    To reuse the same container next time, just run

         source runML-here.sh
    or
         source run-ml_container.sh

    podman exec -it yesw_ml-base_alma9-python39 /bin/bash

    root@3c90a02d92d6:[1]%
    ```

## Jupyter Interface at AFs

=== "BNL"

    Use the following steps to implement the ML container through the BNL jupyter interface:

    1. Open the BNL Jupyter Interface and create a new notebook
    2. For **Select JupyterLab Environment** select *Default*
    3. Select *Custom* in the **Singularity Container** question and input the `/cvmfs` address for ML container
    4. Press Start!

    After pressing start, the server for the notebook will begin the setup process. After a minute or so, your jupyter notebook will appear and you will have a number of kernels available to use. The kernel associated with the container is called `ML-Python3`.

=== "UChicago"

    Currently the UChicago JupyterLab servers don't allow custom containers when creating a new notebook. This is planning on being updated soon. In the meantime, the `ml-platform-cpu:latest` or `ml-platform-gpu:latest` containers shown in the `Image` dropdown box does have all packages necessary for running the ML Tutorial material.

=== "SLAC"

    Steps for using ML containers using SLAC Jupyter interface:

    1. Connect to SLAC's [S3DF Jupyter Portal](https://s3df.slac.stanford.edu/pun/sys/dashboard/batch_connect/sys/slac-ood-jupyter/session_contexts/new)
    2. After you login, you'll come to the menu where you can define your jupyter server. The first dropdown is the **Jupyter Instance**. Choose the "Custom Singularity Container" option.
    3. Now the **Commands to initiate Jupyter** box will be editable. Replace the content of the box by the following:

        ```
        export APPTAINER_IMAGE_PATH=/cvmfs/unpacked.cern.ch/registry.hub.docker.com/yesw2000/ml-base:alma9-python39
        function jupyter() { apptainer exec --nv -B /cvmfs,/sdf,/fs,/sdf/scratch,/lscratch ${APPTAINER_IMAGE_PATH} jupyter $@; }
        ```

    4. You may need to check the **Use Jupyter Lab instead of Jupyter Notebook** box (in case only Jupyter Lab or only Jupyter Notebook is available in the container)
    5. Make sure the two pull down menu under **Run on cluster type** choose `Batch` and `s3df`.
    6. In **Account** box, type in `atlas:usatlas`
    7. In **Partition** choose `ampere` if you want GPUs, or choose `rome` if you don't need a GPU.
    8. Fill the **Number of GPUs** box if you are asking for GPUs.
    9. Your server will be submitted to the queue and will take a minute or so to generate. You will see the **Connect to Jupyter** button when your server has initialized successfully.
