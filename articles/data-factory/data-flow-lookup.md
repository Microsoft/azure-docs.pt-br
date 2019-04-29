---
title: Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef473ea5f88b9108894787785fe1e9083fab1b0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347923"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use a pesquisa para adicionar dados de referência de outra fonte a seu fluxo de dados. A transformação de pesquisa requer a definição de uma fonte que aponte para a sua tabela de referência e apresente correspondência com os campos de chave.

![Transformação de pesquisa](media/data-flow/lookup1.png "Pesquisa")

Selecione os campos de chave que você deseja comparar entre os campos de fluxo de entrada e os campos da fonte de referência. Primeiro é preciso criar uma nova fonte na tela de design de fluxo de dados para ser usada como o lado direito da pesquisa.

Quando correspondências forem encontradas, as linhas e colunas resultantes da fonte de referência serão adicionadas ao seu fluxo de dados. Você pode escolher que campos de interesse deseja incluir em seu coletor no final do fluxo de dados.

## <a name="optimizations"></a>Otimizações

No Data Factory, execute os dados fluem em ambientes de Spark escalados horizontalmente. Se seu conjunto de dados pode se ajustar no espaço de memória do nó de trabalho, podemos otimizar o desempenho da pesquisa.

![Junção de difusão](media/data-flow/broadcast.png "junção de difusão")

### <a name="broadcast-join"></a>Junção de difusão

Selecione para a esquerda e/ou junção para solicitar o ADF para enviar por push o conjunto de dados inteiro de ambos os lados da relação de pesquisa na memória de difusão do lado direito.

### <a name="data-partitioning"></a>Particionamento de dados

Você também pode especificar o particionamento dos seus dados, selecionando "Particionamento do conjunto de" na guia Otimizar da transformação pesquisa para criar conjuntos de dados que podem se ajustar melhor na memória por trabalho.

## <a name="next-steps"></a>Próximas etapas

[Unir](data-flow-join.md) e [Exists](data-flow-exists.md) transformações executam tarefas semelhantes no ADF mapeando os dados fluem. Vejamos essas transformações Avançar.
