---
title: "Estruturas de Aprendizado Profundo e AI – Azure | Microsoft Docs"
description: Estruturas de Aprendizado Profundo e de AI
keywords: "ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 46662c15374f781a6527bb1435d883ae060c63f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deep-learning-and-ai-frameworks"></a>Estruturas de Aprendizado Profundo e de AI
A DSVM ([Máquina Virtual de Ciência de Dados](http://aka.ms/dsvm)) e a [VM de Aprendizado Profundo](http://aka.ms/dsvm/deeplearning) dão suporte a várias estruturas de aprendizado profundo para ajudar a criar aplicativos de AI (Inteligência Artificial) com funcionalidades cognitivas e análise preditiva como entendimento de imagem e linguagem. 

Aqui estão os detalhes sobre todas as estruturas de aprendizado profundo sobre na DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Kit de Ferramentas Cognitivas da Microsoft

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | O CNTK (Kit de Ferramentas Cognitivas Microsoft) é instalado em Python 2.7 no ambiente _raiz_, bem como Python 3.5, no ambiente _py35_.   |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter estão incluídos.     |
| Ferramentas relacionadas ao DSVM      | Keras      |
| Como usar/executar?    | Abra o Jupyter e, em seguida, procure a pasta CNTK  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | No Linux, o TensorFlow é instalado em Python 2.7 (_raiz_), bem como no ambiente Python 3.5 (_py35_). No Windows, o Tensorflow é instalado no ambiente Python 3.5 (_py35_).  |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter estão incluídos.     |
| Ferramentas relacionadas ao DSVM      | Keras      |
| Como usar/executar?    | Abra o Jupyter e, em seguida, procure a pasta TensorFlow.  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | O Keras é instalado em Python 2.7 (_raiz_), bem como no ambiente Python 3.5 (_py35_).   |
| Links para exemplos      | https://github.com/fchollet/keras/tree/master/examples      |
| Ferramentas relacionadas ao DSVM      | Kit de Ferramentas Cognitivas Microsoft, TensorLlow, Theano      |
| Como usar/executar?    | Baixe os exemplos do Github local, copie-os para um diretório em ~/notebooks e abra-os em Jupyter   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Linux     |
| Como é configurado/instalado no DSVM?  | O Caffe é instalado em `/opt/caffe`.    |
| Links para exemplos      | Os exemplos são incluídos no `/opt/caffe/examples`.      |
| Ferramentas relacionadas ao DSVM      | Caffe2      |
### <a name="how-to-use--run-it"></a>Como usar/executar?  

Use X2Go para fazer logon em sua VM e, em seguida, inicie um novo terminal e insira

```
cd /opt/caffe/examples
jupyter notebook
```

Uma nova janela do navegador é aberta com blocos de anotações de exemplo.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Linux     |
| Como é configurado/instalado no DSVM?  | O Caffe2 é instalado em `/opt/caffe2`. Ele também está disponível para o ambiente conda Python 2.7 (_raiz_).     |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter estão incluídos     |
| Ferramentas relacionadas ao DSVM      | Caffe      |
| Como usar/executar?    | Abra o Jupyter e, em seguida, navegue até o diretório Caffe2 para localizar os blocos de anotações de exemplo. Alguns blocos de anotações exigem que a raiz Caffe2 seja definida no código Python; insira /opt/caffe2.   |


## <a name="chainer"></a>Encadeador

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | O Chainer é instalado em Python 2.7 (_raiz_), bem como no ambiente Python 3.5 (_py35_). ChainerRL e ChainerCV também são instalados.   |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter estão incluídos.      |
| Ferramentas relacionadas ao DSVM      | Caffe      |

### <a name="how-to-use--run-it"></a>Como usar/executar?  

Em um terminal, ative a versão do Python que deseja (_raiz_ ou _py35_), execute o _python_ e importe o Chainer. No Jupyter, selecione o kernel Python 2.7 ou 3.5 e importe o Chainer.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo para H2O      |
| Edições do DSVM com suporte      | Linux     |
| Como é configurado/instalado no DSVM?  | Deep Water é instalado em `/dsvm/tools/deep_water`.   |
| Links para exemplos      | Exemplos estão disponíveis por meio do servidor Deep Water.      |
| Ferramentas relacionadas ao DSVM      | H2o, Sparkling Water      |

### <a name="how-to-use--run-it"></a>Como usar/executar?  

Conectar-se à VM usando X2Go. Em um terminal, inicie o servidor Deep Water:

    java -jar /dsvm/tools/deep_water/h2o.jar

Em seguida, abra um navegador e conecte-se ao `http://localhost:54321`.



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | MXNet é instalado em `C:\dsvm\tools\mxnet` no Windows e `/dsvm/tools/mxnet` no Linux. Associações do Python são instaladas em Python 2.7 (_raiz_), bem como no ambiente Python 3.5 (_py35_). Associações do R também são instaladas.   |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter estão incluídos.    |
| Ferramentas relacionadas ao DSVM      | Keras      |
| Como usar/executar?    | Abra Jupyter e, em seguida, procure a pasta mxnet  |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| O que é?   | Sistema de aprendizado profundo da NVIDIA para treinar rapidamente modelos de aprendizado profundo      |
| Edições do DSVM com suporte      | Linux     |
| Como é configurado/instalado no DSVM?  | DIGITS está instalado em `/dsvm/tools/DIGITS` e disponível como um serviço, chamado _digits_.   |
### <a name="how-to-use--run-it"></a>Como usar/executar?  

Faça logon na VM com X2Go. Em um terminal, inicie o serviço:

    sudo systemctl start digits

O serviço demora aproximadamente um minuto para iniciar. Abra um navegador da Web e navegue até `http://localhost:5000`.



## <a name="nvdia-smi"></a>nvidia-smi

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta NVIDIA para consultar a atividade da GPU      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | _nvidia-smi_ está disponível no caminho do sistema.   |
| Como usar/executar? | Inicie um prompt de comando (no Windows) ou um terminal (no Linux) e, em seguida, execute _nvidia-smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Linux     |
| Como é configurado/instalado no DSVM?  | O Theano é instalado em Python 2.7 (_raiz_), bem como no ambiente Python 3.5 (_py35_).   |
| Ferramentas relacionadas ao DSVM      | Keras      |
| Como usar/executar?    | Em um terminal, ative a versão do Python que deseja (raiz ou py35), execute o python e importe o theano. No Jupyter, selecione o kernel Python 2.7 ou 3.5 e importe o theano.  |



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Linux     |
| Como é configurado/instalado no DSVM?  | O Torch é instalado em `/dsvm/tools/torch`. O PyTorch é instalado em Python 2.7 (_raiz_), bem como no ambiente Python 3.5 (_py35_).   |
| Links para exemplos      | Exemplos de Torch estão localizados em `/dsvm/samples/torch`. Exemplos de PyTorch estão localizados em `/dsvm/samples/pytorch`.      |

