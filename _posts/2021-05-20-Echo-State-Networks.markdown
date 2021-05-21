---
layout: post
title:  "Echo State Networks"
date:   2021-05-20 22:07:29 +0200
categories: jekyll update
---

The first machine learning algorithm that I encountered while doing my master thesis, was the echo state network (ESN). I am still not sure, whether this specific algorithm was a good introduction to the whole field of ML and neural networks. It is not your typical neural network about which you have already seen a YouTube video about. Nevertheless it was certainly an interesting topic. Especially, when combining it with an application in fluid mechanics.


### The Echo State Approach
The echo state network was proposed by Herbert Jaeger in his GMD report **148** in 2001[[1]]. It is a recurrent neural network (RNN), meaning that the hidden neurons of this network are recurrently connected to one another. Even connections of one neuron to itself are possible. Our brain is said to have a similar architecture. This recurrency has interesting properties: as information passes through our brain cells, it stays within many cells for a long time, as cyclic connection keep the information 'trapped'. I find this a nice analogy to understand why RNNs are very usefull for tasks that require some sort of memory, i.e. access to information of the past few time steps. It is certainly nice to philosiphy about such similarities, but in the end I would not interpret too much into it.

RNNs were proposed way before Jaeger's echo state approach[[1]]. In conventional NN training, you usually learn input weights, hidden weights and output weights, as well as some bias terms. Yet, doing the same for an RNN by backpropagation algoritms posed a big problem at the time, as the gradients that one has to compute either vanished or diverged. This is an inherent feature of recurrent connections. Researchers proposed many altered RNN architectures like the long short term memory (LSTM)[[3]] and gated recurrent unit (GRU)[[4]]. Jaegers approach is radical different, by
 - letting input and output weights stay fixed after random initialization
 - merely fitting the output weights to the training output via linear regression

This is what makes the ESN a simple and handy algorithm, as the training is only a linear fit to your trainin data. The fact that we randomly create two weight matrices and subsequently use them for further data processin, might seem strange. Why should two random untrained matrices give any good results? They probably wouldn't. That is why, one needs to take extra care of the algebraic properties of these weights. For the input weights this usually boils down to a global scaling factor that can also be understood as scaling your input data. For the hidden weights on the other hand, one should scale the biggest absolute eigenvalue, denoted as its spectral radius. Jaeger further proposed to set many hidden weights to zero, leaving the matrix to be sparse. These factors together with the number of hidden neurons pose a set of hyperparameters, i.e. parameters that are not learned and have to be tuned beforehand. The bad news here is that these hyperparameters are very sensitive to the data at hand. This usually leads to extensive grid searches.

### ESN Algorithm



[1]: http://publica.fraunhofer.de/documents/B-73135.html
[2]: https://doi.org/10.1038%2F323533a0
[3]: dx.doi.org/10.1162/neco.1997.9.8.1735
[4]: https://arxiv.org/abs/1406.1078