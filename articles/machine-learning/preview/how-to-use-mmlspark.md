---
title: Como usar o MMLSpark Machine Learning | Microsoft Docs
description: Guia de como usar a biblioteca MMLSpark com o Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 6714e8ad77693f0cdefe3e40c99153299e1c72d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Como usar a Biblioteca do Microsoft Machine Learning para Apache Spark

## <a name="introduction"></a>Introdução

A MMLSpark ([Biblioteca do Microsoft Azure Machine Learning para Apache Spark](https://github.com/Azure/mmlspark)) fornece ferramentas que permitem a fácil criação de modelos de aprendizado de máquina escalonáveis para grandes conjuntos de dados. Inclui a integração de pipelines do SparkML ao [Kit de Ferramentas Cognitivas da Microsoft](https://github.com/Microsoft/CNTK) e ao [OpenCV](http://www.opencv.org/), permitindo a você: 
 * Inserir e pré-processar dados de imagem
 * Personalizar imagens e textos usando modelos de aprendizado profundo pré-treinados
 * Treinar e pontuar modelos de classificação e regressão usando personalização implícita.

## <a name="prerequisites"></a>Pré-requisitos

Para percorrer este guia de instruções, você precisa:
- [Instalar o Azure Machine Learning Workbench](quickstart-installation.md)
- [Configurar o cluster Azure HDInsight Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>Executar seu experimento no contêiner do Docker

Seu Azure Machine Learning Workbench é configurado para usar MMLSpark quando você executa experimentos no contêiner do Docker, seja localmente ou na VM remota. Essa funcionalidade permite que você facilmente depure e teste seus modelos PySpark antes de executá-los em escala em um cluster. 

Para começar a usar um exemplo, crie um novo projeto e selecione o exemplo da Galeria "MMLSpark no Adult Census". Selecione "Docker" como o contexto de computação do painel de projeto e clique em "Executar". O Azure Machine Learning Workbench cria o contêiner do Docker para executar o programa PySpark e, em seguida, executa o programa.

Após a execução, você pode exibir os resultados na exibição de histórico de execução do Azure Machine Learning Workbench.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Instalar o MMLSpark no Cluster do Azure HDInsight Spark.

Para concluir esta etapa e a seguinte, é preciso primeiro [criar um cluster do Azure HDInsight Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

Por padrão, o Azure Machine Learning Workbench instala o pacote MMLSpark no seu cluster quando você executa o experimento. Você pode controlar esse comportamento e instalar outros pacotes do Spark editando um arquivo chamado _aml_config/spark_dependencies.yml_ na pasta do projeto.

```
# Spark configuration properties.
configuration:
  "spark.app.name": "Azure ML Experiment"
  "spark.yarn.maxAppAttempts": 1

repositories:
  - "https://mmlspark.azureedge.net/maven"
packages:
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.7.9"
```

Você também pode instalar MMLSpark diretamente no seu cluster HDInsight Spark usando [Ação de Script](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Configurar o cluster Azure HDInsight Spark como destino de computação

Abra a janela da CLI do Azure Machine Learning Workbench acessando o Menu "Arquivo" e clique em "Abrir Prompt de Comando"

Na janela da CLI, execute os seguintes comandos:

```
az ml computetarget attach --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> --type cluster
```

```
az ml experiment prepare -c <myhdi>
```

Agora o cluster está disponível como destino de computação para o projeto.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Executar o experimento no cluster do Azure HDInsight Spark.

Volte para o painel de projeto do exemplo "MMLSpark no Adult Census". Selecione seu cluster como destino de computação e clique em executar.

O Azure Machine Learning Workbench envia o trabalho do Spark para o cluster. Agora você pode monitorar o andamento e exibir os resultados na exibição de histórico de execução.

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre a biblioteca do MMLSpark e exemplos, consulte [Repositório do MMLSpark GitHub](https://github.com/Azure/mmlspark)

*Apache®, Apache Spark e Spark® são marcas registradas ou marcas comerciais da Apache Software Foundation nos Estados Unidos e/ou em outros países.*
