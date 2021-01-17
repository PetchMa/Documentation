#  `BLPC0` Computing Notes

### Peter Ma | Jan 16 2021

Here are the much needed compute node notes that helps me work with these powerful machines. Absolutely necessary for all the work that I do. Mostly used for managing the processes making sure I don't loose all my work. 



## Login to Computers VIA SSH Tunnel 🚄

Here is the steps to log into the computers. 

```{shell}
#Ubuntu login 
ssh pma@digilab.astro.berkeley.edu

ssh pma@blph0.ssl.berkeley.edu

ssh blpc0
```



## Basic Python 🐍

To start a python environment. 

```{shell}
source /opt/conda/init.sh
```

 If more sophisticated python environments are needed I suggest you check out `singularity` containers for this need [here](singularity.html). In short this is what it effectively does: More in depth details later. 

```{shell}
singularity run peterma-ml3
```



## Screens Linux 💻

Good way of managing multiple terminal screens without the being flushed out every 30 minutes from connection loss. Here are the important commands. 

``` {shell}
screen -S <name>

# Detach from screen
ctrl+a +d 

# kill off screen
ctrl +a +k +y

# list screen 
screen -ls

# reattach screen
screen -r
```

Sometimes the screen might not get killed and you might want to try and flush everything out. 

```{shell}
screen -ls | grep '(Detached)' | awk '{print $1}' | xargs -I % -t screen -X -S % quit
```

More documentation on the actual website: [here](https://linuxize.com/post/how-to-use-linux-screen/)

##  Copy Files 📁

Sometimes you might need to find the actual file and use on your own computer. Here is the commands to do so.

```{shell}
scp pma@blpc0: <directory on remote> <directory on your computer>
```

If you are using a windows subsystem and you might have trouble finding the directory. It is located here. 

```{code}
C:\Users\peter\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState\rootfs\home\peter
```



## Jupyter Notebooks 🪐

Sometimes you might need to open up a computer with the given port for jupyter notebooks. Thats easy. On each computer run the following commands:

#### Remote Machine

Note down the port it opens

```{code}
jupyter notebook
```

#### Local Machine

Tunnel to the port. 

```code
ssh -NL 8888:localhost:8888 pma@blpc0
```

The format is the following 

```code 
ssh -NL <port on remote>:localhost:<port on local> pma@<computer name>

peternotebook
```

Note you will NEED multi-hop set up for `ssh` into these notebooks. To set that up check the following documentation 

Next you just need to open up that port on a machine `http://localhost:8888` 

## MeerKAT Development Nodes 🧭

Connecting to this compute node a bit different. 

```{shell}
ssh pma@digilab.astro.berkeley.edu

ssh blh0.digilab.pvt

ssh blc03.bl.pvt
```



## Resource Checkup

Sometimes we need to check up on resources and usage. Typically if we had route access we'd simply run `htop` but in most cases we just use the following commands 

```shell
#toggle 1 for cpu cores
top 

# GPU CARDS
nvidia-smi

```







<div style="text-align:right">Made with Typora © 2021 Peter Ma </div>