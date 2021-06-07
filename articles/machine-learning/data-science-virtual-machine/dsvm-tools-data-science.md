---
title: Ferramentas de ciência de dados e aprendizado de máquina
titleSuffix: Azure Data Science Virtual Machine
description: Saiba mais sobre as ferramentas e as estruturas de aprendizado de máquina que são pré-instalados no Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d78b106464bfbd3c86b9899a5c3543eed5dc0924
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888950"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Ferramentas de ciência de dados e aprendizado de máquina em máquinas virtuais de ciência de dados do Azure
As DSVMs (máquinas virtuais de ciência de dados) do Azure têm um conjunto avançado de ferramentas e bibliotecas para o aprendizado de máquina disponível em linguagens populares, como Python, R e Julia.

Aqui estão algumas das ferramentas e bibliotecas de aprendizado de máquina no DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>SDK do Azure Machine Learning para Python

Consulte a referência completa para o [SDK do Azure Machine Learning para Python](../overview-what-is-azure-ml.md).

| Categoria | Valor |
| ------------- | ------------- |
| O que é?   |   Azure Machine Learning é um serviço de nuvem que você pode usar para desenvolver e implantar modelos de aprendizado de máquina. É possível acompanhar os modelos na medida em que você cria, treina, dimensiona e gerencia usando o SDK do Python. Implante modelos como contêineres e execute-os na nuvem, no local ou no Azure IoT Edge.   |
| Edições compatíveis     | Windows (ambiente do conda: AzureML), Linux (ambiente do conda: py36)    |
| Usos típicos      | Plataforma de aprendizado de máquina geral      |
| Como é configurado ou instalado?      |  Instalada com o suporte a GPU   |
| Como usá-lo ou executá-lo      | Como um SDK do Python e no CLI do Azure. Ative para o ambiente conda `AzureML` na edição do Windows *ou* para `py36` na edição do Linux.      |
| Link para exemplos      | Exemplos Jupyter Notebooks estão incluídos no diretório `AzureML` em notebooks.  |
| Ferramentas relacionadas      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

| Categoria | Valor |
| ------------- | ------------- |
| O que é?   | Uma plataforma de ia de software livre que dá suporte ao aprendizado de máquina na memória, distribuída, rápida e escalonável.  |
| Versões com suporte      | Linux   |
| Usos típicos      | Aprendizado de máquina distribuído e escalonável de uso geral   |
| Como é configurado ou instalado?      | O H2O é instalado em `/dsvm/tools/h2o`.      |
| Como usá-lo ou executá-lo      | Conecte-se à VM usando o X2Go. Inicie um novo terminal e execute `java -jar /dsvm/tools/h2o/current/h2o.jar`. Em seguida, inicie um navegador da Web e conecte-se ao `http://localhost:54321`.      |
| Link para exemplos      | Os exemplos estão disponíveis na VM em Jupyter no diretório `h2o`.      |
| Ferramentas relacionadas      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Há várias outras bibliotecas de aprendizado de máquina em DSVMs, como o pacote popular `scikit-learn` que faz parte da distribuição Anaconda Python para DSVMs. Para verificar a lista de pacotes disponíveis em Python, R e Julia, execute os respectivos gerenciadores de pacotes.

## <a name="lightgbm"></a>LightGBM

