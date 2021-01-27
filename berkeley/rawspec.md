# RAWSPEC Commands 📀

### Peter Ma | Jan 27  2021

This notebook documents some of the main commands needed to RAWSPEC a basic raw file. This can be improved. Currently a work in progress. 

[<<< BACK](directory.html)



### Basic RAWSPEC Commands

First we want to generate a single spectra from a series of files. Here is a good example.... 

```{shell}
time rawspec -f <freq size> -t <time size> <filename> -d <save directory>
```

The data shape in the end will be the `freq`  size you set with the `-f` and <u>the `-t` tags stands for the factor of **down sampling** in the time domain</u>. Note that this might overload the memory of the GPU and thus you might need to split up the work into multiple files over multiple `coarse channels`. We can do so like such: 

Consider if `Total Coarse Channels = 64` we can create files with subsets of each coarse channels like such: We divide into intervals of `16` and each file has `4` `coarse channels`. Note the `-s` tag which stands for the starting point and `-n` which is the length of the interval. 

The relative file structure for data ingestion:

```code
../../../mnt_blpd15/datax/MKtemp/
├── blpn48
│   └── GUPPI
│   	├── guppi_59143_55142_000486_GPS-BIIR-11_0001.0001.raw
│   	├── guppi_59143_55142_000486_GPS-BIIR-11_0001.0002.raw
|		├── guppi_59143_55142_000486_GPS-BIIR-11_0001.0003.raw
|		.	
|		.
|		└── guppi_59143_55142_000486_GPS-BIIR-11_0001.i.fil
├── blpn47
│   └── GUPPI
│   	├── guppi_59143_55142_000486_GPS-BIIR-11_0001.0001.raw
│   	├── guppi_59143_55142_000486_GPS-BIIR-11_0001.0002.raw
|		├── guppi_59143_55142_000486_GPS-BIIR-11_0001.0003.raw
|		.	
|		.
|		└── guppi_59143_55142_000486_GPS-BIIR-11_0001.i.fil
|
.
.
.
├── blpnN
   └── GUPPI
   		├── guppi_59143_55142_000486_GPS-BIIR-11_0001.0001.raw
   		├── guppi_59143_55142_000486_GPS-BIIR-11_0001.0002.raw
		├── guppi_59143_55142_000486_GPS-BIIR-11_0001.0003.raw
		.	
		.
		└── guppi_59143_55142_000486_GPS-BIIR-11_0001.i.fil

```

The script would be written as such a form: 

What the script will do is **for** every `16` intervals of the entire coarse channel it will create a single spectrogram with `4` coarse channels. The rawspec script will try to give it `131072` frequency samples and keeps a factor of down sampling time by factor of 1. The `-n` tag stands for the length or size of each interval of coarse channels. The `-s` tag stands for which coarse channel to start computing. 

```{shell}
#!/bin/bash
for i in {0..16}
do
    mkdir ../../../datax/scratch/pma/guppi_59143_55142_000486_GPS-BIIR-11_0001.$i
    rawspec -f 131072 -t 1 -s $i*4 -n 4  ../../../mnt_blpd15/datax/MKtemp/blpn48/GUPPI/guppi_59143_55142_000486_GPS-BIIR-11_0001 -d /home/pma/Ideas/guppi_59143_55142_000486_GPS-BIIR-11_0001.$i
    
done
```

The resultant output would be of the following form..

```code
../../../datax/scratch/pma/
├── guppi_59143_55142_000486_GPS-BIIR-11_0001.1
|		└── guppi_59143_55142_000486_GPS-BIIR-11_0001.fil
|
├── guppi_59143_55142_000486_GPS-BIIR-11_0001.2
|		└── guppi_59143_55142_000486_GPS-BIIR-11_0001.fil
.
.
├── guppi_59143_55142_000486_GPS-BIIR-11_0001.16
		└── guppi_59143_55142_000486_GPS-BIIR-11_0001.fil
```



## `SPLICE2` Command

The `SPLICE2` command is used to merge files together. Say after you created multiple individual files like shown above ^ we can then merge them with this following command. First we need to add the following:  Just add it to path. 

