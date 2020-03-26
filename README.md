# emoji

## タスク設定

### 元データ

Twitter

### タスク設定

文に対する多クラス分類問題として解く。

### 評価指標

- Top-5 accuracy
- Top-1 accuracy

## 準備

```sh
$ cd docker
$ docker image build -t jupyter ./
$ cd ..
```

Jupyter notebookの起動

```sh
$ docker container run -v $(pwd):/work -p 8888:8888 --rm jupyter jupyter notebook --ip 0.0.0.0 --allow-root
```

## 実行

学習データセットの作成

```sh
$ docker container run -v $(pwd):/work --rm jupyter papermill dataset.ipynb output/dataset_out.ipynb -p tweet_file tweets.json -p test_valid_size_per_emoji 500 -p out_dir output
```

### fastTextモデルの学習

```sh
$ cd model/fasttext
$ docker container run -v $(pwd):/work --rm jupyter papermill emoji_fasttext.ipynb output/output.ipynb
```

### BERTモデルの学習

学習


```sh
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/bert-tune_layer_classifier.ipynb -p data_dir data -p tune_layer classifier -p name bert-tune_layer_classifier
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/bert-tune_layer_last_and_classifier.ipynb -p data_dir data -p tune_layer last_and_classifier -p name bert-tune_layer_last_and_classifier
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/bert-tune_layer_all.ipynb -p data_dir data -p tune_layer all -p name bert-tune_layer_all
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/bert-tune_layer_all-warmup_rate_0.ipynb -p data_dir data -p tune_layer all -p warmup_rate 0 -p name bert-tune_layer_all-warmup_rate_0
```

TensorBoardで結果を見る

```sh
$ docker container run -v $(pwd):/work -p 6006:6006 --rm -it jupyter bash -c 'pip install tensorboard && tensorboard --logdir runs --host=0.0.0.0'
```