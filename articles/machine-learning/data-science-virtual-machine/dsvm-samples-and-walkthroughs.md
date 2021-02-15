---
title: Exemplos de programas & instruções do ML
titleSuffix: Azure Data Science Virtual Machine
description: Por meio desses exemplos e orientações, saiba como lidar com as tarefas e cenários comuns com o Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: cda5dfd936243602775e1f4f965032b9d746b0b7
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519755"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Exemplos em máquinas virtuais de ciência de dados do Azure

As DSVMs (máquinas virtuais de ciência de dados) do Azure incluem um conjunto abrangente de códigos de exemplo. Esses exemplos incluem blocos de anotações e scripts do Jupyter em linguagens como Python e R.
> [!NOTE]
> Para obter mais informações sobre como executar notebooks Jupyter em suas máquinas virtuais de ciência de dados, consulte a seção [Access Jupyter](#access-jupyter) .

## <a name="prerequisites"></a>Pré-requisitos

Para executar esses exemplos, você deve ter provisionado um [máquina virtual de ciência de dados Ubuntu](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Exemplos disponíveis
| Categoria de exemplos | Descrição | Locais |
| ------------- | ------------- | ------------- |
| Linguagem R  | Os exemplos ilustram cenários como a conexão com armazenamentos de dados de nuvem baseados no Azure e como comparar o R e o Microsoft Machine Learning Server de código aberto. Eles também explicam como colocar em operação modelos em Microsoft Machine Learning Server e SQL Server. <br/> [Linguagem R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguagem Python  | Os exemplos explicam cenários como a conexão com armazenamentos de dados de nuvem baseados no Azure e como trabalhar com Azure Machine Learning.  <br/> [Linguagem Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Linguagem Julia  | Fornece uma descrição detalhada de plotagem e aprendizado profundo em Julia. Também explica como chamar C e Python de Julia. <br/> [Idioma Julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Ilustra como criar modelos de aprendizado de máquina e aprendizado aprofundado com o Machine Learning. Implante modelos em qualquer lugar. Use aprendizado de máquina automatizado e ajuste de hiperparâmetro inteligente. Use também o gerenciamento de modelos e treinamento distribuído. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notebooks do PyTorch  | Exemplos de aprendizado aprofundado que usam redes neurais baseadas em PyTorch. Os notebooks vão de cenários iniciantes a avançados.  <br/> [Notebooks do PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Uma variedade de exemplos de rede neural e técnicas implementadas usando a estrutura TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Kit de Ferramentas Cognitivas da Microsoft <br/>   | Exemplos de aprendizado aprofundado publicados pela equipe de Cognitive Toolkit na Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Exemplos de aprendizado aprofundado que usam redes neurais baseadas em Caffe2. Vários notebooks familiarizam os usuários com o Caffe2 e como usá-lo com eficiência. Os exemplos incluem o pré-processamento de imagem e a criação de conjunto de imagens. Eles também incluem a regressão e como usar modelos pretreinados. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Exemplos baseados em Python que usam H2O para cenários de problemas do mundo real. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Linguagem SparkML  | Exemplos que usam recursos do Apache Spark Toolkit MLLib por meio de pySpark e MMLSpark: Microsoft Machine Learning para Apache Spark no Apache Spark 2. x.  <br/> [Linguagem SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Exemplos de aprendizado de máquina padrão no XGBoost para cenários como classificação e regressão. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Acessar o Jupyter 

Para acessar o Jupyter, selecione o ícone de **Jupyter** no menu da área de trabalho ou do aplicativo. Você também pode acessar o Jupyter em uma edição do Linux de um DSVM. Para acessar remotamente a partir de um navegador da Web, vá para `https://<Full Domain Name or IP Address of the DSVM>:8000` no Ubuntu.

Para adicionar exceções e tornar o Jupyter acesso disponível em um navegador, use as seguintes diretrizes:


![Habilitar exceção do Jupyter](./media/ubuntu-jupyter-exception.png)


Entre com a mesma senha que você usa para fazer logon no Máquina Virtual de Ciência de Dados.
<br/>

**Página inicial do Jupyter**
<br/>![Página inicial do Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Linguagem R 
<br/>![Exemplos de R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguagem Python
<br/>![Exemplos em Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Linguagem Julia 
<br/>![Exemplos de Julia](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Exemplos do Azure Machine Learning](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Exemplos do PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Exemplos de TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Exemplos do CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![Exemplos de caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Exemplos de H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Exemplos de SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Exemplos de XGBoost](./media/xgboost-samples.png)<br/>

