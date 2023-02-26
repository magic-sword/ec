FROM nvcr.io/nvidia/pytorch:23.01-py3

# sudoを利用できるようにする
RUN apt update
RUN apt install sudo

# root以外のユーザを追加し、sudoをパスワードなしで実行可能にする
ARG USERNAME=dev
ARG GROUPNAME=dev
ARG UID=1000
ARG GID=1000
ARG PASSWORD=user
RUN groupadd -g $GID $GROUPNAME && \
    useradd -m -s /bin/bash -u $UID -g $GID -G sudo $USERNAME && \
    echo $USERNAME:$PASSWORD | chpasswd && \
    echo "$USERNAME   ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
USER $USERNAME

# software-properties-commonなどをインストール時にタイムゾーンを聞かれて停止するため、タイムゾーンをインストールしておく
# sudoでroot権限へ昇格するときに環境変数が引き継がれないので、sudoに-Eオプションを入れる
RUN DEBIAN_FRONTEND=noninteractive sudo -E apt install -y tzdata

# root権限となってしまっているフォルダとファイルの権限を、再帰的に全て変更するコマンド
# sudo chown -R dev:dev [filename/foldername]

# Getting Started -- Dependencies and Requirements
RUN sudo apt install -y software-properties-common
RUN sudo add-apt-repository --yes ppa:deadsnakes/ppa
RUN sudo apt update
RUN sudo apt install python3.8-dev
RUN pip install --upgrade pip
RUN pip install --upgrade setuptools

COPY requirements.txt .
RUN pip install -r requirements.txt
RUN echo "import nltk; nltk.download('punkt')" | python

# Build the OCaml binaries.
RUN sudo apt install -y ocaml
RUN sudo apt install -y opam

# WSLではopamのサンドボックスが利用できない
# 参考: https://tech.4423.ch/wsl-opam、　参考: https://github.com/ocaml/opam-repository/issues/12050
RUN opam init --disable-sandboxing --yes
RUN opam update --yes
RUN opam switch create 4.06.1+flambda --yes
RUN opam switch 4.06.1+flambda
RUN eval `opam config env`

# menhirはubuntu 20以降でエラーが発生するため、バージョンを指定してインストールする (https://github.com/ellisk42/ec/issues/96)
RUN opam install depext --yes
RUN opam depext ppx_jane core re2 yojson vg cairo2 camlimages menhir.20211128 ocaml-protoc zmq --yes
RUN opam install ppx_jane core re2 yojson vg cairo2 camlimages menhir.20211128 ocaml-protoc zmq --yes
RUN eval $(opam env)

# jbuilder clean    jbuilder: command not found

