---
title: Machine Learning no Azure Synapse Analytics
description: Uma visão geral das funcionalidades de machine learning no Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 68b113de63cfefde805c1c46e9303829c4eb33a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222132"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Funcionalidades do Machine Learning no Azure Synapse Analytics

O Azure Synapse Analytics oferece várias funcionalidades de machine learning. Este artigo fornece uma visão geral de como você pode aplicar Machine Learning no contexto do Azure Synapse.

Esta visão geral aborda as diferentes funcionalidades do Azure Synapse, relacionadas a machine learning, de uma perspectiva do processo de ciência de dados.

Você talvez já tenha familiaridade com a aparência de um processo típico de ciência de dados. É um processo bem conhecido, que a maioria dos projetos de machine learning segue.

Em um alto nível, o processo contém as seguintes etapas:
* (Noções básicas sobre negócios)
* Compreensão e aquisição de dados
* Modelagem
* Implantação de modelo e pontuação

Este artigo descreve as funcionalidades de machine learning do Azure Synapse em diferentes mecanismos de análise, de uma perspectiva do processo de ciência de dados. Para cada etapa no processo de ciência de dados, as funcionalidades do Azure Synapse que podem ajudar são resumidas.

## <a name="azure-synapse-machine-learning-capabilities"></a>Funcionalidades de machine learning do Azure Synapse

### <a name="data-acquisition-and-understanding"></a>Compreensão e aquisição de dados

A maioria dos projetos de machine learning envolvem etapas bem estabelecidas e uma dessas etapas é acessar e entender os dados.

#### <a name="data-source-and-pipelines"></a>Fonte de dados e pipelines

Graças ao [Azure Data Factory](../../data-factory/introduction.md), uma parte nativamente integrada do Azure Synapse, há um avançado conjunto de ferramentas disponível para pipelines de orquestração e ingestão de dados. Isso permite que você crie facilmente pipelines de dados para acessar e transformar os dados em um formato que possa ser consumido para machine learning. [Saiba mais sobre pipelines de dados](../../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) no Azure Synapse. 

#### <a name="data-preparation-and-explorationvisualization"></a>Preparação e exploração/visualização de dados

Uma parte importante do processo de machine learning é entender os dados por exploração e visualizações.

Dependendo de onde os dados são armazenados, o Azure Synapse oferece um conjunto de ferramentas diferentes para explorá-los e prepará-los para análise e machine learning. Uma das maneiras mais rápidas de começar a explorar dados é usar os pools de SQL sem servidor ou do Apache Spark diretamente sobre os dados no data lake.

* O [Apache Spark para Azure Synapse](../spark/apache-spark-overview.md) oferece funcionalidades para transformar, preparar e explorar seus dados em escala. Esses pools do Spark oferecem ferramentas como PySpark/Python, Scala e .NET para processamento de dados em escala. Usando bibliotecas de visualização avançadas, a experiência de exploração de dados pode ser aprimorada para ajudar a entender melhor os dados. [Saiba mais sobre como explorar e visualizar dados no Azure Synapse usando o Spark](../get-started-analyze-spark.md).

* Os [pools de SQL sem servidor](../sql/on-demand-workspace-overview.md) oferecem um modo de explorar dados usando o TSQL diretamente no data lake. Os pools de SQL sem servidor também oferecem algumas visualizações internas no Synapse Studio. [Saiba mais sobre como explorar dados com pools de SQL sem servidor](../get-started-analyze-sql-on-demand.md).

### <a name="modeling"></a>Modelagem

No Azure Synapse, os modelos de machine learning de treinamento podem ser executados nos pools do Apache Spark com ferramentas como PySpark/Python, Scala ou .NET.

#### <a name="train-models-on-spark-pools-with-mllib"></a>Treinar modelos em pools do Spark com MLlib

Os modelos de machine learning podem ser treinados com a ajuda de vários algoritmos e bibliotecas. O [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) oferece algoritmos de machine learning escalonáveis que podem ajudar a resolver os problemas de machine learning mais clássicos. [Este tutorial](../spark/apache-spark-machine-learning-mllib-notebook.md) aborda como treinar um modelo usando o MLlib no Azure Synapse.

Além do MLlib, bibliotecas populares, como [Scikit Learn](https://scikit-learn.org/stable/), também podem ser usadas para desenvolver modelos. Confira [Gerenciar bibliotecas para Apache Spark no Azure Synapse Analytics](../spark/apache-spark-azure-portal-add-libraries.md) para obter detalhes sobre como instalar bibliotecas em pools do Synapse Spark.

#### <a name="train-models-with-azure-machine-learning-automated-ml"></a>Treinar modelos com o ML automatizado do Azure Machine Learning

Outro modo de treinar modelos de machine learning, que não requer muita familiaridade com machine learning, é usar o ML automatizado. O [ML automatizado](../../machine-learning/concept-automated-ml.md) é um recurso que treina automaticamente um conjunto de modelos de machine learning e permite que o usuário selecione o melhor modelo com base em métricas específicas. Graças a uma integração direta ao Azure Machine Learning usando os Notebooks do Azure Synapse, os usuários podem facilmente aproveitar o ML automatizado no Azure Synapse com autenticação de passagem do Azure Active Directory.  Isso significa que você só precisa apontar para seu Workspace do Azure Machine Learning e não precisa inserir nenhuma credencial. Aqui está um [tutorial do ML automatizado](../spark/apache-spark-azure-machine-learning-tutorial.md) que descreve como treinar modelos usando o ML automatizado do Azure Machine Learning em Pools do Spark do Azure Synapse.

### <a name="model-deployment-and-scoring"></a>Implantação de modelo e pontuação

Os modelos que foram treinados no Azure Synapse ou fora dele podem ser facilmente usados para a pontuação de lote. Atualmente, há dos modos de execução de pontuação de lote no Azure Synapse.

* Você pode usar a [função PREDICT do TSQL](../sql-data-warehouse/sql-data-warehouse-predict.md) em pools de SQL do Synapse para executar suas previsões no local em que seus dados residem. Essa função avançada e escalonável permite enriquecer seus dados sem mover dados para fora do seu data warehouse. Uma nova [experiência guiada de modelo de machine learning no Synapse Studio](./tutorial-sql-pool-model-scoring-wizard.md) foi introduzida. Nela, você pode implantar um modelo ONNX do registro de modelos do Azure Machine Learning em pools de SQL do Synapse para a pontuação de lote usando PREDICT.

* Outra opção para a pontuação de lote de modelos de machine learning no Azure Synapse é aproveitar os pools do Apache Spark para o Azure Synapse. Dependendo das bibliotecas usadas para treinar os modelos, você pode usar uma experiência de código para executar a pontuação de lote.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Synapse Analytics](../get-started.md)
* [Criar um workspace](../get-started-create-workspace.md)
* [Início Rápido: Criar um serviço vinculado do Azure Machine Learning no Azure Synapse](quickstart-integrate-azure-machine-learning.md)
* [Tutorial: Assistente de pontuação do modelo de machine learning – pool de SQL dedicado](tutorial-sql-pool-model-scoring-wizard.md)