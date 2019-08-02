---
title: Referência de módulo e algoritmo
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre os módulos disponíveis na interface visual do Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: e73d4ebd3eb05f7cf217573d8112e3dbbe6d3a37
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514067"
---
# <a name="algorithm--module-reference-overview"></a>Visão geral de referência de módulo e algoritmos

O conteúdo de referência fornece experiência técnica em cada um dos módulos disponíveis na interface do visual (visualização) do serviço Azure Machine Learning e algoritmos de aprendizado de máquina.

Cada módulo representa um conjunto de códigos que podem executar de forma independente e executar um machine learning tarefa, considerando as entradas necessárias. Um módulo pode conter um algoritmo específico, ou executar uma tarefa que é importante em aprendizado de máquina, como substituição de valor ausente ou análise estatística.

> [!TIP]
> Em qualquer experimento na interface do visual, você pode obter informações sobre um módulo específico. Selecione o módulo e selecione o **mais ajuda** link na **ajuda rápida** painel.

## <a name="modules"></a>Módulos

Módulos são organizados por funcionalidade:

| Funcionalidade | DESCRIÇÃO | Módulo |
| --- |--- | ---- |
| Conversões de formato de dados | Converter dados entre vários formatos de arquivo usados no aprendizado de máquina, | [Converter para CSV](convert-to-csv.md) |
| Dados de entrada e saída | Mova dados de fontes de nuvem em seu teste. Gravar seus resultados ou dados intermediários para armazenamento do Azure, um banco de dados SQL ou o Hive, durante a execução de um experimento, ou usar o armazenamento em nuvem para trocar dados entre experimentos.  | [Importar Dados](import-data.md)<br/>[Exportar Dados](export-data.md)<br/>[Inserir os dados manualmente](enter-data-manually.md) |
| Transformação de dados | Operações nos dados que são exclusivos ao machine learning, como normalização ou compartimentação de dados, a seleção de recursos e redução de dimensionalidade.| [Selecionar colunas no conjunto de dados](select-columns-in-dataset.md) <br/> [Editar metadados](edit-metadata.md) <br/> [Limpar dados ausentes](clean-missing-data.md) <br/> [Adicionar colunas](add-columns.md) <br/> [Adicionar linhas](add-rows.md) <br/> [Remover linhas duplicadas](remove-duplicate-rows.md) <br/> [Unir dados](join-data.md) <br/> [Dividir os dados](split-data.md) <br/> [Normalizar dados](normalize-data.md) <br/> [Partição e exemplo](partition-and-sample.md) |
| Módulos de Python e R | Escrever código e inseri-lo em um módulo para integrar o Python e R ao seu experimento. | [Executar Script Python](execute-python-script.md)   <br/> [Criar modelo de Python](create-python-model.md) <br/> [Executar Script R](execute-r-script.md)
|  | **Algoritmos de aprendizado de máquina**: | |
| classificação | Prever uma classe.  Escolha a partir do binário (duas classes) ou algoritmos multiclasses.| [Floresta de decisão multiclasse](multiclass-decision-forest.md) <br/> [Regressão logística multiclasse](multiclass-logistic-regression.md)  <br/> [Rede Neural multiclasse](multiclass-neural-network.md)  <br/>  [Regressão logística de duas classes](two-class-logistic-regression.md)  <br/>[De média Perceptron de duas classes](two-class-averaged-perceptron.md) <br/> [Duas classes&nbsp;aumentada&nbsp;decisão&nbsp;árvore](two-class-boosted-decision-tree.md)  <br/> [Floresta de decisão de duas classes](two-class-decision-forest.md)  <br/> [Rede Neural de duas classes](two-class-neural-network.md)  <br/> [Dois&#8209;classe&nbsp;suporte&nbsp;vetor&nbsp;Machine](two-class-support-vector-machine.md) 
| Clustering | Agrupe dados.| [Clustering de K-Means](k-means-clustering.md)
| Regressão | Prever um valor. | [Regressão linear](linear-regression.md)  <br/> [Regressão de rede neural](neural-network-regression.md)  <br/> [Regressão de floresta de decisão](decision-forest-regression.md)  <br/> [Boosted&nbsp;decisão&nbsp;árvore&nbsp;regressão](boosted-decision-tree-regression.md)
|  | **Criar e avaliar modelos**: | |
| Treinar   | Dados de execução através do algoritmo. | [Modelo de treinamento](train-model.md)  <br/> [Treinar o modelo de Clustering](train-clustering-model.md)    |
| Avaliar Modelo | Medir a precisão do modelo treinado. |  [Avaliar modelo](evaluate-model.md)
| Pontuação | Obter as previsões do modelo que acabou de treinar. | [Aplicar transformação](apply-transformation.md)<br/>[Atribua&nbsp;dados&nbsp;para&nbsp;Clusters](assign-data-to-clusters.md) <br/>[Modelo de pontuação](score-model.md)

## <a name="error-messages"></a>Mensagens de erro

Saiba mais sobre o [mensagens de erro e códigos de exceção](machine-learning-module-error-codes.md) que podem ser encontrados usando os módulos na interface do visual do serviço Azure Machine Learning.