```
source /usr/local/sigproc/sigproc.sh
```

Then go into the directory with multiple files. And say `N` is the total number of files we can run the following `SPLICE2` command. 

```shell
splice2 -o guppi_59143_55142_000486_GPS-BIIR-11_0001.rawspec.0000.fil guppi_59143_55142_000486_GPS-BIIR-11_0001.{0..N}/guppi_59143_55142_000486_GPS-BIIR-11_0001.rawspec.0000.fil 
```

The structure is the following: 

```code
.root
├── guppi_59143_55142_000486_GPS-BIIR-11_0001.1
│   └── guppi_59143_55142_000486_GPS-BIIR-11_0001.rawspec.0000.fil
├── guppi_59143_55142_000486_GPS-BIIR-11_0001.2
│   └── guppi_59143_55142_000486_GPS-BIIR-11_0001.rawspec.0000.fil
.
.
.
└──  guppi_59143_55142_000486_GPS-BIIR-11_0001.N
	└── guppi_59143_55142_000486_GPS-BIIR-11_0001.rawspec.0000.fil
```

The general format follows the following:

```code
splice2 -o <final file name> <foldername>.{0..N}/<filename> 
```

The folder system looks like the following:

```code
.root
├── foldername.1
│   └── filename.fil
├── foldername.2
│   └── filename.fil
.
.
.
└──  foldername.N
	└── filename.fil
```



## RAWHD -Documentation 

`RAWHD` Is a way of checking the header info on raw files. This is a useful tool that lets you then compute and determine the parameters for running `rawspec` . To get it working, here are the following commands. 

```code 
$ cat ~davidm/.bash_aliases >> ~/.bash_aliases
$ echo 'source ~/.bash_aliases' >> ~/.bashrc
```

This just adds the command into your `$PATH` and then you can run the following commands. Traverse to a directory with the raw file in which you're trying to open. 

```shell
# we want to get the header for this file guppi_59143_55142_000486_GPS-BIIR-11_0001.0025.raw
cd /mnt_blpd15/datax/MKtemp/blpn48/GUPPI
# then check the file
rawhd guppi_59143_55142_000486_GPS-BIIR-11_0001.0025.raw  
```

This command will spit out a number of lines of parameters. Here is an example of such. 

```code
BACKEND = 'GUPPI   '
INSTANCE=                       0
IBVPKTSZ= '42,96,1024'
MAXFLOWS=                      16
BINDHOST= 'eth4    '
BINDPORT=                    7148
DATADIR = '/buf0/20201021/0018'
DESTIP  = '239.9.0.192+3'
CHAN_BW =             0.208984375
OBSBW   =                  13.375
OBSFREQ =         1504.5830078125
HCLOCKS =                 2097152
TBIN    =  4.7850467289719627E-06
PKTSTART=                 7963712
IBVSTAT = 'running '
NETSTAT = 'receiving'
DAQSTATE= 'RECORD  '
BLOCSIZE=               121634816
DIRECTIO=                       1
NBITS   =                       8
NPOL    =                       4
OBSNCHAN=                    3712
OVERLAP =                       0
PKTFMT  = 'SPEAD   '
OBS_MODE= 'RAW     '
NDROP   =                       0
DISKSTAT= 'writing '
NETBUFST= '18/24   '
OBSINFO = 'VALID   '
FENCHAN =                    4096
NANTS   =                      58
NSTRM   =                       4
HNTIME  =                     256
HNCHAN  =                      16
SCHAN   =                    3072
.
.
.
.
NPKT    =                  118784
DROPSTAT= '0/118784'
END
```

The list is rather extensive. You can check the terminology for these parameters on some documentation that Cherry has made! [here](https://docs.google.com/document/d/1dnye0HHSlVqRXH7rQ7v3wly0qKg-3_9tGJzaTI-76s4/edit?usp=sharing)



[<<< BACK](directory.html)




<div style="text-align:right">Made with Typora © 2021 Peter Ma </div>

