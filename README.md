# BANANAS
Bayesian Optimization with Neural Architectures for Neural Architecture Search

## A new method for neural architecture search
BANANAS is a neural architecture search (NAS) algorithm which uses Bayesian optimization with a meta neural network to predict the validation accuracy of neural architectures. We use a path-based encoding scheme to featurize the neural architectures that are used to train the neural network model. After training on just 200 architectures, we are able to predict the validation accuracy of new architectures to within one percent on average. The full NAS algorithm beats the state of the art on the NASBench and the DARTS search spaces. On the NASBench search space, BANANAS is over 100x more efficient than random search, and 3.8x more efficent than the next-best algorithm we tried. On the DARTS search space, BANANAS finds an architecture with a test error of 2.57%.

<p align="center">
<img src="img/bananas_fig.png" alt="bananas_fig" width="70%">
</p>

## Requirements
- tensorflow == 1.14.0
- pytorch == 1.2.0, torchvision == 0.4.0
- matplotlib, jupyter
- nasbench (follow the installation instructions [here](https://github.com/google-research/nasbench))

## Train a meta neural network with a notebook on the NASBench dataset
- Download the nasbench_only108 tfrecord file (size 499MB) [here](https://storage.googleapis.com/nasbench/nasbench_only108.tfrecord)
- Place `nasbench_only108.tfrecord` in the top level folder of this repo
- Open and run `meta_neuralnet.ipynb` to reproduce Table 1 and Figure 4 of our paper

<p align="center">
  <img src="img/metann_adj_train.png" alt="bananas_fig" width="24%">
  <img src="img/metann_adj_test.png" alt="bananas_fig" width="24%">
  <img src="img/metann_path_train.png" alt="bananas_fig" width="24%">
  <img src="img/metann_path_test.png" alt="bananas_fig" width="24%">
</p>

## Evaluate pretrained BANANAS architecture
The best architecture found by BANANAS on the DARTS search space achieved 2.57% test error. To evaluate our pretrained neural architecture, run the following command.
```
cd darts-deterministic/cnn; python test.py --model_path bananas.pt
```
The error on the test set should be 2.57%. This can be run on a CPU or GPU, but it will be faster on a GPU.
\*Note: the code inside `bananas/darts-deterministic` is our fork of `quark0/darts` and `liamcli/darts`. But to preserve anonymity, we placed this code inside our repo.

<p align="center">
<img src="img/bananas_normal.png" alt="bananas_normal" width="42%">
<img src="img/bananas_reduction.png" alt="bananas_reduction" width="47%">
</p>
<p align="center">
The best neural architecture found by BANANAS on CIFAR-10. Convolutional cell (left), and normal cell (right).
</p>

## Train BANANAS architecture
Train the best architecture found by BANANAS.
```
python train.py --auxiliary --cutout
```
This will train the architecture from scratch, which takes about 32 hours on a NVIDIA V100 GPU. 
The final test error should be 2.57%.

## Run BANANAS on the NASBench search space
To run BANANAS on NASBench, go back into the bananas repo.
- Download nasbench_only108.tfrecord and put it into the bananas folder
```
python run_experiments_sequential.py
```
This will test the nasbench algorithm against several other NAS algorithms on the NASBench search space.
To customize your experiment, open `params.py`. Here, you can change the hyperparameters and the algorithms to run.

<p align="center">
<img src="img/nasbench_plot.png" alt="nasbench_plot" width="70%">
</p>

## Run BANANAS on the DARTS search space
We highly recommend using multiple GPUs to run BANANAS on the DARTS search space. You can run BANANAS in parallel on GCP using the shell script 
```
run_experiments_parallel.sh
```
