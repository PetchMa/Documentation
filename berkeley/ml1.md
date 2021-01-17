# Machine Learning Training Environments 🧵

### Peter Ma | Jan 16 2021

These compute nodes need you to wrap environments around them for you to train your models with the proper metrics and libraries etc. 

This notebook will be dedicated on documenting these important commands. 

These are some commands that are needed for reference

```python
singularity run --nv -B /mnt_blpd7/ peterma-ml2
```

The code is stored here and you should CD into it

```python
cd /home/pma/peterma-ml2/BL-Reservoir/development_env/freq_thread
```

Thread through one part of the spectrum and give a bunch of files, and the depth at which is threads through. [freq] [directory] [depth]

```python
python3 execute_multicore.py 1575 ../../../../../../mnt_blpd7/datax/dl/ 4000 
```

We can run feature computing ON BULK

```python
python3 dev_bulk_feature.py ../../../../../../mnt_blpd7/datax/dl/ 0 400
```

Computing a single file for features

```python
python3 feature.py ../../../../../../mnt_blpd7/datax/dl/GBT_57571_67683_HIP34024_fine.h5
```

we can compute the cluster and find the candidates

```python
python3 cluster.py ~/freq_thread
```