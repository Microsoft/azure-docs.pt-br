---
title: Aprendizado profundo & estruturas de ia
titleSuffix: Azure Data Science Virtual Machine
description: Estruturas e ferramentas de aprendizado profundo disponíveis no Azure Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 43a8004b6cdfe2e074304569b83360101784a87e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567994"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Estruturas de aprendizado profundo e de ia para o Azure VM de Ciência de Dados
Estruturas de aprendizado profundo no DSVM estão listadas abaixo.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | |
| Edições DSVM com suporte      | Ubuntu 16.04    |
| Como é configurado/instalado no DSVM?  | O Caffe é instalado em `/opt/caffe`.   Os exemplos estão em `/opt/caffe/examples` .|
| Como executá-lo      | Use o X2Go para entrar em sua VM e, em seguida, inicie um novo terminal e insira o seguinte:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Uma nova janela do navegador é aberta com blocos de anotações de exemplo. Binários são instalados em /opt/caffe/build/install/bin.<br/><br/>A versão instalada do Caffe requer o Python 2,7 e não funcionará com o Python 3,6, que é ativado por padrão. Para alternar para o Python 2,7, execute `source activate root` para alternar para o ambiente Anaconda.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | |
| Edições DSVM com suporte      | Ubuntu 16.04     |
| Como é configurado/instalado no DSVM?  | O Caffe2 é instalado no ambiente do [Python 2,7 (raiz) Conda. |
| Como executá-lo      | Terminal: Inicie o Python e importe Caffe2. <br/> * JupyterHub: [Conecte-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e vá para o diretório Caffe2 para encontrar blocos de anotações de exemplo. Alguns blocos de anotações exigem que a raiz Caffe2 seja definida no código Python; insira /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | 5.2 |
| Edições DSVM com suporte      | Ubuntu 16.04    |
| Como é configurado/instalado no DSVM?  | O Chainer é instalado em Python 3.5. |
| Como executá-lo      | Terminal: Ative o ambiente Python 3,5, execute `python` e, em seguida `import chainer` . <br/> * JupyterHub: [Conecte-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e, em seguida, vá para o diretório do encadeamento para encontrar blocos de anotações de exemplo.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, driver NVIDIA](https://developer.nvidia.com/cuda-toolkit)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | 10.0.130|
| Edições DSVM com suporte      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04  |
| Como é configurado/instalado no DSVM?  |_nvidia-smi_ está disponível no caminho do sistema.  |
| Como executá-lo      | Abra um prompt de comando (no Windows) ou um terminal (no Linux) e, em seguida, execute _NVIDIA-SMI_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | 0.16.1|
| Edições DSVM com suporte      | Ubuntu 18.04<br> Ubuntu 16.04   |
| Como é configurado/instalado no DSVM?  | O Horovod é instalado no Python 3,5 |
| Como executá-lo      | Ative o ambiente correto no terminal e execute o Python. |

## <a name="keras"></a>[Keras](https://keras.io/)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | 2.2.4 |
| Edições DSVM com suporte      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04   |
| Como é configurado/instalado no DSVM?  | O Keras é instalado no Python 3,6 no Windows e no Python 3,5 no Linux |
| Como executá-lo      | Ative o ambiente correto no terminal e execute o Python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[CNTK (Microsoft Cognitive Toolkit)](/cognitive-toolkit/)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | 2.5.1 |
| Edições DSVM com suporte      | Windows 2016 <br> Ubuntu 16.04   |
| Como é configurado/instalado no DSVM?  | O CNTK é instalado no Python 3,6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e no Python 3,5 no [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Como executá-lo      | Terminal: Ative o ambiente correto e execute o Python. <br/>Jupyter: Conecte-se ao [Jupyter](provision-vm.md) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e, em seguida, abra o diretório CNTK para obter exemplos. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | 1.3.0 |
| Edições DSVM com suporte      | Windows 2016 <br> Ubuntu 16.04    |
| Como é configurado/instalado no DSVM?  | O MXNet é instalado no `C:\dsvm\tools\mxnet` no Windows e `/dsvm/tools/mxnet` no Ubuntu. As associações do Python são instaladas no Python 3,6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e no Python 3,5 no [Linux](./dsvm-tools-languages.md#python-linux-edition)) as associações do R também estão incluídas no Ubuntu DSVM. |
| Como executá-lo      | Terminal: Ative o ambiente Conda correto e, em seguida, execute `import mxnet` . <br/>Jupyter: Conecte-se ao [Jupyter](provision-vm.md#access-the-dsvm) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e, em seguida, abra o `mxnet` diretório para obter exemplos. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | 1.0.1 |
| Edições DSVM com suporte      | Windows 2016 <br> Ubuntu 16.04    |
| Como é configurado/instalado no DSVM?  | O MXNet Model Server está instalado no Python 3,6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e no Python 3,5 no [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Como executá-lo      | Terminal: execute `sudo systemctl stop jupyterhub` para interromper o serviço JupyterHub primeiro, pois ambos escutam na mesma porta. Em seguida, ative o ambiente Conda correto e execute `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[Interface de gerenciamento do sistema NVidia (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte |  |
| Edições DSVM com suporte      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04   |
| Para que ele serve? | Ferramenta NVIDIA para consultar a atividade da GPU |
| Como é configurado/instalado no DSVM?  | `nvidia-smi` está no caminho do sistema. |
| Como executá-lo      | Em uma máquina virtual **com GPU**, abra um prompt de comando (no Windows) ou um terminal (no Linux) e, em seguida, execute `nvidia-smi` . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | 1.2.0 (Ubuntu 16, 4), 1.7.1 (Ubuntu 18, 4, Windows 2019) |
| Edições DSVM com suporte      | Windows Server 2019<br>Ubuntu 18.04<br> Ubuntu 16.04 |
| Como é configurado/instalado no DSVM?  | Instalado no [Python 3,7](dsvm-tools-languages.md#python-linux-edition) no ambiente do py37_pytorch Conda. Os notebooks Jupyter de exemplo estão incluídos e os exemplos estão em/dsvm/Samples/pytorch. |
| Como executá-lo      | Terminal: Ative o ambiente correto e execute Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Conecte-se e, em seguida, abra o diretório PyTorch para obter exemplos.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | 1.13 |
| Edições DSVM com suporte      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04 |
| Como é configurado/instalado no DSVM?  | Instalado no Python 3,5 no [Linux](dsvm-tools-languages.md#python-linux-edition) e Python 3,6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Como executá-lo      | Terminal: Ative o ambiente correto e execute Python. <br/> * Jupyter: Conecte-se ao [Jupyter](provision-vm.md) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e, em seguida, abra o diretório TensorFlow para obter exemplos.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/tfx/guide/serving)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | 1.12 |
| Edições DSVM com suporte      | Ubuntu 16.04 |
| Como é configurado/instalado no DSVM?  | tensorflow_model_server está disponível no terminal. |
| Como executá-lo      |  Exemplos estão disponíveis [online](https://www.tensorflow.org/tfx/guide/serving).   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

| Categoria | Valor |
| ------------- | ------------- |
| Versão (ões) com suporte | 1.0.3 |
| Edições DSVM com suporte      | Ubuntu 16.04 |
| Como é configurado/instalado no DSVM?  |O Theano é instalado no Python 2,7 (_raiz_) e no ambiente Python 3,5 (_py35_). |
| Como executá-lo      |  Terminal: Ative a versão do Python que você deseja (raiz ou py35), execute o Python e, em seguida, importe o Theano.<br/>* Jupyter: selecione o kernel Python 2,7 ou 3,5 e importe Theano.  <br/>Para contornar um bug recente da MKL (biblioteca de kernel matemática), primeiro você precisa definir a camada de Threading do MKL da seguinte maneira:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |
