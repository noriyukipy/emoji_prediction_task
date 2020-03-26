FROM pytorch/pytorch:1.3-cuda10.1-cudnn7-runtime

RUN apt-get update && \
    apt-get install -y wget unzip language-pack-ja mecab libmecab-dev mecab-ipadic-utf8 jq

ENV LC_ALL=ja_JP.UTF-8

WORKDIR /work
RUN pip install jupyter==1.0.0 tensorboardX==2.0 papermill==1.2.1
