+++
title = 'Training Models'
date = 2024-02-08T14:10:01+07:00
weight = 1
+++

## Dataset preparation
-----

[Dataset preparation](/data/preprocess/dataset/) and [Data Preprocess](data/preprocess/) Section guide you through the step of how to prepare dataset be generating numpy array from raster image.

#### Model input

- Sentinel 2 + 1 stack image
- Multi-dimensional image numpy .npy format sampling from target raster, in this study we use [B1, B2, B3, B4, B5, B6, B7, B8, B8A, B9, B11, B12 , VV(ASC) , VH(ASC)]
- Support any resolution , but thing to consider that larger image require more computation and time cost while smaller image will likely to lost spatial information , in this study we use 244x244 px

![14layer](14layer.png?height=400px)
> Figure : Example visualization of sampling data Sentinel 2 + 1 stack image [B1, B2, B3, B4, B5, B6, B7, B8, B8A, B9, B11, B12 , VV(ASC) , VH(ASC)]

#### Model Reference Ground truth

- GEDI rasterize sparse AGB/canopy height image
- 1 channel image numpy .npy format sampling from target raster, same area as model input.
- leave pixel that have no value as NAN 
- Almost all pixel will be NAN value. That's okay because of properties of sensor, GEDI has very sparse coverage measurement.

![input](input.png?classes=inline)
![output](output.png?classes=inline)

> Figure : Example visualization pair of input Sentinel 2 + 1 stack image and GEDI ground truth , although GEDI has very limited data. We will use technique call sparse-supervison to train deep learning model.

Sparse supervision is take place during training step by only calculate only pixel that have numeric value

- in `trainer.py`
```
    # calculate on only with GEDI pixel that is not nan
    predictions = predictions[~np.isnan(true_masks.detach().cpu().numpy())]
    log_variances = log_variances[~np.isnan(true_masks.detach().cpu().numpy())]
    true_masks = true_masks[~np.isnan(true_masks.detach().cpu().numpy())].float()
```

#### Dataset Hierarchy

- Make root folder for input and reference data seperately.

- Keep trian/val/test in the same root directory.

- Make sure to keep the same name for each instance input/groundtruth in train/val/test subset so pytorch dataloder can identify.

- Example of dataset directory


###### Sentinel 1 , 2 Input
```
    /[Input Dir Name]
        /train
            - abc.npy 
            ...
        /test
            - def.npy 
            ...
        /val
            - ijk.npy 
            ...

```

###### GEDI ground truth
```
    /[GT Dir Name]
        /train
            - abc.npy 
            ...
        /test
            - def.npy 
            ...
        /val
            - ijk.npy 
            ...
```

#### Training
-----
##### Prerequsite

- Pytorch
- torchsummary
- torchmetrics
- tqdm
- rasterio
- numpy
- matplotlib
- wandb

##### Training from scratch 

1. Set up wandb API key to record model evaluation metric

The training script use both tensorboard to store model evaluation result locally as well as WANDB 

Tensorboard result is locate at `/log ` dir at output directory

- After preparing dataset. Set up `WANDB` API key as enviroment variable

```
export WANDB_API_KEY=[YOUR_WADB_API_KEY]

```

2. run `train.py` in `/src`  directory with required argument

```
python3 train.py [--train_input_data_dir TRAIN_DIR] [--train_label_data_dir GT_DIR] [--all_checkpoint_dir CKP_DIR]
```
`TRAIN_DIR`  specify root directory of train/validation/test of input image.

`GT_DIR`     specify root directory of train/validation/test of reference image.

`CKP_DIR`   specify root directory that keep all model weights in each epoch.



Additionally, you can specify optional argument to modify hyperameter of the training process

```
optional arguments:
  -h, --help                        show this help message and exit
  --out_dir                         output directory for the experiment , default='./tmp/'
  --nb_epoch E                      Number of epochs , default=100
  --batch_size B                    Batch size, default=32
  --base_learning_rate BLR          Initial learning rate, default=1e-7            
  --l2_lambda  L2                   L2 regularizer on weights hyperparameter, default=1e-8          
  --optimizer ['ADAM', 'SGD']       optimizer , default= 'ADAM'
  --momentum MT                     momentum for SGD, default=0.9
  --gradient_clipping G             gradient_clipping, default=1.0
  --amp [True , False]              Use mixed precision , default = False
  --bilinear [True , False]         Use bilinear upsampling, default = True
  --num_ch CH                       Number of input_channels, default=14
  --save_checkpoint [True , False]  Save weight every epoch? , default = True      

```

- After model finished training, model's best weight (lowest validation error) is locate at path specify in `--out_dir` optinal argument. Along with log and test result


{{% notice style="tip" title="Tip"%}}
If you don't want to pass input,labe,checkpoint directory path as argument everytime. You can set as default parameter in src/utils/parset.py {{% /notice %}}

In `parser.py` locate 3 parser argument to set default path.

```


parser.add_argument("--all_checkpoint_dir", required = True, help="all_checkpoint directory")

#dataset params
parser.add_argument("--train_input_data_dir", required = True , help="Training input data directory with folder train/test/val.")
parser.add_argument("--train_label_data_dir", required = True , help="Training label data directory with folder train/test/val.")

```

##### Training from pre-trained weight

- use keyword `resume` to argument `--train_mode`  and specify model checkpoint path with  argument `--model_weights_path` 

```
python3 train.py [--train_input_data_dir TRAIN_DIR] [--train_label_data_dir GT_DIR] [--all_checkpoint_dir CKP_DIR] [--model_weights_path MWP] [--train_mode 'resume']

```

Note : `--model_weights_path MWP`     path of model checkpoint include checkpoint itself eg; /path/to/checkpoint.pth

optional arguments hyperparameter can be adjust or remain the same.


