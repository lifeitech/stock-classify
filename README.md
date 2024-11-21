# stock-classify

![](snap.png)

This is a data science project that performs binary classification on all US stocks. The goal is to compare traditional machine learning approach and deep learning approach for time series classification.

A stock ticket is labeled as "1" (positive) if its price have "fallen to the ground", whatever that means, and labeled as "0" (negative) otherwise. Such low price stocks are very risky. But price patterns can be very diverse, so it is difficult to filter such stocks by any fixed rule. We manually labeled 414 positive tickets and 616 negative tickets that are randomly chosen from a pool of 6000+ tickets, for a total of 1030 labeled tickets. Labels are stored in the `label.json` file.

## Workflow
### setup
download the repository, create a new python envrionment, and install dependencies.
```shell
pip install -r requirements.txt
```

### I.data
1️⃣ download historical day-level price data for all US stocks. The list of tickets (`data/tickets.txt`) comes from nasdaq, and may not be 100% complete or up-to-date.
```shell
cd data
python download.py
```
Each ticket history will be downloaded as a separate csv file in `data/csv` folder. You can use `--num=1000` flag to download data for only certain number of tickets at a time.

2️⃣ plot all the data.
```shell
python plot.py
```
The plots will be saved in the `data/plots` folder.

### II.machine learning
1️⃣ extract features from data 
```shell
cd ml
python extract_features.py
```
the output will be two csv tables in the `ml` folder, one for labeled tickets and one for unlabeled tickets.

2️⃣ fit model
```shell
python fit.py --model=lr
```
Available models:
- `--model=lr` for logistic regression (default)
- `--model=tree` for decision trees
- `--model=boost` for gradient boosting

You should be able to get around 95% test accuracy. Model file will be saved as `model.pkl` in the same folder.

3️⃣ classify 5000+ unlabeled tickets
```shell
python pred.py
```
Prediction will be saved to a `prediction.json` file.

### III.deep learning
1️⃣ prepare data for training with
```shell
cd cnn
python prepare.py
```
The output is two csv files "training_data.csv" and "unlabeled_data.csv" in the `cnn` folder.

2️⃣ train the model with
```shell
python train.py
```
[wandb](https://wandb.ai/) is used for logging with project name "stock-cls", so before training please create an account and login. Otherwise, you can comment out the logger variable in `train.py`. After training, model weights will be saved to `cnn/stock-cls/[some-name]/checkpoints/` folder.

3️⃣ classify unlabeled tickets with
```shell
python pred.py
```
The output will be a `prediction.json` file in the `cnn` folder.

## Remarks
1. In real situations, defining the problem is often hard. If you have _precise_ definition of your problem, then you already solved it.
2. No feature can perfectly separate all categories. Otherwise, this single feature can be used as a classifier.
3. Machine learning approach is very explainable. It can even let you discover errors in data labeling by examing individual features.
4. Deep learning models have powerful representations, you can instantly acheive high accuracy without going through feature engineering. 
5. However, setting up and training neural networks is a heavy process. Training can be unstable, and performance is also sensitive to hyperparameters. Yet hyperparameter tuning wouldn't give you much insights to your original problem. 