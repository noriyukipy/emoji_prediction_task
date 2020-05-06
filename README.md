# emoji

## ワークフロー

### 問題の定義

文に対して、適切な絵文字を推定する

入出力

- 入力: 文
- 出力: 絵文字のラベル

問題の種類

- 多クラス単一ラベル分類

### データセットの作成

- 均衡データ

### 指標の選択

- Recall@k (正解ラベルが一つなので、top-k accuracyと一致)

### 評価プロトコル

ホールドアウト法による検証を実行

### 残り

あとのワークフローは各 notebook で行う

- データの準備
- ベースラインを超えるモデルの開発
- 過学習するモデルの開発
- モデルの正則化とハイパーパラメータチューニング

## 準備

```sh
$ cd docker
$ docker image build -t jupyter -f Dockerfile .
$ docker image build -t keras -f Dockerfile.keras .
$ cd ..
```

Jupyter notebookの起動

```sh
$ docker container run -v $(pwd):/work -w /work -p 8888:8888 --rm keras jupyter notebook --ip 0.0.0.0 --allow-root

```

TensorBoardで結果を見る

```sh
$ docker container run -v $(pwd):/work -w /work -p 6006:6006 --rm -it keras tensorboard --logdir . --host=0.0.0.0
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

## Keras

```sh
$ docker container run -v $(pwd):/work -w /work --rm keras papermill --log-level WARNING model/keras/embedding_flatten_model/model.ipynb model/keras/embedding_flatten_model/output/output.ipynb -f model/keras/embedding_flatten_model/params.yaml
```

### TODO

- Process unknow word