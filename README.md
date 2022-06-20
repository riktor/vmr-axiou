vmr-axiou
==============================

Codes of our paper "AxIoU: Axiomatically Justified Measure for Video Moment Retrieval" (CVPR'22)

## Dependencies
```shell
$ pip install -r requirements.txt
$ python -m spacy download en_core_web_sm
```
All codes are tested with Python3.7.7.

## Data

### Charades-STA

1. Download [Charades annotations](http://ai2-website.s3.amazonaws.com/data/Charades.zip) and save `Charades_v1_train.csv` and `Charades_v1_test.csv` in `data/raw/charades/`.
2. Download [Charades-STA annotations](https://github.com/jiyanggao/TALL#charades-sta-anno-download). Only train and test annotation files are required.

```
├── data
│   ├── processed
│   └── raw
        └── charades
            └──Charades_v1_train.csv
            └──Charades_v1_test.csv
            └──charades_sta_train.txt
            └──charades_sta_test.txt
```

Then run these commands below:

```shell
$ python src/data/make_dataset data/raw/charades/charades_sta_train.txt data/raw/charades/Charades_v1_train.csv 
$ python src/data/make_dataset data/raw/charades/charades_sta_test.txt data/raw/charades/Charades_v1_test.csv
```

## Dataset for Model Selection Experiments
We provide the dataset for our model selection experiments, which includes the snapshots of model predicitons for each epoch of model training with 5 different hyper-parameters. 
1. Download the dataset (exp-*.tar.gz) from the [release](https://github.com/riktor/vmr-axiou/releases/tag/dataset)
2. Decompress them into data/validation_exp/exp-*


### ActivityNet Captions
Download annotations [here](https://cs.stanford.edu/people/ranjaykrishna/densevid/captions.zip) and save `train.json`, `val_1.json` and `val_2.json` in `data/raw/activitynet/`.

```
├── data
│   ├── processed
│   └── raw
        └── activitynet
            └──train.json
            └──val_1.json
            └──val_2.json
```

## Evaluate your model's outputs

`src/toolbox` provides tools for evaluation and visualization of moment retrieval.
For example, evaluation on Charades-STA is done as:

```python
from src.toolbox.data_converters import CharadesSTA2Instances
from src.toolbox.eval import recall, axiou, accumulate_metrics

test_data = CharadesSTA2Instances(
    pd.read_csv(f"data/processed/charades/charades_test.csv")
)
############################
## your prediction code here
## ....
############################

recall_results = recall(test_data, predictions)
recall_summary = accumulate_metrics(recall_results)

axiou_results = axiou(test_data, predictions)
axiou_summary = accumulate_metrics(axiou_results)
```
`predictions` is a list of model's output.
Each item should be in the format as:
```
(
 (video_id: str, description: str),
 List[(moment_start: float, moment_end: float, video_duration: float)],
 List[rating: float]
)
```
- `video_id`: video ID
- `description`: a query sentence. 
- `moment_start`: a starting point of predicted moment's location in seconds
- `moment_end`: a end point of predicted moment's location in seconds
- `video_duration`: the duration of a whole video in seconds.
- `rating`: a score of a predicted location. A prediction with the largest `rating` is evaluated as top-1 prediction.

For example, an item in `predictions` is like:
```
predictions[0]

(('3MSZA', 'person turn a light on.'),
 [[0.76366093268685, 7.389522474042329, 30.96],
  [21.86557223053205, 29.71737331263709, 30.96],
  ...
  ],
 [7.252954266982226,
  4.785879048072588,
  ...])
```

`summary` is a dictionary of metrics (e.g. R@k (IoU>m) and AxIoU@k).
Examples of how to use our toolbox are in the notebook of our reproducible experiments (`notebooks/ExperimentsOnCharadesSTA.ipynb`).

If this work helps your research, please cite:
```
@InProceedings{Togashi_2022_CVPR,
    author    = {Togashi, Riku and Otani, Mayu and Nakashima, Yuta and Rahtu, Esa and Heikkil\"a, Janne and Sakai, Tetsuya},
    title     = {AxIoU: An Axiomatically Justified Measure for Video Moment Retrieval},
    booktitle = {Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
    month     = {June},
    year      = {2022},
    pages     = {21076-21085}
}
```

--------

<p><small>Project based on the <a target="_blank" href="https://drivendata.github.io/cookiecutter-data-science/">cookiecutter data science project template</a>. #cookiecutterdatascience</small></p>
