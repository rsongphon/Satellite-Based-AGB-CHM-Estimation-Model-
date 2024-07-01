+++
title = 'Model Evaluation'
date = 2024-02-08T14:10:34+07:00
weight = 2
+++

#### Manually evaluate candidate ensemble

Nomally, in the training process. validation will run at the end of every epoch

`trainer.py`
```
# Start training
for epoch in range(self.start_epoch,self.args.nb_epoch):
    epoch += 1
    print('Epoch: {} / {} '.format(epoch, self.args.nb_epoch))

    # optimize parameters
    training_metrics = self.optimize_epoch(dl_train , epoch)
    # validated performance
    val_dict, val_metrics = self.validate(dl_val)


    # -------- LOG TRAINING METRICS --------
    ....

    # -------- LOG VALIDATION METRICS --------
    ....

    # Save Checkpoint weight

     .....

```

And at the end of training process, test step(with test dataset) is run to produce evaluation result in `--out_dir` directory along with **confusion plot** name `confusion.png`

Trainer will skip testing process if `confusion.png` is already exists.

But if you want to run the test manually or use with specific checkpoint to observe result follow this step 

1. Create empty dirctory and move checkpoint here, rename as `best_weights.pt`
2. Use argument `--skip_training  True` and specify path of directory with `--out_dir`

```
python3 train.py [--train_input_data_dir TRAIN_DIR] [--train_label_data_dir GT_DIR] [--all_checkpoint_dir CKP_DIR] [--skip_training True]  [--out_dir MODEL_WEIGHT_PATH]

```


#### Evaluate ensemble model

To evalute the efficiency of all candidate ensemble with test set. We need to calculate avarage output of all model along with total variance as describe in [uncertainty](/model/methodology/uncertain/) section.

Follow these step

1. Produce evaluation result for each candidat ensemble model. Each model must have `predictions.npy` and `variances.npy` to calculate output of ensemble and total variance

2. Create a empty directory and place each model's directory contain evaluation result from 1. Rename each model directory to `model_i` , **subscript i with number of candiate (1 <= i <= n)**

Directory hierarchy must be follow : example : parent directory name is 'ensemble_best_weight'  and candidate ensemble n = 5

```
/ensemble_best_weight
    /model_1
        /log
        best_weights.pt
        confusion.png
        predictions.npy
        results.txt
        targets.npy
        test_results.json
        variances.npy
    /model_2
        ...
    /model_3
        ....
    /model_4
        ....
    /model_5
        ....

```


3. Collect ensemble result, use command 

```
python3 collect_ensembles.py [WEIGHTS_DIRS]  [N]

```
Specify directory contain candidate ensemble in args[1] and number of candidate ensembles in args[2]

```
arguments:
WEIGHTS_DIRS        absolute path of model ensemble weight's collection parent directory
N                   total number of candidate ensemble
```

The script will creates:

1) a new subdirectory in e.g. "/experiment_dir/ensemble" # for futher implementation of K-fold cross validation,
2) a new subdirectory in the experiment base directory e.g. "/experiment_dir/ensemble_collected", containing the collected ensemble predictions and results


 ensemble_collected directory contains

- Aleatoric Uncertainty on test set
- Epistemic Uncertainty on test set
- error_metrics.json contain evaluation result
- Calibration plot
- Confusion plot

![confusion](confusion.png?height=300px)
> Figure : Example of confusion plot

![calibration](calibration.png?height=300px)
> Figure : Example of calibration plot