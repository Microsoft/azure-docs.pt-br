---
title: Resumir dados
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo resumir dados em Azure Machine Learning para gerar um relatório de estatísticas descritivas básico para as colunas em um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 5206565b85d1551e5e551f1dfe75d28c93bc53f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898219"
---
# <a name="summarize-data"></a>Resumir dados

Este artigo descreve um módulo do designer de Azure Machine Learning.

Use o módulo resumir dados para criar um conjunto de medidas estatísticas padrão que descrevem cada coluna na tabela de entrada.

As estatísticas de resumo são úteis quando você deseja entender as características do conjunto de todos. Por exemplo, talvez seja necessário saber:

- Quantos valores ausentes existem em cada coluna?
- Quantos valores exclusivos existem em uma coluna de recurso?
- Qual é a média e o desvio padrão de cada coluna?

O módulo calcula as pontuações importantes para cada coluna e retorna uma linha de estatísticas de resumo para cada variável (coluna de dados) fornecida como entrada.

## <a name="how-to-configure-summarize-data"></a>Como configurar dados de resumo  

1. Adicione o módulo **resumir dados** ao seu pipeline. Você pode encontrar esse módulo na categoria **funções estatísticas** no designer.

1. Conecte o conjunto de um para o qual você deseja gerar um relatório.

    Se você quiser relatar apenas algumas colunas, use o módulo [selecionar colunas no conjunto de DataSet](select-columns-in-dataset.md) para projetar um subconjunto de colunas com o qual trabalhar.

1. Nenhum parâmetro adicional é necessário. Por padrão, o módulo analisa todas as colunas fornecidas como entrada e, dependendo do tipo de valores nas colunas, gera um conjunto de estatísticas relevante, conforme descrito na seção [resultados](#results) .

1. Enviar o pipeline.

## <a name="results"></a>Resultados

O relatório do módulo pode incluir as estatísticas a seguir. 

|Nome da coluna|Descrição|
|------|------|  
|**Recurso**|Nome da coluna|
|**Count**|Contagem de todas as linhas|
|**Contagem de valor exclusivo**|Número de valores exclusivos na coluna|
|**Contagem de valor ausente**|Número de valores exclusivos na coluna|
|**Min**|Valor mais baixo na coluna|  
|**Max**|Valor mais alto na coluna|
|**Mean**|Média de todos os valores de coluna|
|**Desvio médio**|Desvio médio de valores de coluna|
|**Primeiro quartil**|Valor no primeiro quartil|
|**Cuja**|Valor da coluna mediana|
|**Terceiro quartil**|Valor no terceiro quartil|
|**Modo**|Modo de valores de coluna|
|**Intervalo**|Inteiro que representa o número de valores entre os valores máximo e mínimo|
|**Variação de amostra**|Variação para a coluna; consulte a observação|
|**Desvio padrão de exemplo**|Desvio padrão para a coluna; consulte a observação|
|**Distorção de exemplo**|Distorção para a coluna; consulte a observação|
|**Curtose de amostra**|Curtose da coluna; consulte a observação|
|**P 0,5**|Percentual de 0,5%|
|**P1**|1% percentil|
|**P5**|5% percentil|
|**P95**|95% percentil|
|**P 99,5**|99,5% percentil |

## <a name="technical-notes"></a>Observações técnicas

- Para colunas não numéricas, apenas os valores para Contagem, Contagem de valor exclusivo e Contagem de valor ausente são calculados. As outras estatísticas retornarão um valor nulo.

- As colunas que contêm valores Boolianos são processadas usando estas regras:

    - Ao calcular Mín, um AND lógico é aplicado.
    
    - Ao calcular Max, um OR lógico é aplicado
    
    - Ao calcular Intervalo, o módulo primeiro verifica se o número de valores exclusivos na coluna é igual a 2.
    
    - Ao calcular qualquer estatística que requeira cálculos de ponto flutuante, os valores True são tratados como 1,0 e os valores False são tratados como 0,0.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.  
