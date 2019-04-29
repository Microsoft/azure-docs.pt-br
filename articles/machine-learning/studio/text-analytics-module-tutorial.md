---
title: Criar um modelo de análise de sentimento
titleSuffix: Azure Machine Learning Studio
description: Como criar modelos de análise de texto no Azure Machine Learning Studio usando módulos de pré-processamento de texto, N-gramas ou hash de recursos
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 08d62e7a6c9503d415fe144da57eee72ce3bfafd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636534"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio"></a>Criar um modelo de análise de sentimento no Azure Machine Learning Studio

Você pode usar o Azure Machine Learning Studio para criar e operacionalizar modelos de análise de texto. Esses modelos podem ajudá-lo a resolver, por exemplo, problemas de classificação de documento ou análise de sentimento.

Em um experimento de análise de texto, geralmente, você pode:

1. Limpar e pré-processar o conjunto de dados de texto
2. Extrair vetores de recurso numérico de um texto pré-processado
3. Treinar o modelo de classificação ou regressão
4. Pontuar e validar o modelo
5. Implantar o modelo na produção

Neste tutorial, você aprenderá essas etapas conforme examinamos um modelo de análise de sentimento usando o conjunto de dados do Amazon Book Reviews (consulte o artigo de pesquisa "Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification" de John Blitzer, Mark Dredze e Fernando Pereira; Association of Computational Linguistics (ACL), 2007.) Esse conjunto de dados consiste em pontuações de crítica (1-2 ou 4-5) e um texto de forma livre. O objetivo é prever a pontuação da crítica: baixa (1-2) ou alta (4-5).

Você pode encontrar os experimentos abordados neste tutorial na Galeria de IA do Azure:

