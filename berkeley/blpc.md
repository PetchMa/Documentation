#  `BLPC0` Computing Notes 

### Peter Ma | Jan 16 2021

Here are the much needed compute node notes that helps me work with these powerful machines. Absolutely necessary for all the work that I do. Mostly used for managing the processes making sure I don't loose all my work. 



## Login to Computers VIA SSH Tunnel

Here is the steps to log into the computers. 

```{shell}
#Ubuntu login 
ssh pma@digilab.astro.berkeley.edu

ssh pma@blph0.ssl.berkeley.edu

ssh blpc0
```



## Basic Python

To start a python environment. 

```{shell}
source /opt/conda/init.sh
```

 If more sophisticated python environments are needed I suggest you check out `singularity` containers for this need [here](singularity.html). In short this is what it effectively does: More in depth details later. 

```{shell}
singularity run peterma-ml3
```









<div style="text-align:right">Made with Typora © 2021 Peter Ma </div>