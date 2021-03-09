# ML MeerKAT V1 

This page will cover the first attempt at developing a ML Detection pipeline for SETI searches for MeerKAT telescope. The goal is to outline the approach and the various results from simulated test benches. 

[<<< BACK](directory.html) 



# Overview

**Problem:** RFI is computationally difficult to characterize and differentiate from the SETI signals we wish to find. The reason being they're both narrowband signals, and (sometimes) both RFI and SETI signals have some drift rate. The the real means of rejecting RFI is to look through a sequence of observations and different pointings to filter out terrestrials signals.

However, its a challenge for computers to characterize what signals are similar to each other to actually identify when a signal truly appears in multiple observations. Take the example below.

![img](raffy.jpg)

Classical algorithms mislabel these spectrograms because although these spectrograms show signals with S/N > 10 present in the ‘ON’ observations and none in the ‘OFF’ observations it fails to identify which signals are the same signal. If this were to be a more convincing candidate, the C scan shouldn't have drifted in the A scan and the A scan would've looked more like the other A scans. Furthermore, there should be a drift rate on these signals but there isn't. This presents a unique problem for ML algorithms. 

**Proposed Solution:** We can develop a Machine Learning model that holistically identifies what signals are similar and what signals aren't. This is potentially superior the the traditional algorithm because SNR and drift rate can't characterize the entire morphology of these signals. The goal is to extract these features using learned parameters. 

# Framework

This is how we will approach the developing the algorithm. There are multiple layers to this approach and we will begin at the top. 

```flow
st=>start: Input Filterbank|past:>http://www.google.com[blank]
e=>end: Classification|future:>http://www.google.com
op1=>operation: Preprocessing|past
op2=>operation: Neural Network|current
op3=>operation: PCA |current
op4=>operation: Spectral Clustering |current
op5=>operation: Pattern Fit |current
cond=>condition: Yes
or No?|approved:>http://www.google.com
c2=>condition: Good idea|rejected
io=>inputoutput: catch something...|future

st->op1(right)->op2(right)->op3(right)->op4->e

```

## Preprocessing 

When feeding data into the neural network, we need to apply transformations that allow the neural network to easily learn. Normally spectrograms contain values that can be `10E11` and some values are also negative. This makes it difficult for models to learn. We apply the following transformations. 

```python
min_val = combined.min()
data_1 = combined-min_val+1
data_1 = np.log(data_1)
max_val = data_1.max()
data_1 = data_1/max
```

Here's how it works:

1. First we get the smallest value. 
2. We shift all values above that minimum value. + some small constant 
3. We `Log` normalize the dataset so we get easier to deal with values
4. Then we find the maximum 
5. Scale everything down by the maximum -> making values between [0,1]. 

## Neural Network

We can then train the following architecture. The input is a `[16,256]` tensor. We apply a `[16]` filter depth with kernal of size `3x3` . We continue by maxpooling by a factor of `2` which reduces the dimension by a factor of 2 however the filter increases by a factor of 2. This finally produces a `64` length Filter. 

![img](neural.jpg)

## PCA

Note that we have a really long vector of `64` dimensional. When trying to apply clustering algorithms that implement a metric defined by the Euclidean distance, we're at risk of the `Curse of Dimensionality` which simply states that the more dimensions the weaker Euclidean distance is able to differentiate the features of these vectors. Making it hard to tell the difference. 

The parameters used for `PCA` specifies the `n_components` to anything less than 10. This case we used `3`. 

## Spectral Clustering

Then we finally apply spectral clustering. The reason we want to use this over `K-means` is that `spectral clustering` is superior in clustering a small number of clusters. In this case we only have `3` clusters for our model.

Why `3` instead of `2`? The reason is because from tests, giving the model `3` labels gives the model a boost in true positive detections while resulting in few false detections. 

Spectral Clustering techniques make use of the spectrum (eigenvalues) of the similarity matrix of the data to perform dimensionality reduction before clustering in fewer dimensions. The similarity matrix is provided as an input and consists of a quantitative assessment of the relative similarity of each pair of points in the dataset.

Also Spectral clustering computes distances in a graph like structure and thus preserves the shape of clusters more accurately. Similar to DBSCAN. 

![Spectral clustering. The intuition and math behind how it… | by Neerja  Doshi | Towards Data Science](https://miro.medium.com/max/2824/1*E1AkY1paiZYAUX6pAAZlPQ.png)

## Pattern Fit

Finally we fit the resultant classes into the patterns. In this case we used the strongest pattern which filters for **ONLY** clusters that have [ON,OFF,ON,OFF,ON,OFF] sequences. This can be seen with the following implemented logic. 

```python
def strong_cadence_pattern(labels):
    return labels[0]!=labels[1] and labels[1]!=labels[2] and labels[2]!= labels[3] and labels[3]!=labels[4] and labels[4]!=labels[5] 

```

## Conclusion 

This produces the final result of whether or not this is a candidate or not. The run time of the algorithm can be broken down in the following table. 

| Operation        | Time Taken [seconds] |
| ---------------- | -------------------- |
| IO Data creation | 203.19229197502136   |
| Preprocessing    | 4.321014881134033    |
| Neural Net       | 3.1015169620513916   |
| PCA              | 28.586014986038208   |
| Pattern          | 16.012786149978638   |
| TOTAL:           | 255.2160985469818    |

This is for 5000 Cadences or `30,000` observations with 600-700`Hz` window. 

[<<< BACK](directory.html) 