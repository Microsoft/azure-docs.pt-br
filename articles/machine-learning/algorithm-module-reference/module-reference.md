---
title: Referência de módulo e algoritmo
description: Saiba mais sobre os módulos do designer de Azure Machine Learning que você pode usar para criar seus próprios projetos de Machine Learning.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/09/2020
ms.openlocfilehash: 89ad9aae7c0d01971bbcfc7e392cb9d455ef85cd
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376835"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Referência de módulo de & de algoritmo para o designer de Azure Machine Learning

Esse conteúdo de referência fornece a experiência técnica em cada um dos módulos e algoritmos de aprendizado de máquina disponíveis no designer de Azure Machine Learning.

Cada módulo representa um conjunto de códigos que podem ser executados de modo independente e executar uma tarefa de aprendizado de máquina, quando fornecidas as entradas necessárias. Um módulo pode conter um algoritmo específico ou executar uma tarefa que seja importante no aprendizado de máquina, tal como substituição de um valor ausente ou análise estatística.

Para obter ajuda com a escolha de algoritmos, consulte 
* [Como selecionar algoritmos](../how-to-select-algorithms.md)
* [Página de dicas úteis do algoritmo do Azure Machine Learning](../algorithm-cheat-sheet.md)

> [!TIP]
> Em qualquer pipeline no designer, é possível obter informações sobre um módulo específico. Escolha o link **Saiba mais** na placa do módulo ao passar o mouse sobre o módulo na lista de módulos ou no painel direito do módulo.

## <a name="data-preparation-modules"></a>Módulos de preparação de dados


| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Dados de entrada e saída | Mova dados de fontes de nuvem para seu pipeline. Grave seus resultados ou dados intermediários no armazenamento do Azure, banco de dados SQL ou Hive, durante a execução de um pipeline ou use o armazenamento em nuvem para trocar dados entre pipelines.  | [Inserir dados manualmente](enter-data-manually.md) <br/> [Exportar dados](export-data.md) <br/> [Importar dados](import-data.md) |
| Transformação de dados | Operações em dados que são exclusivos para o aprendizado de máquina, tais como normalização ou compartimentalização de dados, redução de dimensionalidade e conversão de dados entre vários formatos de arquivo.| [Adicionar Colunas](add-columns.md) <br/> [Adicionar Linhas](add-rows.md) <br/> [Aplicar Operação Matemática](apply-math-operation.md) <br/> [Aplicar Transformação SQL](apply-sql-transformation.md) <br/> [Limpar Dados Ausentes](clean-missing-data.md) <br/> [Recortar Valores](clip-values.md) <br/> [Converter em CSV](convert-to-csv.md) <br/> [Converter em Conjunto de Dados](convert-to-dataset.md) <br/> [Converter em Valores de Indicador](convert-to-indicator-values.md) <br/> [Editar Metadados](edit-metadata.md) <br/> [Agrupar Dados em Compartimentos](group-data-into-bins.md) <br/> [Unir Dados](join-data.md) <br/> [Normalizar Dados](normalize-data.md) <br/> [Particionar e Gerar Amostra](partition-and-sample.md)  <br/> [Remover Linhas Duplicadas](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Selecionar Colunas para Transformação](select-columns-transform.md) <br/> [Projetar Colunas no Conjunto de Dados](select-columns-in-dataset.md) <br/> [Dividir Dados](split-data.md) |
| Seleção de recursos | Escolha um subconjunto de recursos relevantes e úteis a serem usados na criação de um modelo analítico. | [Seleção de Recursos Baseada em Filtro](filter-based-feature-selection.md) <br/> [Importância do Recurso de Permuta](permutation-feature-importance.md) |
| Funções estatísticas | Forneça uma ampla variedade de métodos estatísticos relacionados à ciência de dados. | [Resumir Dados](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmos de aprendizado de máquina

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Regressão | Prever um valor. | [Regressão de Árvore de Decisão Aumentada](boosted-decision-tree-regression.md) <br/> [Regressão de Floresta de Decisão](decision-forest-regression.md) <br/> [Regressão rápida de quantil de floresta](fast-forest-quantile-regression.md)  <br/> [Regressão Linear](linear-regression.md)  <br/> [Regressão de Rede Neural](neural-network-regression.md)  <br/> [Regressão de Poisson](poisson-regression.md)  <br/>|
| Clustering | Agrupar dados juntos.| [Cluster K-Means](k-means-clustering.md)
| classificação | Prever uma classe.  Escolha entre os algoritmos binário (duas classes) ou multiclasse.| [Árvore de Decisão Aumentada Multiclasse](multiclass-boosted-decision-tree.md) <br/> [Floresta de Decisão Multiclasse](multiclass-decision-forest.md) <br/> [Regressão Logística Multiclasse](multiclass-logistic-regression.md)  <br/> [Rede Neural Multiclasse](multiclass-neural-network.md) <br/> [Um contra Todas as Multiclasses](one-vs-all-multiclass.md) <br/> [Uma versus multiclasse](one-vs-one-multiclass.md) <br/>[Perceptron Médio de Duas Classes](two-class-averaged-perceptron.md) <br/>  [Árvore de Decisão Aumentada de Duas Classes](two-class-boosted-decision-tree.md)  <br/> [Floresta de Decisão de Duas Classes](two-class-decision-forest.md) <br/>  [Regressão Logística de Duas Classes](two-class-logistic-regression.md) <br/> [Rede Neural de Duas Classes](two-class-neural-network.md) <br/> [Computador de Vetor de Suporte de Duas Classes](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Módulos para criação e avaliação de modelos

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Treinamento de modelo | Execute os dados por meio do algoritmo. |  [Treinar Modelo de Clustering](train-clustering-model.md) <br/> [Treinar Modelo](train-model.md) <br/> [Treinar modelo Pytorch](train-pytorch-model.md) <br/> [Ajustar os Hiperparâmetros de Modelo](tune-model-hyperparameters.md) |
| Pontuação e Avaliação do Modelo | Medir a precisão do modelo treinado. | [Aplicar Transformação](apply-transformation.md) <br/> [Atribuir Dados a Clusters](assign-data-to-clusters.md) <br/> [Modelo de Validação Cruzada](cross-validate-model.md) <br/> [Avaliar Modelo](evaluate-model.md) <br/> [Pontuar Modelo de Imagem](score-image-model.md) <br/> [Pontuar Modelo](score-model.md) |
| Linguagem Python | Grave o código e incorpore-o em um módulo para integrar o Python ao seu pipeline. | [Criar Modelo Python](create-python-model.md) <br/> [Executar Script do Python](execute-python-script.md) |
| Linguagem R | Grave o código e incorpore-o em um módulo para integrar o R ao seu pipeline. | [Executar Script R](execute-r-script.md) |
| Análise de texto | Disponibilize ferramentas computacionais especializadas para trabalhar com texto estruturado e não estruturado. |  [Converter Palavra em Vetor](convert-word-to-vector.md) <br/> [Extrair Recursos N-Gram do Texto](extract-n-gram-features-from-text.md) <br/> [Hash de Recursos](feature-hashing.md) <br/> [Pré-processar Texto](preprocess-text.md) <br/> [Alocação de Dirichlet Latente](latent-dirichlet-allocation.md) <br/> [Pontuar o modelo Vowpal Wabbit](score-vowpal-wabbit-model.md) <br/> [Treinar o modelo Vowpal Wabbit](train-vowpal-wabbit-model.md)|
| Pesquisa Visual Computacional | Módulos relacionados ao pré-processamento de dados de imagem e ao reconhecimento de imagem. |  [Aplicar Transformação de Imagem](apply-image-transformation.md) <br/> [Converter em Diretório de Imagem](convert-to-image-directory.md) <br/> [Transformação da Imagem de Inicialização](init-image-transformation.md) <br/> [Dividir Diretório de Imagens](split-image-directory.md) <br/> [DenseNet](densenet.md) <br/> [ResNet](resnet.md) |
| Recomendação | Criar modelos de recomendação. | [Avaliar o Sistema de Recomendação](evaluate-recommender.md) <br/> [Pontuar o Sistema de Recomendação SVD](score-svd-recommender.md) <br/> [Pontuar o sistema de recomendação grande e profundo](score-wide-and-deep-recommender.md)<br/> [Treinar o Sistema de Recomendação SVD](train-SVD-recommender.md) <br/> [Treinar o sistema de recomendação grande e profundo](train-wide-and-deep-recommender.md)|
| Detecção de anomalias | Modelos de detecção de anomalias treinados. | [Detecção de Anomalias Baseada em PCA](pca-based-anomaly-detection.md) <br/> [Treinar um Modelo de Detecção de Anomalias](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Serviço Web

Saiba mais sobre os [módulos de serviço Web](web-service-input-output.md) que são necessários para a inferência em tempo real no designer do Azure Machine Learning.

## <a name="error-messages"></a>Mensagens de erro

Saiba mais sobre [as mensagens de erro e os códigos de exceção](designer-error-codes.md) que podem ser encontradas usando módulos no designer do Azure Machine Learning.

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Criar um modelo no designer para prever preços automáticos](../tutorial-designer-automobile-price-train-score.md)
