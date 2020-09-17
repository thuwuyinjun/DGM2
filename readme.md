# The code for the paper "Dynamic Gaussian Mixture based Deep Generative Model ForRobust Forecasting on Sparse Multivariate Time Series" submitted to AAAI 2021


## Prerequisites:
install conda, pytorch, matplotlib, pandas, scikit-learn tensorboardX, torchdiffeq (see the instructions in https://github.com/rtqichen/torchdiffeq)



## Instructions on how to use the code to do the forecasting tasks on the USHCN, KDD_CUP and MIMIC-III dataset:
1. Normalize and partition the dataset for forecasting with the following commands in the terminal (since the raw dataset is very large, here we only provide the dataset after the preprocess step):


To generate data for forecasting on USHCN dataset:

```
cd data/
python3 generate_time_series.py --dataset USHCN
```

To generate data for forecasting on KDDCUP dataset:

```
cd data/
python3 generate_time_series.py --dataset KDDCUP
```



To generate data for forecasting on MIMIC3 dataset (the MIMIC3 dataset is very large even after the preprocessing step. Therefore, we compress this dataset here):

```
cd dataset_dir/
unzip mimic3.zip
cd data/
python3 generate_time_series.py --dataset KDDCUP
```



2. run the forecasting program train.py in the main directory. The arguments for running this program are:


--dataset: the name of the dataset (KDDCUP or USHCN or MIMIC3)

--model: the model name (DGM2_L or DGM2_O, DGM2_L uses LSTM for transition while DGM2_O uses ODE for transition)

-b: mini-batch size

--epochs: epoch count for training

--GPU: flag of using GPU or not

--GPUID: ID of the GPU for running train.py

--max_kl: the maximal coefficient for the KL divergence term in the loss function. We use annealing technique to tune the coefficient during the training process.

--use_gate: flag of using the gate function or not

--gaussian: the parameter gamma to balance the dynamic component and the basis mixture component in the dynamic gaussian mixture distribution, which will take effect when --use_gate is not used, e.g. "--gaussian 0.001"

--wait_epoch: number of epochs for the warm-up phase with annealing technique during which the coefficient for the KL divergence term in the loss function is zero. The default value is 0


--cluster_num: number of clusters for DGM2_L and DGM2_O. The default value is 20.





In what follows, we use USHCN as a running example to show how we run the forecasting program:

### with GPU (suppose the GPU ID is 0) and gate function:



use DGM2_L:
```
python3 train.py --dataset USHCN --model DGM2_L -b 100 --epochs 50 --GPU --GPUID 0 --max_kl 5 --use_gate --wait_epoch 0
```

or

use DGM2_O:
```
python3 train.py --dataset USHCN --model DGM2_O -b 100 --epochs 50 --GPU --GPUID 0 --max_kl 5 --use_gate --wait_epoch 0
```


### without GPU but using gate function:

use DGM2_L:

```
python3 train.py --dataset USHCN --model DGM2_L -b 100 --epochs 50 --max_kl 5 --use_gate --wait_epoch 0
```

or

use DHM2_O:

```
python3 train.py --dataset USHCN --model DGM2_O -b 100 --epochs 50 --max_kl 5 --use_gate --wait_epoch 0
```











Similarly, we can use the following command to run the forecasting program on MIMIC3 and KDDCUP dataset:


```
python3 train.py --dataset MIMIC3 --model DGM2_O -b 3000 --epochs 200 --GPU --GPUID 0 --max_kl 6 --use_gate --wait_epoch 60
```

or 


```
python3 train.py --dataset KDDCUP --model DGM2_O -b 200 --epochs 200 --GPU --GPUID 0 --max_kl 3 --use_gate
```
