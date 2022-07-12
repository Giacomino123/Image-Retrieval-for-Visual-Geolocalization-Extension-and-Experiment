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

### Model Zoo
We are currently exploring hosting options, so this is a partial list of models. More models will be added soon!!

<details>
     <summary><b>Pretrained models with different backbones</b></summary></br>
    Pretained networks employing different backbones.</br></br>
	<table>
		<tr>
			<th rowspan=2>Model</th>
			<th colspan="3">Training on Pitts30k</th>
			<th colspan="3">Training on MSLS</th>
	 	</tr>
	 	<tr>
	  		<td>Pitts30k (R@1)</td>
	   		<td>MSLS (R@1)</td>
	   		<td>Download</td>
			<td>Pitts30k (R@1)</td>
	   		<td>MSLS (R@1)</td>
	   		<td>Download</td>
	 	</tr>
		<tr>
			<td>vgg16-gem</td>
			<td>78.5</td> <td>43.4</td>
			<td><a href="https://drive.google.com/file/d/1-e9v_mynIX5XBsdtN_mG9tz5-nA5PWiq/view?usp=sharing">[Link]</a></td>
			<td>70.2</td> <td>66.7</td>
			<td><a href="https://drive.google.com/file/d/1GqgO-qG-WNJXWty43KgvDtW0OpG0Wrq-/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
	 		<td>resnet18-gem</td>
			<td>77.8</td> <td>35.3</td>
			<td><a href="https://drive.google.com/file/d/1R66NYeLlxBIqLviUVL9XPZkrtmyMn_tU/view?usp=sharing">[Link]</a></td>
			<td>71.6</td> <td>65.3</td>
			<td><a href="https://drive.google.com/file/d/1IH0d_ME2kU3pagsKhx5ZfRfyWriErajn/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
			<td> resnet50-gem </td>
			<td>82.0</td> <td>38.0</td>
			<td><a href="https://drive.google.com/file/d/1esgXzRFvDFHrMnwwR3GlTnErXjFNrYV7/view?usp=sharing">[Link]</a></td>
			<td>77.4</td> <td>72.0</td>
			<td><a href="https://drive.google.com/file/d/1uuIYJN4N7lQqqsN32pbZwjhz5Xvv3zr-/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
			<td> resnet101-gem </td>
			<td>82.4</td> <td>39.6</td>
			<td><a href="https://drive.google.com/file/d/1Sd-sezmbzOGbZcy3eqRnWH07eoJ7CM0X/view?usp=sharing">[Link]</a></td>
			<td>77.2</td> <td>72.5</td>
			<td><a href="https://drive.google.com/file/d/1Iondvd8P3vb3piHFTA-RUgTFpqh0I31M/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
			<td>vgg16-netvlad</td>
			<td>83.2</td> <td>50.9</td>
			<td><a href="https://drive.google.com/file/d/14s7OZor6wrlGBKeXr0vKbPfTzlW9preM/view?usp=sharing">[Link]</a></td>
			<td>79.0</td> <td>74.6</td>
			<td><a href="https://drive.google.com/file/d/1dwai3uNudjvns58JIyaf5CBRg4ojcWIW/view?usp=sharing">[Link]</a</td>
	 	</tr>
	 	<tr>
			<td>resnet18-netvlad</td>
			<td>86.4</td> <td>47.4</td>
			<td><a href="https://drive.google.com/file/d/1KFwonDQYdvzTAIILsOMjmLRUR76jXXvB/view?usp=sharing">[Link]</a></td>
			<td>81.6</td> <td>75.8</td>
			<td><a href="https://drive.google.com/file/d/1_Ozq2TdvwLAJUwy7YH9l69GsfOU-MlFZ/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
			<td>resnet50-netvlad</td>
			<td>86.0</td> <td>50.7</td>
			<td><a href="https://drive.google.com/file/d/1KL8HoAApOjJFETin7Q7u7IcsOvroKlSj/view?usp=sharing">[Link]</a></td>
			<td>80.9</td> <td>76.9</td>
			<td><a href="https://drive.google.com/file/d/1krf0A6CeW8GqLqHWZ7dlSNJ9aTJ4dotF/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
			<td>resnet101-netvlad</td>
			<td>86.5</td> <td>51.8</td>
			<td><a href="https://drive.google.com/file/d/1064kDJ0LPyWoU7J4bMvAa0lTNEhAEi8v/view?usp=sharing">[Link]</a></td>
			<td>80.8</td> <td>77.7</td>
			<td><a href="https://drive.google.com/file/d/1rtPfsgfJ2Zoxs5uu7Ph1_qc7q-hIxJek/view?usp=sharing">[Link]</a></td>
	 	</tr>
	</table>
    
</details>

