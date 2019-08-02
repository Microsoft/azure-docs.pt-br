---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/30/2019
ms.openlocfilehash: 1b62d4b2ac1bb69e2270c3202d29eb595df7aac4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806049"
---
**Computação destinos podem ser reutilizados de um trabalho de treinamento para o próximo**. Por exemplo, após anexar uma VM remota ao seu workspace, você pode reutilizá-la para vários trabalhos.

|Treinamento &nbsp;destinos| Suporte de GPU |[ML automatizado](../articles/machine-learning/service/concept-automated-ml.md) | [Pipelines do ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Interface Visual](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Talvez | sim | &nbsp; | &nbsp; |
|[Computação do Azure Machine Learning](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| sim | Sim & <br/>hyperparameter&nbsp;tuning | sim | sim |
|[VM remota](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |sim | Sim & <br/>gráfico de ajuste de hiperparâmetro | sim | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | sim | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | sim | &nbsp; |
|[Lote do Azure](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | sim | &nbsp; |
