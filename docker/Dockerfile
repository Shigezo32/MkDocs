# python のバージョンを変更する場合は以下のURLからタグを確認してください
# https://hub.docker.com/_/python
FROM python:3.7-slim

# python 実行環境のお作法的なもの
RUN apt-get update
RUN apt-get -y install locales && \
    localedef -f UTF-8 -i ja_JP ja_JP.UTF-8
ENV LANG ja_JP.UTF-8
ENV LANGUAGE ja_JP:ja
ENV LC_ALL ja_JP.UTF-8
ENV TZ JST-9
ENV TERM xterm

RUN apt-get install -y curl
RUN apt-get update && apt-get install -y git
RUN apt-get install -y vim less
RUN pip install --upgrade pip

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive

# mkdocs のインストール
ADD requirements.txt /work_dir/
WORKDIR /work_dir
RUN pip install -r requirements.txt
RUN pip install git+https://github.com/srymh/MkdocsTagPlugin.git#egg=mdoctag

WORKDIR /docs

EXPOSE 8000

# 起動時に自動でサーバー実行する場合
ENTRYPOINT ["mkdocs"]
CMD ["serve", "--dev-addr=0.0.0.0:8000"]