# Parameter Documentation

When creating data we refer to the following definitions:

## Synthetic Data

**Positive:** we mean that there is a *SETI* signal

**Negative: **we mean that there is no *SETI* signal however that doesn't mean there is no signal

**RFI: **we define as a signal that appears in multiple observations. Could be narrowband, could be drifting etc. 

**SETI: ** signal is defined as a narrowband doppler drifting signal that only appears in *ON* observations. 

**Injected: ** means signal that is synthetic. 

Combining together some words help explain some of the Synthetic data parameters we have a number of parameters and types of data that we make and search.

****

**Gaussian Noise Background - BLANK observation**

These are observations that contain no signal whatsoever however they do contain a potentially varying background noise. 

**RFI Injected Negative GBT**

These observations contain no SETI signal and contain an injected RFI signal on top of GBT RFI. It looks like this: 

![alt text](images/injected_neg.jpg)

**RFI Injected Positive GBT**

These observations include SETI signal and includes a fake signal we injected, on top of RFI from GBT data. This looks like the following: 

![alt text](images/injected_pos.jpg)

**Positive GBT- single shot**

These observations ONLY have a SETI signal with some background RFI/noise from the GBT 

![alt text](images/injected_pos_seti.jpg)

