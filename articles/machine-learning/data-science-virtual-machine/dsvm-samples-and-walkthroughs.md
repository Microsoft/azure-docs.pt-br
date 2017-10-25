---
title: "Exemplos e instruções passo a passo para a Máquina Virtual de Ciência de Dados – Azure | Microsoft Docs"
description: "Exemplos e instruções passo a passo para a Máquina Virtual de Ciência de Dados."
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
ms.openlocfilehash: 89bb5d255db9ab266d04169a3101e2b694236029
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Exemplos de Máquinas Virtuais de Ciência de Dados (DSVM)

As DSVMs vêm com exemplos totalmente trabalhados incluídos na forma de Blocos de Anotações do Jupyter e alguns que não são baseados em Jupyter. Você pode acessar o Jupyter clicando no ícone `Jupyter` no menu do aplicativo ou da área de trabalho.  
> [!NOTE]
> Consulte a seção [Acessar o Jupyter](#access-jupyter) para habilitar os Blocos de Anotações na sua DSVM.

## <a name="quick-reference-of-samples"></a>Referência rápida de amostras
| Categoria de exemplos | Descrição | Locais |
| ------------- | ------------- | ------------- |
| Linguagem **R**  | Amostras em **R** explicando cenários como conectar-se aos armazenamentos de dados de nuvem do Azure, comparar R de Software Livre e R Microsoft e operacionalizar modelos no Microsoft R Server ou no SQL Server. <br/> [Captura de tela](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguagem **Python**  | Exemplos em **Python** explicando cenários como conectar-se a armazenamentos de dados de nuvem do Azure e trabalhar com **Azure Machine Learning**.  <br/> [Captura de tela](#python-language) | <br/>`~notebooks` <br/><br/>|
| Linguagem **Julia**  | Exemplo em **Julia** que detalha Plotagem em Julia, aprendizado profundo em Julia, chamada de C e Python de Julia etc. <br/> [Captura de tela](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (Kit de Ferramentas Cognitivas da Microsoft)  | Exemplos de aprendizado profundo publicados pela equipe do Kit de Ferramentas Cognitivas na Microsoft.  <br/> [Captura de tela](#cntk) | <br/>**Windows**:<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| Blocos de anotações **MXnet**  | Exemplos de Aprendizado Profundo utilizando redes neurais baseadas em **MXnet**. Há uma variedade de blocos de anotações que variam de cenários de iniciantes a avançados.  <br/> [Captura de tela](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Azure Machine Learning** AzureML  | Interagir com **Azure Machine Learning** Studio e criar pontos de extremidade de serviço Web usando modelos treinados localmente para fluxos de trabalho de pontuação baseados em nuvem. <br/> [Captura de tela](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **caffe2** | Exemplos de Aprendizado Profundo utilizando redes neurais baseadas em **caffe2**. Há vários blocos de anotações que desenvolvidos para familiarizar os usuários com o caffe2 e como usá-los com eficiência, inclusive exemplos de como pré-processamento de imagem, criação de conjunto de dados, Regressão e uso de modelos pré-treinados. <br/> [Captura de tela](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | Exemplos com base em Python utilizando **H2O** para muitos problemas de cenário do mundo real. <br/> [Captura de tela](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Linguagem **SparkML**  | Exemplo de uso de recursos e funcionalidades do kit de ferramentas **MLlib** do Spark por meio de **pySpark 2.0** em **Apache Spark 2.0**.  <br/> [Captura de tela](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| Linguagem **MMLSpark**  | Uma variedade de exemplos utilizando **MMLSpark – Microsoft Machine Learning para Apache Spark**, que é uma estrutura que fornece uma série de ferramentas de aprendizado profundo e ciência de dados para **Apache Spark**. <br/> [Captura de tela](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | Vários Exemplos de Rede Neural diferentes e técnicas implementadas usando a estrutura **TensorFlow**. <br/> [Captura de tela](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | Exemplos padrão de Machine Learning em **XGBoost** para cenários como classificação, regressão etc. <br/> [Captura de tela](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Acessar o Jupyter 

Acesse a Página Inicial do Jupyter acessando **`https://localhost:9999`** no Windows ou **`https://localhost:8000`** no Ubuntu.


### <a name="enabling-jupyter-access-from-browser"></a>Como habilitar o acesso ao Jupyter do navegador

**DSVM do Windows**

Execute **`Jupyter SetPassword`** do atalho da área de trabalho e siga o prompt para definir/redefinir sua senha para Jupyter e iniciar o processo do Jupyter. 
<br/>![Habilitar a Exceção do Jupyter](./media/jupyter-setpassword.png)<br/>
Você pode acessar a Página Inicial do Jupyter quando o processo do Jupyter tiver iniciado com êxito na sua VM acessando **`https://localhost:9999`** no navegador. Consulte a captura de tela para adicionar a exceção e habilitar o acesso do Jupyter pelo navegador
<br/>![Habilitar a Exceção do Jupyter](./media/windows-jupyter-exception.png)<br/>
Entrar com a nova senha que você acaba de definir.
<br/>
**DSVM do Linux**

Você pode acessar a Página Inicial do Jupyter na sua VM acessando **`https://localhost:8000`** no navegador. Consulte a captura de tela para adicionar a exceção e habilitar o acesso do Jupyter pelo navegador.
<br/>![Habilitar a Exceção do Jupyter](./media/ubuntu-jupyter-exception.png)<br/>
Entre com a mesma senha usada para o logon na DSVM.
<br/>

**Página inicial do Jupyter**
<br/>![Página inicial do Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Linguagem R 
<br/>![Exemplos de R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguagem Python
<br/>![Exemplos em Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Linguagem Julia 
<br/>![Exemplos de Julia](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK 
<br/>![Exemplos de CNTK](./media/cntk-samples2.png)<br/>
<br/>![Exemplos de CNTK](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXnet
<br/>![Exemplos de MXnet](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Exemplos de AzurekML](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>caffe2 
<br/>![Exemplos de caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Exemplos de H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Exemplos de SparkML](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Exemplos de TensorFlow](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Exemplos de XGBoost](./media/xgboost-samples.png)<br/>