[Prever crítica literária](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Prever crítica literária – Experimento preditivo](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Etapa 1: Limpar e pré-processar o conjunto de dados de texto
Começamos o experimento dividindo as pontuações de crítica em buckets categóricos altos e baixos para formular o problema como uma classificação de duas classes. Usamos os módulos [Editar Metadados](https://msdn.microsoft.com/library/azure/dn905986.aspx) e [Agrupar Valores Categóricos](https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Criar rótulo](./media/text-analytics-module-tutorial/create-label.png)

Em seguida, limpamos o texto usando o módulo [Pré-processar Texto](https://msdn.microsoft.com/library/azure/mt762915.aspx) . A limpeza reduz o ruído no conjunto de dados, ajuda a encontrar os recursos mais importantes e melhora a precisão do modelo final. Removemos palavras irrelevantes (stop words) – palavras comuns, como “o” ou “um” – bem como números, caracteres especiais, caracteres duplicados, endereços de email e URLs. Também convertemos o texto em minúsculas, fazemos a lematização das palavras e detectamos os limites de sentença, que são então indicados pelo símbolo “|||” no texto pré-processado.

![Pré-processar Texto](./media/text-analytics-module-tutorial/preprocess-text.png)

E se você quiser usar uma lista personalizada de palavras irrelevantes? Você pode passá-la como uma entrada opcional. Também é possível usar uma expressão regular personalizada da sintaxe em C# para substituir subcadeias de caracteres e remover palavras por parte da fala: substantivos, verbos ou adjetivos.

Após a conclusão do pré-processamento, dividimos os dados em conjuntos de treinamento e teste.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Etapa 2: Extrair vetores de recurso numérico de um texto pré-processado
Para criar um modelo para dados de texto, normalmente, você precisa converter o texto de forma livre em vetores de recurso numérico. Neste exemplo, usamos o módulo [Extrair Recursos de N-grama de Texto](https://msdn.microsoft.com/library/azure/mt762916.aspx) para transformar os dados de texto em um formato desse tipo. Este módulo usa uma coluna de palavras separadas por espaço em branco e calcula um dicionário de palavras ou N-gramas de palavras, que aparecem no conjunto de dados. Em seguida, ele conta quantas vezes cada palavra, ou N-gram, é exibida em cada registro e cria vetores de recurso das contagens. Neste tutorial, definimos o tamanho do N-grama como 2, para que nossos vetores de recurso incluam palavras individuais e combinações de duas palavras subsequentes.

![Extrair N-gramas](./media/text-analytics-module-tutorial/extract-ngrams.png)

Aplicamos a pesagem TF*IDF (Frequência de Termo Frequência Inversa do Documento) a contagens de N-grama. Essa abordagem adiciona o peso de palavras que aparecem frequentemente em um único registro, mas que são raras em todo o conjunto de dados. Outras opções incluem o binário, TF e a pesagem de grafo.

Geralmente, esses recursos de texto têm alta dimensionalidade. Por exemplo, se seu corpus tiver 100.000 palavras exclusivas, seu espaço de recurso terá 100.000 dimensões ou mais, caso sejam usados N-gramas. O módulo Extrair Recursos de N-grama fornece um conjunto de opções para reduzir a dimensionalidade. Você pode optar por excluir palavras que são curtas ou longas, muito incomuns ou muito frequentes que têm um valor preditivo significativo. Neste tutorial, excluímos N-gramas que aparecem em menos de 5 registros ou em mais de 80% dos registros.

Além disso, você pode usar a seleção de recursos para selecionar apenas os recursos que estão mais correlacionados ao seu destino de previsão. Usamos a seleção de recursos Qui Quadrado para selecionar 1.000 recursos. Você pode exibir o vocabulário de palavras selecionadas ou N-gramas clicando na saída correta do módulo Extrair N-gramas.

Como uma abordagem alternativa ao uso de Extrair Recursos de N-grama, você pode usar o módulo Hash de Recursos. No entanto, observe que o [Hash de Recursos](https://msdn.microsoft.com/library/azure/dn906018.aspx) não traz funcionalidades de seleção de recursos internos nem a pesagem TF*IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Etapa 3: Treinar o modelo de classificação ou regressão
Agora o texto foi transformado em colunas de recurso numérico. O conjunto de dados ainda contém colunas de cadeia de caracteres de estágios anteriores e, portanto, usamos Selecionar Colunas no Conjunto de Dados para excluí-las.

Em seguida, usamos a [Regressão Logística de Duas Classes](https://msdn.microsoft.com/library/azure/dn905994.aspx) para prever nosso destino: pontuação de crítica alta ou baixa. Neste ponto, o problema de análise de texto foi transformado em um problema de classificação regular. Você pode usar as ferramentas disponíveis no Azure Machine Learning Studio para melhorar o modelo. Por exemplo, você pode experimentar com diferentes classificadores para descobrir quão precisos são os resultados fornecidos ou usar o ajuste de hiperparâmetro para melhorar a precisão.

![Treinar e pontuar](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Etapa 4: Pontuar e validar o modelo
Como você validará o modelo treinado? Pontuamos o modelo em relação ao conjunto de dados de teste e avaliamos a precisão. No entanto, o modelo aprendeu o vocabulário de N-gramas e seus pesos do conjunto de dados de treinamento. Por isso, devemos usar esse vocabulário e os pesos ao extrair recursos dos dados de teste, em vez de criar o vocabulário novamente. Portanto, adicionamos o módulo Extrair Recursos de N-grama à ramificação de pontuação do experimento, conectamos o vocabulário de saída da ramificação de treinamento e definimos o modo de vocabulário como somente leitura. Também desabilitamos a filtragem de N-gramas por frequência, definindo o mínimo como 1 instância e o máximo como 100%, bem como desativamos a seleção de recursos.

Depois que a coluna de texto nos dados de teste foi transformada em colunas de recurso numérico, excluímos as colunas de cadeia de caracteres de estágios anteriores como fizemos na ramificação de treinamento. Em seguida, usamos o módulo Pontuar Modelo para fazer previsões e o módulo Avaliar Modelo para avaliar a precisão.

## <a name="step-5-deploy-the-model-to-production"></a>Etapa 5: Implantar o modelo na produção
O modelo está quase pronto para ser implantado na produção. Quando implantado como um serviço Web, ele usa a cadeia de caracteres de texto de forma livre como entrada e retorna uma previsão “alta” ou “baixa”. Ele usa o vocabulário de N-gram aprendido para transformar o texto em recursos e o modelo de regressão logística treinado para fazer uma previsão desses recursos. 

Para configurar o experimento preditivo, primeiro salvamos o vocabulário de N-grama como conjunto de dados e o modelo de regressão logística treinado da ramificação de treinamento do experimento. Em seguida, salvamos o experimento usando “Salvar Como” para criar um grafo de experimento para o experimento preditivo. Removemos o módulo Dividir Dados e a ramificação de treinamento do experimento. Em seguida, conectamos o vocabulário de N-grama salvo e o modelo anteriormente aos módulos Extrair Recursos de N-grama e Pontuar Modelo, respectivamente. Também removemos o módulo Avaliar Modelo.

Inserimos o módulo Selecionar Colunas no Conjunto de Dados antes do módulo Pré-processar Texto para remover a coluna de rótulo e desmarcamos a opção “Acrescentar a coluna de pontuação ao conjunto de dados” em Pontuar Módulo. Dessa forma, o serviço Web não solicita o rótulo que está tentando prever e não retorna os recursos de entrada em resposta.

![Experimento preditivo](./media/text-analytics-module-tutorial/predictive-text.png)

Agora temos um experimento que pode ser publicado como um serviço Web e chamado com APIs de execução em lotes ou solicitação-resposta.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os módulos de análise de texto na [documentação do MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

