# emoji


## タスク設定

### 元データ

Twitter

### タスク設定

文に対する多クラス分類問題として解く。

### 評価指標

- Precision (Top-1, 5)

## 準備

```sh
$ docker image build -t jupyter ./
```

## 実行

学習データセットの作成

```sh
$ docker container run -v $(pwd):/work --rm jupyter papermill dataset.ipynb output/dataset_out.ipynb -p tweet_file tweets.json -p test_valid_size_per_emoji 500 -p out_dir output
```

fastTextモデルの学習

```sh
$ cd model/fasttext
$ docker container run -v $(pwd):/work --rm jupyter papermill emoji_fasttext.ipynb output/output.ipyn
```