| Categoria | Valor |
| ------------- | ------------- |
| O que é?   | Uma estrutura de reforço de gradiente rápida, distribuída e de alto desempenho (GBDT, GBRT, GBM ou MART) com base em algoritmos de árvore de decisão. Ele é usado para classificação, classificação e muitas outras tarefas de aprendizado de máquina.    |
| Versões com suporte      | Windows, Linux    |
| Usos típicos      | Estrutura de aumento de gradiente de uso geral      |
| Como é configurado ou instalado?      | No Windows, LightGBM é instalado como um pacote do Python. No Linux, o executável de linha de comando está em `/opt/LightGBM/lightgbm`, o pacote R está instalado e pacotes Python estão instalados.     |
| Link para exemplos      | [Guia do LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Ferramentas relacionadas      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
| Categoria | Valor |
| ------------- | ------------- |
| O que é?   |   Uma interface gráfica do usuário para Data Mining usando o R.   |
| Edições compatíveis     | Windows, Linux     |
| Usos típicos      | Ferramenta de mineração de dados de interface do usuário geral para R    |
| Como usá-lo ou executá-lo      | Como uma ferramenta de interface do usuário. No Windows, inicie um prompt de comando, execute R e, em seguida, dentro do R, execute `rattle()` . No Linux, conecte-se com o X2Go, inicie um terminal, execute R e, em R, execute `rattle()` . |
| Link para exemplos      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
| Categoria | Valor |
| ------------- | ------------- |
| O que é?   |   Uma biblioteca de sistema de aprendizado rápida, de código aberto e fora do núcleo    |
| Edições compatíveis     | Windows, Linux     |
| Usos típicos      | Biblioteca geral de aprendizado de máquina      |
| Como é configurado ou instalado?      |  Windows: instalador MSI<br/>Linux: apt-get |
| Como usá-lo ou executá-lo      | Como uma ferramenta de linha de comando no caminho (`C:\Program Files\VowpalWabbit\vw.exe` no Windows, `/usr/bin/vw` no Linux)    |
| Link para exemplos      | [Exemplos de VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Ferramentas relacionadas      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
| Categoria | Valor |
| ------------- | ------------- |
| O que é?   |  Uma coleção de algoritmos de aprendizado de máquina para tarefas de mineração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados do seu próprio código Java. Weka contém ferramentas para o pré-processamento, classificação, regressão, clustering, regras de associação e visualização de dados. |
| Edições compatíveis     | Windows, Linux     |
| Usos típicos      | Ferramenta de aprendizado de máquina geral     |
| Como usá-lo ou executá-lo      | No Windows, procure weka no menu **Iniciar** . No Linux, entre com X2Go e, em seguida, vá para **aplicativos**  >  **desenvolvimento**  >  **weka**. |
| Link para exemplos      | [Exemplos de Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
| Categoria | Valor |
| ------------- | ------------- |
| O que é?   |   Uma biblioteca de aumento de gradiente rápida, portátil e distribuída (GBDT, GBRT ou GBM) para Python, R, Java, escala, C++ e muito mais. Ele é executado em um único computador e no Apache Hadoop e no Spark.    |
| Edições compatíveis     | Windows, Linux     |
| Usos típicos      | Biblioteca geral de aprendizado de máquina      |
| Como é configurado ou instalado?      |  Instalada com o suporte a GPU   |
| Como usá-lo ou executá-lo      | Como uma biblioteca do Python (2,7 e 3.6 +), pacote R e ferramenta de linha de comando no caminho ( `C:\dsvm\tools\xgboost\bin\xgboost.exe` para Windows e `/dsvm/tools/xgboost/xgboost` para Linux)    |
| Links para exemplos      | Exemplos são incluídos na VM, em `/dsvm/tools/xgboost/demo` no Linux e em `C:\dsvm\tools\xgboost\demo` no Windows.   |
| Ferramentas relacionadas      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Análise do Apache
| Categoria | Valor |
| ------------- | ------------- |
| O que é?   | Mecanismo de consulta SQL de código aberto no Big Data    |
| Versões do DSVM com suporte      | Windows 2019, Linux  |
| Como ele é configurado e instalado no DSVM?      |  Instalado em `/dsvm/tools/drill*` somente no modo inserido   |
| Usos típicos      |  Para a exploração de dados in-loco sem a necessidade de extração, transformação, carregamento (ETL). Consulte diferentes fontes de dados e formatos, incluindo CSV, JSON, tabelas relacionais e Hadoop.     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho  <br/> [Introdução a Análise em 10 minutos](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Ferramentas relacionadas no DSVM      |   Rattle, Weka, SQL Server Management Studio      |