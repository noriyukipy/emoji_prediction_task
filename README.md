# emoji

文に対する絵文字を推定する

### 元データ

Twitter

### タスク設定

文に対する多クラス分類問題として解く。

### 評価指標

- Top-1,5 accuracy

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

TensorBoardで結果を見る

```sh
$ docker container run -v $(pwd):/work -p 6006:6006 --rm -it jupyter bash -c 'pip install tensorboard && tensorboard --logdir runs --host=0.0.0.0'
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
$ cd model/bert
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/bert-tune_layer_classifier.ipynb -p data_dir data -p tune_layer classifier -p name bert-tune_layer_classifier
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/bert-tune_layer_last_and_classifier.ipynb -p data_dir data -p tune_layer last_and_classifier -p name bert-tune_layer_last_and_classifier
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/bert-tune_layer_all.ipynb -p data_dir data -p tune_layer all -p name bert-tune_layer_all
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/bert-tune_layer_all-warmup_rate_0.ipynb -p data_dir data -p tune_layer all -p warmup_rate 0 -p name bert-tune_layer_all-warmup_rate_0
```

### LSTM モデルの学習

```sh
$ cd model/lstm
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/lstm.ipynb -p data_dir "data" -p name lstm
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/lstm-bidirectional_True.ipynb -p data_dir "data" -p bidirectional True -p name lstm-bidirectional_True
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/lstm-num_layers_2.ipynb -p data_dir "data" -p num_layers 2 -p name lstm-num_layers_2
$ docker container run -v $(pwd):/work --gpus all --rm jupyter papermill model.ipynb output/lstm-num_layers_2-bidirectional_True.ipynb -p data_dir "data" -p bidirectional True -p num_layers 2 -p name lstm-num_layers_2-bidirectional_True
```

## テストデータでの評価

| Name | Model | Top-1 acc | Top-5 acc | notebook |
| --- | --- | --- | --- | --- |
| fastText | fastText | 0.1286 | 0.3204 | model/fasttext/output.ipynb |
| lstm | LSTM | 0.1345 | 0.3276 | model/lstm/output/lstm.ipynb |
| lstm-bidirectional_True | LSTM | 0.1331 | 0.3279 | model/lstm/output/lstm-bidirectional_True.ipynb |
| lstm-num_layers_2 | LSTM | 0.1431 | 0.3434 | model/lstm/output/lstm-num_layers_2.ipynb |
| lstm-num_layers_2-bidirectional_True | LSTM | 0.1436 | 0.3443 | model/lstm/output/lstm-num_layers_2-bidirectional_True.ipynb |
| bert-tune_layer_classifier | BERT | 0.0569 | 0.1774 | model/bert/output/bert-tune_layer_classifier.ipynb |
| bert-tune_layer_last_and_classifier | BERT | 0.1456 | 0.3518 | model/bert/output/bert-tune_layer_last_and_classifier.ipynb |
| bert-tune_layer_all | BERT | 0.1441 | 0.3547 | model/bert/output/bert-tune_layer_all.ipynb |
| bert-tune_layer_all-warmup_rate_0 | BERT | 0.1466 | 0.3584 | model/bert/output/bert-tune_layer_all-warmup_rate_0.ipynb |