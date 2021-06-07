---
title: 'Atribuir dados ao cluster: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo atribuir dados ao cluster em Azure Machine Learning para pontuar o modelo de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: e618395a2a4a11b4afb311d612cf5d0e27503dc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898857"
---
# <a name="module-assign-data-to-clusters"></a>Módulo: atribuir dados a clusters

Este artigo descreve como usar o módulo *atribuir dados a clusters* no designer de Azure Machine Learning. O módulo gera previsões por meio de um modelo de clustering que foi treinado com o algoritmo de *clustering K-* means.

O módulo atribuir dados a clusters retorna um DataSet que contém as atribuições prováveis para cada novo ponto de dados. 

## <a name="how-to-use-assign-data-to-clusters"></a>Como usar atribuir dados a clusters
  
1. No Azure Machine Learning designer, localize um modelo de clustering treinado anteriormente. Você pode criar e treinar um modelo de clustering usando um dos seguintes métodos:  
  
    - Configure o algoritmo de clustering K-means usando o módulo de [clustering k-](k-means-clustering.md) means e treine o modelo usando um conjunto de um DataSet e o módulo treinar modelo de clustering (este artigo).  
  
    - Você também pode adicionar um modelo de clustering treinado existente do grupo **modelos salvos** em seu espaço de trabalho.

2. Anexe o modelo treinado à porta de entrada à esquerda de **atribuir dados a clusters**.  

3. Anexe um novo conjunto de dados como entrada. 

   Nesse conjunto de DataSet, os rótulos são opcionais. Em geral, o clustering é um método de aprendizado não supervisionado. Você não deve conhecer as categorias com antecedência. No entanto, as colunas de entrada devem ser iguais às colunas usadas no treinamento do modelo de clustering ou ocorre um erro.

    > [!TIP]
    > Para reduzir o número de colunas que são gravadas no designer de previsões de cluster, use [selecionar colunas no conjunto](select-columns-in-dataset.md)de linhas e selecione um subconjunto das colunas. 
    
4. Deixe a caixa de seleção **Marcar para acrescentar ou desmarcar para resultado somente** marcada se desejar que os resultados contenham o conjunto de dados de entrada completo, incluindo uma coluna que exibe os resultados (atribuições de cluster).
  
    Se você desmarcar essa caixa de seleção, somente os resultados serão retornados. Essa opção pode ser útil quando você cria previsões como parte de um serviço Web.
  
5.  Enviar o pipeline.  
  
### <a name="results"></a>Resultados

+  Para exibir os valores no conjunto de um, clique com o botão direito do mouse no módulo e selecione **Visualizar**. Ou selecione o módulo e alterne para a guia **saídas** no painel direito, clique no ícone de histograma nas saídas de **porta** para visualizar o resultado.

