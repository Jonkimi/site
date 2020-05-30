---
title: google-colab-local-runtime-connect
tags: [colab, jupyter]
description: google-colab-local-runtime-connect
date: 2019-12-31 11:00:56
---

## Install jupyter

```shell
pip install jupyter jupyter_http_over_ws
jupyter serverextension enable --py jupyter_http_over_ws
jupyter notebook --ip=0.0.0.0 --no-browser --NotebookApp.token='' --NotebookApp.password='' --NotebookApp.allow_origin='https://colab.research.google.com' --NotebookApp.port_retries=0
```

or use docker image

```
docker run  -d --gpus all --ipc=host --user="$(id -u):$(id -g)" -v ${PWD}/hostdir:/tf --net host --name colab  <image>:<tag>
docker run  --rm --gpus all --ipc=host  -v ${PWD}/hostdir:/tf --net host --name colab
```

- --gpus=all: Required if using CUDA, optional otherwise. Passes the graphics cards from the host to the container. You can also more precisely control which graphics cards are exposed using this option (see documentation at https://github.com/NVIDIA/nvidia-docker).
- --ipc=host: Required if using multiprocessing, as explained at https://github.com/pytorch/pytorch#docker-image.
- --user="$(id -u):$(id -g)": Sets the user inside the container to match your user and group ID. Optional, but is useful for writing files with correct ownership.

```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
  sudo apt-key add -
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
  sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker
docker run --gpus all nvidia/cuda nvidia-smi
```

## Colab Connect

If the host running jupyter notebook is not localhost, use port-forwarding as follows.

```
ssh user@example.com -L 8888:localhost:8888
```

## Reference

- https://jupyter.org/install
- https://github.com/anibali/docker-pytorch
- https://research.google.com/colaboratory/local-runtimes.html
- https://dev.to/ikeyasu/how-to-use-google-colaboratory-with-local-runtime-4j1p
- https://datasainslab.com/local-runtime-on-google-colab/
