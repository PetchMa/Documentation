# `Singularity` Containers 📦

This note page will be dedicated on how to properly use singularity containers. 

In this quick tutorial I will go from a single `dockerfile`  convert it to a operational singularity container running on HPCs specifically for energy detection.

[<<< BACK](directory.html) 

## Build Docker Container

Lets start with a single `dockerfile` that we will use for energy detection. You can find one for example to use here:

```bash
git clone --single-branch --branch peter-dev [<https://github.com/UCBerkeleySETI/BL-Reservoir.git>](<https://github.com/UCBerkeleySETI/BL-Reservoir.git>)
```

You can CD into the directory to find the image:

```bash
cd BL-Reservoir/temp_meerKAT_analysis
```

You should find a `dockerfile`. Now you can build the `dockerfile` for a given container name.

```bash
sudo docker build -t peterma-dev1 .
```

After you've built this image you should check for the images you've got and see its actually built.

- Other commands for Docker

  `sudo docker rmi --force 8914c41be09b`

  `sudo docker container run -it peterma-dev1 /bin/bash`

```bash
sudo docker images
```

Now we can create a tarball version of this this lets the singularity container read this! Remember to put the correct container id and name you want to give it.

```bash
sudo docker save -o peterma-dev1.tar peterma-dev1
```

We're then going to have to login into a google sdk and upload our image to a google bucket or somewhere you can access! We will be porting this image to the HPC and building the singularity container there.

```bash
sudo gsutil cp peterma-dev1.tar  gs://deepseti/docker_img/peterma-dev1.tar
```

This will begin an upload process. The image is quite large about 2-3gb in size. This all the docker stuff we need to do!

## Singularity Containers on HPC

login to the compute node

```bash
ssh blpc0
```

We can get our image from our storage bucket [this is a public image] if its not yours make sure it is public or else you'd have to set up credentials yourself.

```bash
wget <https://storage.googleapis.com/deepseti/docker_img/peterma-dev_remote.tar>
```

Build your singularity container as such: remember to give it a directory that you have read access to or else it will point to the normal tmp directory which you might not have access to.

```bash
SINGULARITY_TMPDIR=/home/pma/temp singularity build  --sandbox peterma-remote docker-archive://peterma-dev_remote.tar
```

You can now simply run the container as such and get into the command line

```bash
singularity run peterma-remote /bin/bash
# bind mounts
singularity run -B /mnt_blpd11/ peterma-remote
```

You can run a flavour of our MeerKAT data as such by running the following command:

NOTE: This is a test dataset and thus in production time this would be accessing simply within the directory itself.

We first acquire the dataset - [you can use any dataset you wish]

```bash
mkdir -p peterma-remote /BL-Reservoir/temp_meerKAT_analysis/data && wget <http://blpd0.ssl.berkeley.edu/blmeerkat_uk_copy/20200917/m063_guppi_59109_53457_003376_J1644-4559_0001.rawspec.0003.fil> -P  peterma-dev2/BL-Reservoir/temp_meerKAT_analysis/data
```

We can then compute it by shoving it into our singularity container!  First convert it to `.h5`

```bash
singularity run peterma-remote python3 peterma-remote /BL-Reservoir/temp_meerKAT_analysis/fil2h5.py peterma-dev2/BL-Reservoir/temp_meerKAT_analysis/data/m063_guppi_59109_53457_003376_J1644-4559_0001.rawspec.0003.fil &&  python3 peterma-dev2/BL-Reservoir/temp_meerKAT_analysis/energy_detection_fine_0003.py peterma-dev2/BL-Reservoir/temp_meerKAT_analysis/data/meerKAT_data.h5
```

We can then run energy detection on that file.

```bash
singularity run peterma-remote python3 peterma-dev2/BL-Reservoir/temp_meerKAT_analysis/energy_detection_fine_0003.py peterma-dev2/BL-Reservoir/temp_meerKAT_analysis/data/m063_guppi_59109_53457_003376_J1644-4559_0001.rawspec.0003.fil.h5
```

`singularity run peterma-remote python3 peterma-remote/BL-Reservoir/temp_meerKAT_analysis/energy_detection_fine_4k.py peterma-remote/BL-Reservoir/temp_meerKAT_analysis/data/dummy_data.h5`



[<<< BACK](directory.html)