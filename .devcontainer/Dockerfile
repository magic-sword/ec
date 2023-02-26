FROM nvcr.io/nvidia/pytorch:23.01-py3

# sudoを利用できるようにする
RUN apt update
RUN apt install sudo

# software-properties-commonなどをインストール時にタイムゾーンを聞かれて停止するため、タイムゾーンをインストールしておく
RUN DEBIAN_FRONTEND=noninteractive apt install -y tzdata

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

RUN opam install depext --yes
RUN opam depext ppx_jane core re2 yojson vg cairo2 camlimages menhir ocaml-protoc zmq --yes
RUN opam install ppx_jane core re2 yojson vg cairo2 camlimages menhir ocaml-protoc zmq --yes

# jbuilder clean    jbuilder: command not found

