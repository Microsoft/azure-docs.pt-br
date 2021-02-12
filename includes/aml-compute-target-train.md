---
title: incluir arquivo
description: incluir arquivo
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/17/2020
ms.openlocfilehash: 87b1f4ab7b7091970d7bb76ae1e00b06549fb0b4
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96152717"
---
**Os destinos de computação podem ser reutilizados pelos trabalhos de treinamento.** Por exemplo, após anexar uma VM remota ao seu workspace, você pode reutilizá-la para vários trabalhos. Para pipelines de machine learning, use a [etapa do pipeline](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) apropriada para cada destino de computação.

Você pode usar qualquer um dos recursos a seguir para um destino de computação de treinamento para a maioria dos trabalhos. Nem todos os recursos podem ser usados para machine learning automatizado, pipelines de machine learning ou designer.

|Destinos de &nbsp;treinamento|[Machine learning automatizado](../articles/machine-learning/concept-automated-ml.md) | [Pipelines do aprendizado de máquina](../articles/machine-learning/concept-ml-pipelines.md) | [Designer do Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Sim | &nbsp; | &nbsp; |
|[Cluster de computação do Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Sim | Sim | Sim |
|[Instância de computação do Azure Machine Learning](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Sim (por meio do SDK)  | Sim |  |
|[VM remota](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Sim  | Sim | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Sim (somente no modo local do SDK) | Sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Sim | &nbsp; |
|[Lote do Azure](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Sim | &nbsp; |
