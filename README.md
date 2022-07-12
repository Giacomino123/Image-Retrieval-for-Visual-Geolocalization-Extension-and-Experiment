# Image Retrieval for visual geolocalization 

## Setup
Before you begin experimenting with this toolbox, your dataset should be organized in a directory tree as such:

```
.
├── benchmarking_vg
└── datasets_vg
    └── datasets
        └── pitts30k
            └── images
                ├── train
                │   ├── database
                │   └── queries
                ├── val
                │   ├── database
                │   └── queries
                └── test
                    ├── database
                    └── queries
```
The [datasets_vg](https://github.com/gmberton/datasets_vg) repo can be used to download a number of datasets. Detailed instructions on how to download datasets are in the repo. Note that many datasets are available, and _pitts30k_ is just an example.

## Running experiments
### Basic experiment
For a basic experiment run

`$ python3 train.py --dataset_name=pitts30k --datasets_folder="location of your dataset"`

this will train a ResNet-18 + NetVLAD on Pitts30k.
The experiment creates a folder named `./logs/default/YYYY-MM-DD_HH-mm-ss`, where checkpoints are saved, as well as an `info.log` file with training logs and other information, such as model size, FLOPs and descriptors dimensionality.

### Architectures and mining
You can replace the backbone and the aggregation as such

`$ python3 train.py --dataset_name=pitts30k --backbone=resnet18conv4 --aggregation=gem`


To evaluate the trained model on other datasets (this example is with the Pitts30k), simply run

`$ python3 eval.py --backbone=resnet18conv4 --aggregation=gem --resume=logs/default/YYYY-MM-DD_HH-mm-ss/best_model.pth --dataset_name=pitts30k`

#### Reproduce the results
Finally, to reproduce our results, use the appropriate mining method: _full_ for _pitts30k_ as such:

`$ python3 train.py --dataset_name=pitts30k --mining=full`

As simple as this, you can replicate all results from tables 3, 4, 5 of the main paper, as well as tables 2, 3, 4 of the supplementary.

### Using pretrained networks on other datasets
Check out our [pretrain_vg](https://github.com/rm-wu/pretrain_vg) repo which we use to train such models.
You can automatically download and train on those models as such

`$ python train.py --dataset_name=pitts30k --pretrained=places`

### Changing the threshold distance
You can use a different distance than the default 25 meters as simply as this (for example to 100 meters):

`$ python3 eval.py --resume=logs/default/YYYY-MM-DD_HH-mm-ss/best_model.pth --val_positive_dist_threshold=100`

### Changing the recall values (R@N)
By default the toolbox computes recalls@ 1, 5, 10, 20, but you can compute other recalls as such:

`$ python3 eval.py --resume=logs/default/YYYY-MM-DD_HH-mm-ss/best_model.pth --recall_values 1 5 10 15 20 50 100`

### Optimizers and schedulers 

to test the different optimizers, use the following code :

`$ python3 train.py --optim=[select one among adam, sgd, asgd, adamw] --dataset_name=pitts30k --datasets_folder="location of your dataset"`

to combine optimizers with schedulers, starting for example from learning rate 0.01, use the following code :

`$ python3 train.py --scheduler=[select among ReduceLROnPlateau, CosineAnnealingLR] --lr=0.01 --optim=[select one among adam, sgd, asgd, adamw] --dataset_name=pitts30k --datasets_folder="location of your dataset"`

### Multiscale Testing 

to test the model using different scale of images, use the following code:
TODO

### Smart Data Augmentation 

To train a model that returns slightly better results when tested on night domain, use the following code 

`$ python3 train.py --night_brightness=[a value that range from (0,+inf)]--dataset_name=pitts30k --datasets_folder="location of your dataset"`
note that the smaller the brightness value, the darker the original image passed as training queries will be

### Domain Adaptation (for night domain)

Create a new dataset that is spilt into source distribution and target distribution 

to train the model with GRL, use the following code :

`$ python3 train.py --grl --dataset_name=pitts30k_new --datasets_folder="location of your new dataset"`

## Acknowledgements
Parts of this repo are inspired by the following great repositories:
- [Deep visual geo localization benchmark](https://github.com/gmberton/deep-visual-geo-localization-benchmark)
- [NetVLAD's original code](https://github.com/Relja/netvlad) (in MATLAB)
- [NetVLAD layer in PyTorch](https://github.com/lyakaap/NetVLAD-pytorch)
- [NetVLAD training in PyTorch](https://github.com/Nanne/pytorch-NetVlad/)
- [GeM](https://github.com/filipradenovic/cnnimageretrieval-pytorch)
- [Deep Image Retrieval](https://github.com/naver/deep-image-retrieval)



