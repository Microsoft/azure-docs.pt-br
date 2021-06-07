---
title: 'Selecionar colunas de transformação: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo selecionar colunas de transformação no designer de Azure Machine Learning para executar uma transformação SELECT.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f03840e55366d7f105ca4b57bd60061c82833e72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420709"
---
# <a name="select-columns-transform"></a>Selecionar colunas para transformação

Este artigo descreve como usar o módulo selecionar colunas de transformação no designer de Azure Machine Learning. A finalidade do módulo selecionar colunas de transformação é garantir que um conjunto de colunas consistente e previsível seja usado em operações de aprendizado de máquina downstream.

Esse módulo é útil para tarefas como pontuação, que exigem colunas específicas. As alterações nas colunas disponíveis podem interromper o pipeline ou alterar os resultados.

Use a transformação selecionar colunas para criar e salvar um conjunto de colunas. Em seguida, use o módulo [aplicar transformação](apply-transformation.md) para aplicar essas seleções a novos dados.

## <a name="how-to-use-select-columns-transform"></a>Como usar a transformação selecionar colunas

Esse cenário pressupõe que você deseja usar a seleção de recursos para gerar um conjunto dinâmico de colunas que serão usadas para treinar um modelo. Para garantir que as seleções de coluna sejam as mesmas para o processo de pontuação, use o módulo selecionar colunas de transformação para capturar as seleções de coluna e aplicá-las em outro lugar no pipeline.

1. Adicione um conjunto de dados de entrada ao seu pipeline no designer.

2. Adicione uma instância da [seleção de recursos baseada em filtro](filter-based-feature-selection.md).

3. Conecte os módulos e configure o módulo seleção de recursos para localizar automaticamente uma série de melhores recursos no conjunto de dados de entrada.

4. Adicione uma instância do [modelo de treinamento](train-model.md) e use a saída da [seleção de recursos baseada em filtro](filter-based-feature-selection.md) como a entrada para treinamento.

    > [!IMPORTANT]
    > Como a importância do recurso é baseada nos valores na coluna, você não pode saber com antecedência quais colunas podem estar disponíveis para entrada para [treinar o modelo](train-model.md).  
5. Anexe uma instância do módulo selecionar colunas de transformação. 

    Esta etapa gera uma seleção de coluna como uma transformação que pode ser salva ou aplicada a outros conjuntos de os. Essa etapa garante que as colunas identificadas na seleção de recursos sejam salvas para que outros módulos reutilizem.

6. Adicione o módulo [modelo de Pontuação](score-model.md) . 

   *Não conecte o conjunto de dados de entrada.* Em vez disso, adicione o módulo [aplicar transformação](apply-transformation.md) e conecte a saída da transformação seleção de recursos.

   A estrutura do pipeline deve ser semelhante à seguinte:

   > [!div class="mx-imgBorder"]
   > ![Amostra do pipeline](media/module/filter-based-feature-selection-score.png)

   > [!IMPORTANT]
   > Você não pode esperar a aplicação da [seleção de recursos baseada em filtro](filter-based-feature-selection.md) ao conjunto de texto de Pontuação e obter os mesmos resultados. Como a seleção de recursos se baseia em valores, ela pode escolher um conjunto diferente de colunas, o que causaria uma falha na operação de pontuação.
    
7. Envie o pipeline.

Esse processo de salvar e aplicar uma seleção de coluna garante que o mesmo esquema de dados esteja disponível para treinamento e pontuação.


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
