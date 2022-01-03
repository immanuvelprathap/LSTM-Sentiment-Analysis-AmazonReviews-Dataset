# LSTM based Sentiment Classification using Tensorflow - Amazon Reviews Rating (Dataset)

<!-- ![Build Status] -->

The dataset is from Amazon Review Data (2018) https://nijianmo.github.io/amazon/index.html.

- I here look at Cell Phones and Accessories review dataset for experimentation. 
- I have pre-processed this dataset in Jupyter Notebook, please find the code in the Preprocessing Folder
- The script for pre-processing is present here: https://github.com/immanuvelprathap/LSTM-Sentiment-Analysis-AmazonReviews-Dataset/tree/main/Preprocessing

## Directory Tree

```
├── Data
│   ├── CellPhonesRating.csv
│   └── tokenizer.json
├── Images
│   ├── Architecture_LSTM_Model.png
│   ├── The_LSTM_cell.png
│   └── multi_input_and_output_model.png
├── Model
│   ├── LSTM_Model.ipynb
│   └── tf_LSTM_model.h5
├── Preprocessing
│   └── Preprocessing - AmazonReviews Dataset.ipynb
├── README.md
└── gitattributes
```


## Installation - Dependencies

```python
import matplotlib as mpl
import matplotlib.pyplot as plt
import os
import re
import shutil
import string
import tensorflow as tf
from tensorflow.keras import regularizers

from tensorflow.keras import layers
from tensorflow.keras import losses

from collections import Counter


import pandas as pd
import numpy as np

import sklearn


from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

from tensorflow.keras import preprocessing
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences


import seaborn as sns

import pydot
```

## Technologies Used

![](https://forthebadge.com/images/badges/made-with-python.svg)

<img target="_blank" src="https://miro.medium.com/max/1400/1*-QTg-_71YF0SVshMEaKZ_g.png" width=200>

<img target="_blank" src="https://keras.io/img/logo.png" width=200>

<img target="_blank" src="data:image/png;base64,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" width=200>


## Team

[![Immanuvel Prathap](https://avatars.githubusercontent.com/u/68032323?v=4)](https://immanuvelprathap.in/) |
-|
[Immanuvel Prathap's Website - Click Here!](https://immanuvelprathap.in/) |)

## License

## Credits

## References for researcher

 
