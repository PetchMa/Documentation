# RAWSPEC Commands 📀

### Peter Ma | Jan 16  2021

This notebook documents some of the main commands needed to RAWSPEC a basic raw file. This can be improved. Currently a work in progress. 

[<<< BACK](directory.html)



### Basic RAWSPEC Commands

First we want to generate a single spectra from a series of files. Here is a good example.... 

```{shell}
time rawspec -f <freq size> -t <time size> <filename> -d <save directory>
```

The data shape in the end will be the `freq` and the `time` size you set with the `-f` and `-t` tags. Note that this might overload the memory of the GPU and thus you might need to split up the work into multiple files over multiple `coarse channels`. We can do so like such: 

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

```{shell}
#!/bin/bash
for i in {0..16}
do
    mkdir ../../../datax/scratch/pma/guppi_59143_55142_000486_GPS-BIIR-11_0001.$i
    rawspec -f 131072 -t 512 -s $i*4 -n 4  ../../../mnt_blpd15/datax/MKtemp/blpn48/GUPPI/guppi_59143_55142_000486_GPS-BIIR-11_0001 -d /home/pma/Ideas/guppi_59143_55142_000486_GPS-BIIR-11_0001.$i
    
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









[<<< BACK](directory.html)




<div style="text-align:right">Made with Typora © 2021 Peter Ma </div>

