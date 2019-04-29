---
title: Transformação de junção do Fluxo de Dados do Azure Data Factory
description: Transformação de junção do Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 18f713198ef9aa45cb72a6718c0f7b086c019258
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348513"
---
# <a name="mapping-data-flow-join-transformation"></a>Transformação de associação de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use a junção para combinar dados de duas tabelas em seu Fluxo de Dados. Clique na transformação que será a relação esquerda e adicione uma transformação de junção da caixa de ferramentas. Dentro da transformação de junção, você selecionará outro fluxo de dados de seu fluxo de dados para ser relação direita.

![Transformação de junção](media/data-flow/join.png "Junção")

## <a name="join-types"></a>Tipos de junção

Selecionar tipo de junção é necessária para a transformação de junção.

### <a name="inner-join"></a>Junção interna

Junção interna será passado somente linhas que correspondem às condições de coluna das duas tabelas.

### <a name="left-outer"></a>Externa esquerda

Todas as linhas do fluxo esquerdo que não atenderem à condição de junção são passadas e as colunas de saída da outra tabela são definidas como NULL, além de todas as linhas retornadas pela junção interna.

### <a name="right-outer"></a>Externa direita

Todas as linhas do fluxo direito que não atenderem à condição de junção são passadas e as colunas de saída que correspondem à outra tabela são definidas como NULL, além de todas as linhas retornadas pela junção interna.

### <a name="full-outer"></a>Externa completa

Externa completa produz todas as colunas e linhas de ambos os lados com valores NULL para colunas que são não presente na outra tabela.

### <a name="cross-join"></a>União cruzada

Especifique o produto cruzado de dois fluxos com uma expressão. Você pode usar isso para criar condições de junção personalizadas.

## <a name="specify-join-conditions"></a>Especificar condições de junção

A condição Junção Esquerda é decorrente do fluxo de dados conectado à esquerda da sua junção. A condição Junção Direita é o segundo fluxo de dados conectado à sua junção na parte inferior, que será um conector direto para outro fluxo ou para uma referência a outro fluxo.

É necessário inserir pelo menos 1 (1..n) condições de junção. Elas podem ser campos referenciados diretamente, selecionados do menu suspenso, ou expressões.

## <a name="join-performance-optimizations"></a>Otimizações de desempenho de junção

Ao contrário da Junção de Mesclagem em ferramentas como o SSIS, a Junção no Fluxo de Dados do ADF não é uma operação de junção de mesclagem obrigatória. Portanto, as chaves de junção não precisam ser classificadas primeiro. A operação de junção ocorrerá no Spark usando o Databricks com base na operação de junção ideal no Spark: Junção de difusão/do lado do mapa:

![Otimização da transformação de junção](media/data-flow/joinoptimize.png "Otimização de junção")

Se o conjunto de dados puder se ajustar à memória do nó de trabalho do Databricks, poderemos otimizar o desempenho da junção. Também é possível especificar o particionamento dos dados na operação de junção para criar conjuntos de dados que podem se adequar melhor à memória por trabalho.

## <a name="self-join"></a>Autojunção

É possível obter as condições de autojunção no Fluxo de Dados do ADF usando a transformação de seleção para atribuir um alias a um fluxo existente. Primeiro, crie um “Novo branch” em um fluxo; em seguida, adicione um Selecionar para atribuir um alias ao fluxo original inteiro.

![Autojunção](media/data-flow/selfjoin.png "Autojunção")

No diagrama acima, a Transformação de seleção está na parte superior. Tudo o que ela está fazendo é atribuir um alias ao fluxo original como “OrigSourceBatting”. Na transformação de junção realçada embaixo dele, é possível ver que usamos esse fluxo de alias de Junção como a junção direita, permitindo-nos referenciar a mesma chave no lado esquerdo e no direito da junção interna.

## <a name="composite-and-custom-keys"></a>Chaves compostas e personalizadas

Você pode criar chaves compostas e personalizadas em tempo real dentro da transformação junção. Adicione linhas para colunas de junção adicional com o sinal de adição (+) ao lado de cada linha da relação. Ou um novo valor de chave no construtor de expressão para um valor de junção na hora de computação.

## <a name="next-steps"></a>Próximas etapas

Após a associação de dados, em seguida, você pode [criar novas colunas](data-flow-derived-column.md) e [seus dados do coletor para um armazenamento de dados de destino](data-flow-sink.md).
