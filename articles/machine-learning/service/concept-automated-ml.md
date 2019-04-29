---
title: Seleção de algoritmo de ML e ajuste automatizado
titleSuffix: Azure Machine Learning service
description: Saiba como Serviço do Azure Machine Learning pode escolher um algoritmo para você e automaticamente gerar um modelo proveniente para economizar tempo usando os parâmetros e os critérios que você fornecer ao selecionar o melhor algoritmo para seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 620dbd22613df37fdc3c20e34906684446b2251f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821188"
---
# <a name="what-is-automated-machine-learning"></a>O que é o aprendizado de máquina automatizado?

Machine learning automatizado é o processo de pegar os dados de treinamento com um recurso de destino definida e iterar por combinações de algoritmos e seleções de recurso para selecionar automaticamente o melhor modelo para seus dados com base nas pontuações de treinamento. O processo de desenvolvimento do modelo de machine learning tradicional é altamente intensivo de recursos e requer conhecimento de domínio significativo e investimento de tempo para executar e comparar os resultados de dezenas de modelos. O machine learning automatizado simplifica esse processo gerando modelos ajustados os objetivos e restrições que você definiu para o seu experimento, como o tempo para o experimento a ser executado.

## <a name="how-it-works"></a>Como ele funciona

1. Você configura o tipo de problema de aprendizado de máquina que está tentando resolver. Categorias de aprendizado supervisionado são compatíveis:
   + classificação
   + Regressão
   + Previsão

   Embora o machine learning automatizado esteja geralmente disponível, **a previsão ainda está em visualização pública.**

   Veja a [lista de modelos](how-to-configure-auto-train.md#select-your-experiment-type) que o Azure Machine Learning pode experimentar durante o treinamento.

1. Você especifica a origem e o formato dos dados de treinamento. Os dados precisam estar rotulados e podem ser armazenados em seu ambiente de desenvolvimento ou no Armazenamento de Blobs do Azure. Se os dados são armazenados em seu ambiente de desenvolvimento, eles devem estar no mesmo diretório que seus scripts de treinamento. Esse diretório é copiado para o destino de computação que você selecionar para treinamento.

    No seu script de treinamento, os dados podem ser lidos em matrizes Numpy ou em um dataframe Pandas.

    Você pode configurar opções separadas para selecionar os dados de treinamento e validação, ou então pode especificar conjuntos de dados de treinamento e de validação separados.

1. Configure o [destino de computação](how-to-set-up-training-targets.md) que é usado para treinar o modelo.

1. Defina a configuração do aprendizado de máquina automatizado. Isso controla os parâmetros usados conforme o Azure Machine Learning itera por diferentes modelos, configurações de hiperparâmetro e quais métricas observar ao determinar o melhor modelo

1. Envie uma execução de treinamento.

Durante o treinamento, o serviço Azure Machine Learning cria um número de pipelines que experimentam diferentes parâmetros e algoritmos. Ele será interrompido depois que atingir o limite de iteração que você fornecer, ou quando ele atingir o valor de destino para a métrica que você especificar.

[![Automatizado de Machine learning](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Você pode inspecionar as informações de execução registradas, que contêm as métricas coletadas durante a execução. A execução de treinamento também produz um objeto serializado do Python (`.pkl` arquivo) que contém o pré-processamento de modelo e de dados.


## <a name="next-steps"></a>Próximas etapas

Veja exemplos e saiba como criar modelos usando o aprendizado de máquina automatizado:

+ [Tutorial: Treinar automaticamente um modelo de classificação com o Machine Learning Automatizado do Azure](tutorial-auto-train-models.md)

+ [Exemplos de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

+ [Usar o treinamento automático em um recurso remoto](how-to-auto-train-remote.md)

+ [Definir as configurações para treinamento automático](how-to-configure-auto-train.md)