<details>
 	<summary><b>Pretrained models with different aggregation methods</b></summary></br>
 	Pretrained networks trained using different aggregation methods.</br></br>
    <table>
		<tr>
			<th rowspan=2>Model</th>
			 <th colspan="3">Training on Pitts30k (R@1)</th>
			 <th colspan="3">Training on MSLS (R@1)</th>
	 	</tr>
	 	<tr>
	  		<td>Pitts30k (R@1)</td>
	   		<td>MSLS (R@1)</td>
	   		<td>Download</td>
			<td>Pitts30k (R@1)</td>
	   		<td>MSLS (R@1)</td>
	   		<td>Download</td>
	 	</tr>
		<tr>
			<td>resnet50-gem</td>
			<td>82.0</td> <td>38.0</td>
			<td><a href="https://drive.google.com/file/d/1esgXzRFvDFHrMnwwR3GlTnErXjFNrYV7/view?usp=sharing">[Link]</a></td>
			<td>77.4</td> <td>72.0</td>
			<td><a href="https://drive.google.com/file/d/1uuIYJN4N7lQqqsN32pbZwjhz5Xvv3zr-/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
			<td>resnet50-gem-fc2048</td>
			<td>80.1</td> <td>33.7</td>
			<td><a href="https://drive.google.com/file/d/1GCbE4gzcRXMH8ETD2YCPo0I3suAXDr-y/view?usp=sharing">[Link]</a></td>
			<td>79.2</td> <td>73.5</td>
			<td><a href="https://drive.google.com/file/d/1oSf11wAxaoEbjLnjfX0EWZ65dgccwdDD/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
			<td>resnet50-gem-fc65536</td>
			<td>80.8</td> <td>35.8</td>
			<td><a href="https://drive.google.com/file/d/19GjodUuAGKpac6WhIcfuy3tiPV1J-ikn/view?usp=sharing">[Link]</a></td>
			<td>79.0</td> <td>74.4</td>
			<td><a href="https://drive.google.com/file/d/1OGwt651loL2vXnQYyABqitL39IEiXhag/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
			<td>resnet50-netvlad</td>
			<td>86.0</td> <td>50.7</td>
			<td><a href="https://drive.google.com/file/d/1KL8HoAApOjJFETin7Q7u7IcsOvroKlSj/view?usp=sharing">[Link]</a></td>
			<td>80.9</td> <td>76.9</td>
			<td><a href="https://drive.google.com/file/d/1krf0A6CeW8GqLqHWZ7dlSNJ9aTJ4dotF/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
			<td>resnet50-crn</td>
			<td>85.8</td> <td>54.0</td>
			<td><a href="https://drive.google.com/file/d/1mLOkILfIf8Wegi3tva9390TRIbWDxRor/view?usp=sharing">[Link]</a></td>
			<td>80.8</td> <td>77.8</td>
			<td><a href="https://drive.google.com/file/d/1KJzXwCsbyT0uNDl925H2J0QKXhKaeEgW/view?usp=sharing">[Link]</a></td>
	 	</tr>
	</table>
</details>


