---
title: Construtor de Expressões de Fluxo de Dados de Mapeamento do Azure Data Factory
description: O Construtor de Expressões para Fluxos de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: df9cfb0c0e36f54c8b1fbee4def552c78e9d42c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269076"
---
# <a name="mapping-data-flow-expression-builder"></a>Construtor de Expressões de Fluxo de Dados de Mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

No Fluxo de Dados de Mapeamento do Azure Data Factory, você encontrará caixas de expressões nas quais será possível inserir expressões para transformação de dados. Use colunas, campos, variáveis, parâmetros, funções do fluxo de dados nessas caixas. Para construir a expressão, use o Construtor de Expressões que é iniciado clicando na caixa de texto de expressões dentro da transformação. Às vezes, você também verá opções de "Coluna Computada" ao selecionar colunas para transformação. Ao clicar nessa opção, você também verá a inicialização do Construtor de Expressões.

![Construtor de Expressões](media/data-flow/expression.png "Construtor de Expressões")

A ferramenta Construtor de Expressões usa como padrão a opção do editor de texto. o recurso de preenchimento automático faz leitura de todo o modelo de objeto do Fluxo de Dados do Azure Data Factory com verificação de sintaxe e realce.

![Preenchimento automático do Construtor de Expressões](media/data-flow/expb1.png "Preenchimento automático do Construtor de Expressões")

## <a name="currently-working-on-field"></a>Campo Trabalhando no Momento

![Construtor de Expressões](media/data-flow/exp3.png "Trabalhando no Momento")

No canto superior esquerdo da interface do usuário do Construtor de Expressões, você verá um campo chamado "Trabalhando no Momento" com o nome do campo em que você está trabalhando atualmente. A expressão que você criar na interface do usuário será aplicada apenas ao campo de trabalho atual. Se você quiser transformar outro campo, salve o trabalho atual e use essa lista suspensa para selecionar outro campo e criar uma expressão para os outros campos.

## <a name="data-preview-in-debug-mode"></a>Visualização dos dados no modo de Depuração

![Construtor de Expressões](media/data-flow/exp4b.png "Visualização dos Dados de Expressão")

Quando estiver trabalhando nas expressões, opcionalmente você poderá alternar no modo de Depuração da superfície de design do Fluxo de Dados do Azure Data Factory, permitindo visualização dinâmica em andamento dos resultados dos dados da expressão que você está criando. A depuração dinâmica em tempo real está habilitada para as expressões.

![Modo de Depuração](media/data-flow/debugbutton.png "Botão Depurar")


![Construtor de Expressões](media/data-flow/exp5.png "Visualização dos Dados de Expressão")

## <a name="comments"></a>Comentários

Adicione comentários às expressões usando sintaxe de comentário de linha única e multilinhas:

![Comentários](media/data-flow/comments.png "Comentários")

## <a name="regular-expressions"></a>Expressões regulares

A linguagem de expressão do Fluxo de Dados do Azure Data Factory, [documentação de referência completa aqui](https://aka.ms/dataflowexpressions), habilita funções que incluem sintaxe de expressão regular. Ao usar funções de expressão regular, o construtor de expressões tentará interpretar a barra invertida (\\) como uma sequência de caracteres de escape. Ao usar barras invertidas em sua expressão regular, coloque todo regex em tiques (\`) ou use uma barra invertida dupla.

Exemplo usando tiques

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

ou usando barra dupla

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Endereçamento de índices de matriz

Com funções de expressão que retornam matrizes, use colchetes [] para endereçar índices específicos dentro desse objeto de matriz de retorno. A matriz baseia-se neles.

![Matriz do Construtor de Expressões](media/data-flow/expb2.png "Visualização dos Dados de Expressão")

## <a name="handling-names-with-special-characters"></a>Tratamento de nomes com caracteres especiais

Quando você tiver nomes de coluna que incluem espaços ou caracteres especiais, coloque o nome entre chaves.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Próximas etapas

[Comece a criar expressões de transformação de dados](data-flow-expression-functions.md)
