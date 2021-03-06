# The continuous Bernoulli: fixing a pervasive error in variational autoencoders & The continuous categorical: a novel simplex-valued exponential family

This repo contains example code from [The continuous Bernoulli: fixing a pervasive error in variational autoencoders](https://arxiv.org/abs/1907.06845) (Tensorflow 1) and [The continuous categorical: a novel simplex-valued exponential family](https://arxiv.org/abs/2002.08563) (Tensorflow 2).

## The continuous Bernoulli: fixing a pervasive error in variational autoencoders

The continuous Bernoulli is now part of [Tensorflow probability](https://github.com/tensorflow/probability) and [PyTorch](https://github.com/pytorch/pytorch) (as of writting this, only in the bleeding edge versions). Different likelihoods are in separate notebooks for didactic purposes, cb/cb_vae_mnist.ipynb implements the continuous Bernoulli VAE on MNIST. If you are only interested in the log normalizing constant of the continuous Bernoulli, see the code snippet below:
```
def cont_bern_log_norm(lam, l_lim=0.49, u_lim=0.51):
    # computes the log normalizing constant of a continuous Bernoulli distribution in a numerically stable way.
    # returns the log normalizing constant for lam in (0, l_lim) U (u_lim, 1) and a Taylor approximation in
    # [l_lim, u_lim].
    # cut_y below might appear useless, but it is important to not evaluate log_norm near 0.5 as tf.where evaluates
    # both options, regardless of the value of the condition.
    cut_lam = tf.where(tf.logical_or(tf.less(lam, l_lim), tf.greater(lam, u_lim)), lam, l_lim * tf.ones_like(lam))
    log_norm = tf.log(tf.abs(2.0 * tf.atanh(1 - 2.0 * cut_lam))) - tf.log(tf.abs(1 - 2.0 * cut_lam))
    taylor = tf.log(2.0) + 4.0 / 3.0 * tf.pow(lam - 0.5, 2) + 104.0 / 45.0 * tf.pow(lam - 0.5, 4)
    return tf.where(tf.logical_or(tf.less(lam, l_lim), tf.greater(lam, u_lim)), log_norm, taylor)
```

## The continuous categorical: a novel simplex-valued exponential family

We provide python scripts to train the models used in the paper, as well as to produce the relevant figures and summary statistics.
- ```cc/cc_funcs.py``` contains functions specific to the CC distribution (log-normalizer, log-likelihood etc.).
- ```cc/cc_samplers.py``` contains sampling algorithms for the CC distribution.
- ```cc/mle_empirical_average.py``` runs simulations that can be used to evaluate the bias of the Dirichlet and CC.
- The scripts ```cc/election*``` prepare the data and fit our models of the UK general election. They include example code for Keras model objects that use the CC (both linear models and neural networks).
- The scripts ```cc/mnist*``` train teacher and student models for our model compression experiments.
- The scripts ```cc/plot*``` produce the plots used for the manuscript.