<details>
     <summary><b>Pretrained models with different mining methods</b></summary><br/>
    Pretained networks trained using three different mining methods (random, full database mining and partial database mining):</br></br>
	<table>
		<tr>
			<th rowspan=2>Model</th>
			 <th colspan="3">Training on Pitts30k (R@1)</th>
			 <th colspan="3">Training on MSLS (R@1)</th>
	 	</tr>
	 	<tr>
	  		<td>Pitts30k (R@1)</td>
	   		<td>MSLS (R@1)</td>
	   		<td>Download</td>
			<td>Pitts30k (R@1)</td>
	   		<td>MSLS (R@1)</td>
	   		<td>Download</td>
	 	</tr>
		<tr>
			<td> resnet18-gem-random</td>
			<td>73.7</td> <td>30.5</td>
			<td><a href="https://drive.google.com/file/d/12Ds-LcvFcA609bZVBTLNjAZIzV-g8UGK/view?usp=sharing">[Link]</a></td>
			<td>62.2</td> <td>50.6</td>
			<td><a href="https://drive.google.com/file/d/1oNZyfjTaulVTFX4wRrj0YISqxLuNRyhy/view?usp=sharing">[Link]</a></td>
	 	</tr>
		<tr>
			<td> resnet18-gem-full</td>
			<td>77.8</td> <td>35.3</td>
			<td><a href="https://drive.google.com/file/d/1bHVsnb6Km2npBsGK9ylI1vuOuc3WLKJb/view?usp=sharing">[Link]</a></td>
			<td>70.1</td><td>61.8</td>
			<td><a href="https://drive.google.com/file/d/1BbANLPVPxWDau2RP0cWTSS3FybbyUPL1/view?usp=sharing">[Link]</a></td>
	 	</tr>
		<tr>
			<td> resnet18-gem-partial</td>
			<td>76.5</td> <td>34.2</td>
			<td><a href="https://drive.google.com/file/d/1R66NYeLlxBIqLviUVL9XPZkrtmyMn_tU/view?usp=sharing">[Link]</a></td>
			<td>71.6</td> <td>65.3</td>
			<td><a href="https://drive.google.com/file/d/1IH0d_ME2kU3pagsKhx5ZfRfyWriErajn/view?usp=sharing">[Link]</a></td>
	 	</tr>
		<tr>
			<td> resnet18-netvlad-random</td>
			<td>83.9</td> <td>43.6</td> 
			<td><a href="https://drive.google.com/file/d/19OcEe2ckk-D8drrmxpKkkarT_5mCkjnt/view?usp=sharing">[Link]</a></td>
			<td>73.3</td> <td>61.5</td>
	 		<td><a href="https://drive.google.com/file/d/1JlEbKbnWyCbR4zP1ZYDct3pYtuJrUmVp/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
			<td> resnet18-netvlad-full</td>
			<td>86.4</td> <td>47.4</td>
			<td><a href="https://drive.google.com/file/d/1kwgyDEfRYtdaOEimQQlmj77rIR2tH3st/view?usp=sharing">[Link]</a></td>
			<td>-</td><td>-</td>
			<td>-</td>
	 	</tr>
	 	<tr>
			<td> resnet18-netvlad-partial</td>
			<td>86.2</td> <td>47.3</td> 
			<td><a href="https://drive.google.com/file/d/1KFwonDQYdvzTAIILsOMjmLRUR76jXXvB/view?usp=sharing">[Link]</a></td>
			<td>81.6</td> <td>75.8</td>
			<td><a href="https://drive.google.com/file/d/1_Ozq2TdvwLAJUwy7YH9l69GsfOU-MlFZ/view?usp=sharing">[Link]</a></td>
	 	</tr>
	 	<tr>
			<td> resnet50-gem-random</td>
			<td>77.9</td> <td>34.3</td> 
			<td><a href="https://drive.google.com/file/d/1f9be75EaG0fFLeNF0bufSre_efKH_ObU/view?usp=sharing">[Link]</a></td>
			<td>69.5</td> <td>57.4</td>
			<td><a href="https://drive.google.com/file/d/1h9-av6qMn-LVapI5KA4cZhT5BKaZ79C6/view?usp=sharing">[Link]</a></td>
		</tr>
		<tr>
			<td> resnet50-gem-full</td>
			<td>82.0</td> <td>38.0</td> 
			<td><a href="https://drive.google.com/file/d/1quS9ZjOrXBqNDBhQzlSj8aeh3dBfP1GY/view?usp=sharing">[Link]</a></td>
			<td>77.3</td> <td>69.7</td>
			<td><a href="https://drive.google.com/file/d/1pxU881eTcz_YdQthKz5yohU7WoLpXt8J/view?usp=sharing">[Link]</a></td>
		</tr>
		<tr>
			<td> resnet50-gem-partial</td>
			<td>82.3</td> <td>39.0</td> 
			<td><a href="https://drive.google.com/file/d/1esgXzRFvDFHrMnwwR3GlTnErXjFNrYV7/view?usp=sharing">[Link]</a></td>
			<td>77.4</td> <td>72.0</td>
			<td><a href="https://drive.google.com/file/d/1uuIYJN4N7lQqqsN32pbZwjhz5Xvv3zr-/view?usp=sharing">[Link]</a></td>
		</tr>
		<tr>
			<td> resnet50-netvlad-random</td>
			<td>83.4</td> <td>45.0</td> 
			<td><a href="https://drive.google.com/file/d/1TkzlO-ZS42u6e783y2O3JZhcIoI7CEVj/view?usp=sharing">[Link]</a></td>
			<td>74.9</td> <td>63.6</td>
			<td><a href="https://drive.google.com/file/d/1E_X2nrnLxBqvLfVfNKtorOGW_VmwOqSu/view?usp=sharing">[Link]</a></td>
		</tr>
		<tr>
			<td> resnet50-netvlad-full</td>
			<td>86.0</td> <td>50.7</td> 
			<td><a href="https://drive.google.com/file/d/133uxEJZ0gK6XL1myhSAFC7wibZtWnugK/view?usp=sharing">[Link]</a></td>
			<td>-</td><td>-</td>
			<td>-</td>
		</tr>
		<tr>
			<td> resnet50-netvlad-partial</td>
			<td>85.5</td> <td>48.6</td> 
			<td><a href="https://drive.google.com/file/d/1GCbE4gzcRXMH8ETD2YCPo0I3suAXDr-y/view?usp=sharing">[Link]</a></td>
			<td>80.9</td> <td>76.9</td>
			<td><a href="https://drive.google.com/file/d/1krf0A6CeW8GqLqHWZ7dlSNJ9aTJ4dotF/view?usp=sharing">[Link]</a></td>
		</tr>
	</table>
</details>


## Acknowledgements
Parts of this repo are inspired by the following great repositories:
- [Deep visual geo localization benchmark](https://github.com/gmberton/deep-visual-geo-localization-benchmark)
- [NetVLAD's original code](https://github.com/Relja/netvlad) (in MATLAB)
- [NetVLAD layer in PyTorch](https://github.com/lyakaap/NetVLAD-pytorch)
- [NetVLAD training in PyTorch](https://github.com/Nanne/pytorch-NetVlad/)
- [GeM](https://github.com/filipradenovic/cnnimageretrieval-pytorch)
- [Deep Image Retrieval](https://github.com/naver/deep-image-retrieval)



