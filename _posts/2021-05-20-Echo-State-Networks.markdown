---
layout: post
title:  "Introduction: Echo State Networks"
date:   2021-05-20 22:07:29 +0200
categories: jekyll update
---

The first machine learning algorithm that I encountered while doing my master thesis, was the echo state network (ESN). I am still not sure, whether this specific algorithm was a good introduction to the whole field of ML and neural networks. It is not your typical neural network about which you have already seen a YouTube video about. Nevertheless it was certainly an interesting topic. Especially, when combining it with an application in fluid mechanics.


### The Echo State Approach
The echo state network was proposed by Herbert Jaeger in his GMD report **148** in 2001[[1]]. It is a recurrent neural network (RNN), meaning that the hidden neurons of this network are recurrently connected to one another. Even connections of one neuron to itself are possible. Our brain is said to have a similar architecture. This recurrency has interesting properties: as information passes through our brain cells, it stays within many cells for a long time, as cyclic connection keep the information 'trapped'. I find this a nice analogy to understand why RNNs are very usefull for tasks that require some sort of memory, i.e. access to information of the past few time steps. It is certainly nice to philosiphy about such similarities, but in the end I would not interpret too much into it.

RNNs were proposed way before Jaeger's echo state approach[[1]]. In conventional NN training, you usually learn input weights, hidden weights and output weights, as well as some bias terms. Yet, doing the same for an RNN by backpropagation algoritms posed a big problem at the time, as the gradients that one has to compute either vanished or diverged. This is an inherent feature of recurrent connections. Researchers proposed many altered RNN architectures like the long short term memory (LSTM)[[3]] and gated recurrent unit (GRU)[[4]]. Jaegers approach is radical different, by
 - letting input and output weights stay fixed after random initialization
 - merely fitting the output weights to the training output via linear regression

This is what makes the ESN a simple and handy algorithm, as the training is only a linear fit to your training data. The fact that we randomly create two weight matrices and subsequently use them for further data processing, might seem strange. Why should two random untrained matrices give any good results? They probably wouldn't. That is why, one needs to take extra care of the algebraic properties of these weights. For the input weights this usually boils down to a global scaling factor that can also be understood as scaling your input data. For the hidden weights on the other hand, one should scale the biggest absolute eigenvalue, denoted as its spectral radius. Jaeger further proposed to set many hidden weights to zero, leaving the matrix to be sparse. These factors together with the number of hidden neurons pose a set of hyperparameters, i.e. parameters that are not learned and have to be tuned beforehand. The bad news here is that these hyperparameters are very sensitive to the data at hand. This usually leads to extensive grid searches. On the other hand, optimization methods like genetic algorithms or bayesian optimization might be of use.

### ESN Algorithm
As mentioned above, the ESN is composed of input weights $$W^{\rm in}\in\mathbb{R}^{N_r\times N_{in}}$$, reservoir/hidden weights $$W^{\rm r}\in \mathbb{R}^{N_r\times N_r}$$ and (the learned) output weights $$W^{\rm out}\in \mathbb{R}^{N_{out}\times N_r}$$.

Further the data is usually given in form of a discrete multivariate time-series $$\mathbf{x}\in \mathbb{R}^{N_{in}\times (T_t + T_v)}$$, where $$T_t$$ and $$T_v$$ are the no. training and validation time steps respectively. With the ESN, we train the reservoir to predict the next iteration $$\mathbf{u}(n+1)\mathbb{R}^{N_{in}}$$ for a given input $$\mathbf{u}(n)\mathbb{R}^{N_{in}}$$. Therefore the input and output dimensions are the same: $$N_{in} = N_{out}$$. Note that this must not be the case, e.g. when the ESN is used as an emulator to a dynamical system.

We denote the prediction of the reservoir, which corresponds to the input $$\mathbf{u}(n)$$ as $$\hat{\mathbf{y}}(n)\mathbb{R}^{N_{in}}$$. The target value will be denoted $$\mathbf{y}(n) = \mathbf{u}(n+1)$$. Note that we choose the time step index for the output/target data, the same as for the input. 

With this, we can think of the neurons $$\mathbf{s} \in \mathbb{R}^{N_r\times N_r}$$ as a dynamical system:

$$
\mathbf{s}(n) = (1-\gamma)\mathbf{s}(n-1) + \gamma \tanh\left(W^{in} \mathbf{u}(n) + W^{r}\mathbf{s}\right)
$$

where the reservoir output is given by the linear mapping

$$
\hat{\mathbf{y}} = W^{out}\mathbf{s}.
$$

Note that, as opposed to common ML algorithms, the learned parameters (here $$W^{out}$$) are not initialized at random and are therefore not available during the training process.

#### Training Phase
During the training phase we update the reserovir neurons and collect each time instance into a state matrix $$S\in \mathbb{R}^{N_r \times T_t}$$. By doing the same for the target output data $$\mathbf{y}$$, we obtain a matrix 
$$Y\in\mathbb{R}^{N_{in}\times T_t}$$. We perform ridge regression with $$L^2$$ penalty to obtain $$W^{out}$$:

$$
W^{out} = YS^T \left(SS^T + \lambda I\right)^{-1},
$$ 

where $$\lambda\ge 0$$ is the ridge regression parameter and $$I\in \mathbb{R}^{N_r \times N_r}$$ is the identity. The regression parameter is usually another hyperparameter which we have to tune to fit our data.

After this linear regression, we are all done with the training. This step is the most computationally expensive in this algorithm. Opposed to gradient descent algorithms, this is a huge speed up.

#### Prediction Phase
During the prediction phase, we can now use the output matrix to compute the reservoirs prediction to the current input. In common applications, we want to predict a point far in the future and not right after training phase. Further, we want to quantify the prediction capablities up to this point. 

For this, we use the validation data $$\mathbf{y}\in \mathbb{R}^{T_v}$$. We consider the validation data not to be known to the reservoir and therefore feed the reservoirs last output to the input layer during the next iteration:

$$
\mathbf{u}(n) = \mathbf{y}(n-1) = W^{out}\mathbf{s}(n-1) 
$$

The reservoir update can then be written as

$$
\mathbf{s}(n) = (1-\gamma)\mathbf{s}(n-1) + \gamma \tanh\left(\left(W^{in} W^{out} + W^r\right)\mathbf{s}(n-1)\right)
$$

[1]: http://publica.fraunhofer.de/documents/B-73135.html
[2]: https://doi.org/10.1038%2F323533a0
[3]: dx.doi.org/10.1162/neco.1997.9.8.1735
[4]: https://arxiv.org/abs/1406.1078