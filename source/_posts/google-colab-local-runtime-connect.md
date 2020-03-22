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
docker run  -d --gpus all -v /tf:/tf --net host --name colab  <image>:<tag>
```

## Colab Connect

If the host running jupyter notebook is not localhost, use port-forwarding as follows.

```
ssh user@example.com -L 8888:localhost:8888
```

## Reference

- https://jupyter.org/install
- https://research.google.com/colaboratory/local-runtimes.html
- https://dev.to/ikeyasu/how-to-use-google-colaboratory-with-local-runtime-4j1p
- https://datasainslab.com/local-runtime-on-google-colab/
